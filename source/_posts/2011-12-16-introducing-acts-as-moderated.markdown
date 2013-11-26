---
layout: post
title: "Introducing ActsAsModerated"
date: 2011-12-16 09:06
comments: true
categories: [Rails, Plugins]
---

Periodically sites that incorporate user generated content need a way to moderate the incoming stream before publishing it to the site. Typically this is accomplished by putting the content in a queue and allowing moderators to explicitly accept or reject content. I needed such functionality for a site I was working on so I wrote the "ActsAsModerated" plugin which allows specific columns of a model to be audited by a moderator at some later point.

ActsAsModerated is good for:

* spot-checking user generated content
* being notified when new content is created
* tracking changes within a record
* spam checking

Moreover, this plugin allows for custom callbacks and validations around the moderation event. This flexibility gives developers the ability to augment the moderation flow by stacking custom rules as needed. For example, a developer write code to assign a reputation score to a user and increment that score for every non-spam contribution they make. This would mean that you could auto approve any content that is made by a user above a certain score, thereby reducing the workload on moderators. I have been using this plugin for over a year on a very high-volume site and it has held up quite well, and so I thought I'd share it with the rest of the Ruby on Rails community.

### Download & Install
For those with ADD you can find acts_as_moderated here:
[https://github.com/heavysixer/acts_as_moderated](https://github.com/heavysixer/acts_as_moderated)

### Setup

Create the moderated_records table like so:

``` ruby
    create_table "moderation_records", :force => true do |t|
      t.integer  "recordable_id"
      t.string   "recordable_type"
      t.integer  "state_id",             :default => 0
      t.integer  "decision_id",          :default => 0
      t.boolean  "flagged",              :default => false
      t.integer  "moderator_id"
      t.string   "reason"
      t.datetime "created_at"
      t.datetime "updated_at"
      t.text     "inspected_attributes"
      t.boolean  "rejected",             :default => false
    end

    add_index "moderation_records", ["decision_id"], :name => "index_moderation_records_on_decision_id"
    add_index "moderation_records", ["flagged"], :name => "index_moderation_records_on_flagged"
    add_index "moderation_records", ["moderator_id"], :name => "index_moderation_records_on_moderator_id"
    add_index "moderation_records", ["recordable_id", "recordable_type"], :name => "index_moderation_records_on_recordable_id_and_recordable_type"
    add_index "moderation_records", ["rejected"], :name => "index_moderation_records_on_rejected"
    add_index "moderation_records", ["state_id"], :name => "index_moderation_records_on_state_id"
```

### Usage

ActsAsModerated has two integration points. The first is within the model(s) to be moderated, which can be done like so:

``` ruby
    class Comment < ActiveRecord::Base
      acts_as_moderated :body
    end
```

The second integration point is the class that acts as the moderator. Typically this is some user or account class. The idea is that behind this integration point is to create an audit trail for decisions made by the moderator if you ever need to watch the watcher.

``` ruby
  class Account < ActiveRecord::Base
    acts_as_moderator
  end
```

The plugin also supports an __after_moderation__ callback on the record being moderated, which you can use to take action based on what the moderator did. For example:

* Delete the record if it is inappropriate or spam
* Email the content creator that their content has been approved / denied

There are several dynamically created methods added to every acts_as_moderated class which moderators can use as a shortcut for making decisions. For example where __@moderator__ is an object with __acts_as_moderator__ applied: 

``` ruby
  @comment.marked_spam_by_moderator(@moderator)
  @comment.marked_scam_by_moderator(@moderator, :reason => 'this is obviously a scam, please delete.')
```

### Default Ordering, Callbacks & Flagging

The ModerationRecord class has a __named_scope__ called *queue*, which will return records sorted oldest to newest. However, if you flag a record it will be returned first regardless of its age relative to unflagged records. This is useful if you want to ensure that moderators see potentially dangerous records first. A good way to flag a record is using the __after_moderated__ callback for example:

``` ruby
    class Comment < ActiveRecord::Base
      acts_as_moderated :body

      def after_moderated(moderation_record)
        moderation_record.flag! if body =~ /viagra/i
      end
    end
```

The moderation record will attempt to make callbacks on the model being moderated after a record is first created __after_moderation__ and when a moderator rejects a record __after_rejection__ here is an example of what they might do:

``` ruby
    class Story < ActiveRecord::Base
      acts_as_moderated :body

      def after_moderated(moderation_record)
        update_attribute(:moderated, true)
      end

      def after_rejection(moderation_record)
        update_attribute(:rejected, true)
      end
    end
```

### Skipping moderation

The moderation plugin adds an __attr_accessor__ called __skip_moderation__, which when set to true will prevent a moderation
record from being created for that instance of a save. This is useful if you need to create records programmatically, which
don't need to be moderated initially but will need to be moderated at some later point. For example:

``` ruby
  @story = ModeratedStory.create #creates a moderation record
  @story = ModeratedStory.create(:skip_moderation => true) #does not create a moderation record
```

A word to the wise however, since this __attr_accessor__ prevents records from being moderated you will want to protect it from
*mass_assignment* in your model.

#### Known Bugs

Presently if you use the __:always_moderate__ flag on a STI model it will produce a never-ending series of record updates.
I'll keep working on this bug, in the meantime please do investigate!
