---
layout: post
title: "Transactions In Rails"
date: 2011-12-01 11:39
comments: true
categories: [Rails, Software Design]
---
Recently I was tasked to write tests for the transactions of an existing application. This gave me the opportunity to learn more about the codebase, while also improving the test coverage. Generally, most of the transaction code looked fine. However, there were some instances where transactions were used incorrectly or inefficiently. I assumed this is just because of a misunderstanding of how transactions work within Rails, and so I thought iʼd take some time and give an overview of the common errors I found and some best practices for using transactions in Rails.

Let me also state at the beginning that most of these examples are not my own, they come directly from the Rails source, which give example usages of applying transactions RTFM FTW. 

[http://api.rubyonrails.org/classes/ActiveRecord/Transactions/ClassMethods.html](http://api.rubyonrails.org/classes/ActiveRecord/Transactions/ClassMethods.html)

### Reasons for transactions

We use transactions as a protective wrapper around SQL statements to ensure changes to the database only occur when all actions succeed together. Transaction help the developer enforce data integrity within the application. The canonical example of transactions is the banking method where funds are withdrawn from one account and deposited into the next. If either of these steps fail, then the entire process should be reset. This example can be described in code this way:

``` ruby
ActiveRecord::Base.transaction do
  david.withdrawal(100)
  mary.deposit(100)
end
```

In Rails transactions are available as class and instance methods for all ActiveRecord models. This means either of these approaches are equally valid:

``` ruby
Client.transaction do
  @client.users.create!
  @user.clients(true).first.destroy!
  Product.first.destroy!
end

@client.transaction do
  @client.users.create!
  @user.clients(true).first.destroy!
  Product.first.destroy!
end
```

You may also notice that there are several different model classes referenced in these transactions. It is perfectly fine to mix model types inside a single transaction block. This is because the transaction is bound to the database connection not the model instance. 
As a rule, transactions are only needed when changes to multiple records must succeed as a single unit. Additionally, Rails already wraps the #save and #destroy methods in a transaction, therefore a transaction is never needed when updating a single record.


### Transaction Rollback Triggers

Transactions reset the state of records through a process called a rollback. In Rails, rollbacks are only triggered by an exception. This is a crucial point to understand; I saw several transaction blocks that would never rollback because the containing code could not throw an exception. Here I have slightly modified our banking example from before:

``` ruby
ActiveRecord::Base.transaction do
  david.update_attribute(:amount, david.amount -100)
  mary.update_attribute(:amount, 100)
end
```

In Rails _#update_attribute_ is designed not to throw an exception when an update fails. It returns false, and for this reason you should ensure that the methods used throw an exception upon failure. A better way to write the previous example would be:

``` ruby
ActiveRecord::Base.transaction do
  david.update_attributes!(:amount => -100)
  mary.update_attributes!(:amount => 100)
end
```

Note: The bang modifier (!) is a rails convention for a method which will throw an exception upon failure.

I also saw examples in the code where records were found inside of transactions using magic finders _#find_by_<some attribute>_. By design magic finders will return nil when no record is returned. This is in contrast to the normal _#find_ method which throws an __ActiveRecord::RecordNotFound__ exception. Consider this example:

``` ruby
ActiveRecord::Base.transaction do
  david = User.find_by_name("david")
  if(david.id != john.id)
    john.update_attributes!(:amount => -100) 
    mary.update_attributes!(:amount => 100)
  end
end
```

Do you see the logic error? The nil object will have an id attribute and it will mask the fact that the desired record was not returned. While this doesn’t cause an error in the transaction it will still lead to a loss of data integrity, because the application is updating where it shouldn’t. Remember a transaction defines a block of code which must succeed as an atomic unit. This means we should raise an exception when a logical dependency like this is not met. Here is how the code should be written:

``` ruby
ActiveRecord::Base.transaction do
  david = User.find_by_name("david")
  raise ActiveRecord::RecordNotFound if david.nil?
  if(david.id != john.id)
    john.update_attributes!(:amount => -100) 
    mary.update_attributes!(:amount => 100)
  end
end
```

When the exception occurs it will be raised outside of the transaction block after the rollback has completed. Your application code must be ready to catch this exception as it bubbles up through the application stack. 

It is also possible to invalidate a transaction without raising an exception that will propagate upwards by using __ActiveRecord::Rollback__. This special exception allows you to invalidate a transaction and reset the database records without needing to rescue elsewhere in your code.


### When To Use Nested Transactions

One of the common mistakes I saw in the codebase is the misuse or overuse of nested transactions. When you nest a transaction inside another transaction this has the effect of making the child transaction a part of the parent. This can have surprising results, take this example from the Rails API documentation:

``` ruby
User.transaction do
  User.create(:username => 'Kotori')
  User.transaction do
    User.create(:username => 'Nemu')
    raise ActiveRecord::Rollback
  end
end
```

As mentioned previously __ActiveRecord::Rollback__ does not propagate outside of the containing transaction block and so the parent transaction does not receive the exception nested inside the child. Since the contents of the child transaction are lumped into the parent transaction both records are created! I find it easier to think of nested transactions like the child who dumps its contents into the parent container, leaving the child transaction empty.

To ensure a rollback is received by the parent transaction you must add the _:requires_new => true_. option to the child transaction. Again using the example from Rails source you would trigger a parent’s Rollback like this:

``` ruby
User.transaction do
  User.create(:username => 'Kotori')
  User.transaction(:requires_new => true) do
    User.create(:username => 'Nemu')
    raise ActiveRecord::Rollback
  end
end
```

A transaction only acts upon the current database connection. If your application writes to multiple databases at once you will need to wrap the method inside a nested transaction. For example:

``` ruby
Client.transaction do
  Product.transaction do
    product.buy(@quantity)
    client.update_attributes!(:sales_count => @sales_count + 1)
  end
end
```

### Callback around Transactions
As mentioned previously _#save_ and _#destroy_ are wrapped inside a transaction. This means that callbacks like _#after_save_ is still part of the active transaction which might still be rolled back! Therefore if you want code to execute that is guaranteed to execute outside of the transaction use one of the transaction specific callbacks like _#after_commit_ or _#after_rollback_.  


### Transaction Gotchas
Do not catch an __ActiveRecord::RecordInvalid__ exception inside a transaction because the this exception invalidates the transaction on some databases like Postgres. Once the transaction has been invalidated you must restart it from the beginning for it to work correctly.

When testing rollbacks or transaction related callbacks that happen after rollback you will want to turn off transactional_fixtures which are on by default in most test frameworks.

### Common Anti-patterns To Avoid
1. Using a transaction when only a single record is updated
2. Needlessly nesting transactions
3. Transactions that contain code, which won’t cause a rollback
4. Use of transactions in a controller