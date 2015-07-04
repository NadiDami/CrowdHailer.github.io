---
layout: post
title: A persistence problem
description: Solutions with entities, Repositories and ruby
date: 2015-07-23 17:20:06
tags: ruby design
author: Peter Saxton
---

### The worst advice I have followed

> Fat Model, Skinny controller

This advice is only good advice in that a fat model is slightly preferable to a fat controller. When I started creating projects with Rails I found that I would have a single model for a single database table. As OOP is about packaging behaviour with data as a database table grows then a data in the model grows and the bahviour on the model grows. Quite soon you end up with a fat model and shorty afterwards a God Object. God objects are objects that seam to know and act upon all parts of the system. They are an antipattern that are in violation of the Single responsibility principle.

Recently I have seen people blaiming Rails and particularly ActiveRecord(the library) for the encouraging god objects. ActiveRecord is a beast but also a fantastic piece of software. Most importantly it is a tool and doesn't encourage god object however it certainly does faciliate making that mistake if you are unaware. What does encourage god objects is simplistic advice like the fat model skiney controller.

So what is the solution? well I think that it is small, well defined, single responsibility objects everywhere.

### The persistence problem

Handling the data in a single database table, sounds like a single responsibility. That is a valid starting point but with anything beyond a trivial application it is too simplistic. To break it down further lets look at some of the key functionality we need when handling with a database table

- Formatting. The database will not understand our domain objects, so we need to transform them too and from a format that the database does understand. We should do this as near the database as possible so that the database has the smallest possible influence on the rest of our code.

- Querying. We will want to be able to fetch the correct items from out database. This may be simple or it may involve filtering on many fields, such as find all the declined orders for this month for customers who signed up more than a year ago.

- Buisness Logic. What you want to acomplish with your data, this is specific to your problem an as such there is no limit to how complex this might be.

Well if persistence is to big an item to tackle on its own then lets break it down a bit.

- Data, The data is stored somehow it needs to be in a format that the data store mechanism (database) understands.

- Retrieval, This is sometimes a simple concern and sometimes not, it was questions like 'can we see all customers who brough an item in the last 2 months with suppliers who are based in english speaking countries' and 'can we see all orders under £20 that included one of our featured items' That was the nudge to delve into the world of design patterns

- behaviour, Classic example a user has a first name and last name. This is data. A user has a full name which is the first name plus the last name this is not data.



### Other
Lets start with the punchline first. I will introduce an organisation of three objects that I use to handle projects of a given complexity. They are the Repository, The Entity, and the Record. These are all lean items. New behaviour should not just be added to the entity, there shall be no cry of lean 'x' fat entity. It is lean everything. If any of the above items is getting to large it is a sign that there it is time for another division along business lines. I have found quite a nice way to do this. This belongs at the end

```rb
# {% highlight ruby %}
class User
  def name
    "#{record.first_name} #{record.last_name}"
  end

  def credentials
    # all entities are stateless, we can memoise this call because as long as they share the same record they are always in sinc
    @credentials ||= Credentials.new record
  end
end

class Users
  # Repository of Users
end

class Credential
  def login(submitted_password)
    raise 'Not Authenticated' unless password == submitted_password
    last_login_at = DateTime.now
    record.save
  end
end

class AccountRecord
  attr_reader :id, :first_name, :last_name, :password, :last_login_at
  def email
    Email.new email_column_value
  end

  def email=(email)
    self.email_column_value = email.to_s
  end
end
# {% endhighlight %}
```

### Record
I like the active record pattern and I do not dislike the ActiveRecord library(ok thats a lie I think it is over engineered and just full of temptation for doing the wrong thing.)

http://mozaicworks.com/blog/5-steps-to-mistake-proof-software-design/
