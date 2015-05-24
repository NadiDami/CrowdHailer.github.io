---
layout: post
title: A persistance problem
description: Solutions with entities, Repositories and ruby
date: 2015-04-23 17:20:05
tags: ruby design
author: Peter Saxton
---

ActiveRecord is a beast. There are many other people who have expanded on this point. ActiveRecord is a fantastic Piece of software. THere are many people who would agree with this. Persistance of data seams like a single responsibility and this idea is often seen as equivalent to the domain. Rails and active model compounds this view in a newbie. I am sure of that however rails at no point precludes you from seeing this disctiction and moving on to a much happier way of working.

 Well if persistance is to big an item to tackle on its own then lets break it down a bit.

- Data, The data is stored somehow it needs to be in a format that the data store mechanism (database) understands.

- Retrieval, This is sometimes a simple concern and sometimes not, it was questions like 'can we see all customers who brough an item in the last 2 months with suppliers who are based in english speaking countries' and 'can we see all orders under £20 that included one of our featured items' That was the nudge to delve into the world of design patterns

- behaviour, Classic example a user has a first name and last name. This is data. A user has a full name which is the first name plus the last name this is not data.

- other

### Other
Lets start with the punchline first. I will introduce an organisation of three objects that I use to handle projects of a given complexity. They are the Repository, The Entity, and the Record. These are all lean items. New behaviour should not just be added to the entity, there shall be no cry of lean 'x' fat entity. It is lean everything. If any of the above items is getting to large it is a sign that there it is time for another division along business lines. I have found quite a nice way to do this. This belongs at the end

```rb
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
```

### Record
I like the active record pattern and I do not dislike the ActiveRecord library(ok thats a lie I think it is over engineered and just full of temptation for doing the wrong thing.)
