---
layout: post
title: "Simple Rails 3 Validator for Unchangeable Attributes"
date: 2012-03-23 09:23
comments: true
categories: [Rails]
---
Here is a simple Rails validator that you can use to ensure an attribute of a model cannot be changed once it has been assigned a value.

``` ruby
class UnchangeableValidator < ActiveModel::EachValidator
  def validate_each(object, attribute, value)
    if !object.new_record? && value.present?
      original = object.class.send(:where, "id = #{object.id}").select("id, #{attribute.to_s}").first
      if original.send(attribute) != value
        object.errors[attribute] << (options[:message] || "cannot be changed once assigned")
      end
    end
  end
end
```

You can use it this way:
``` ruby
validates :email, :unchangeable => true, :presence => true
```

Here are some specs to prove it works:

``` ruby
it "should not allow you to change an email for an existing record" do
  @user.email = "test@test.com"
  @user.save
  @user.errors[:email].first.should == "cannot be changed once assigned"
end
```