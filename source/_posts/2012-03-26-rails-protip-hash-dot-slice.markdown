---
layout: post
title: "Rails Protip: hash.slice"
date: 2012-03-26 11:09
comments: true
categories: [Rails, ProTip]
---

Rails has hidden gems just waiting to be discovered. I will demonstrate the use of _Hash.slice_, which is one of the core extensions of ActiveSupport.

Here is an example of how _Hash.slice_ can clean up a controller, take this existing code for example:
``` ruby
def index
  @users = User.paginate({ :page => params[:page].present? ? params[:page].to_i : 1, :per_page => params[:per_page].present? ? params[:per_page].to_i : 12 })
end
```

With _Hash.slice_ we can shorten it to:
``` ruby
def index
  @users = User.paginate({ :page => 1, :per_page => 12 }.merge(params.slice(:page, :per_page)))
end
```

_Hash.slice_ is that it is very forgiving. The method only returns the attributes if they exist. In our example we are assured all conditions will be met because the default values will only be overwritten if _Hash.slice_ returns them.