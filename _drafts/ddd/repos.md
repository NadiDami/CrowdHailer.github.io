---
layout: post
title: Solving a persistence problem
description: Simple repository pattern for Ruby
date: 2015-08-05 17:20:06
tags: ruby design
author: Peter Saxton
---

Part 5 in [Domain Drive Design series](/2015/07/14/domain-driven-design-introduction.html) following on from [Entities and Records]().

### The Domain model is not just Model objects

I have asserted that for any non-trivial program the modeling of the problem space should not be handled by individual model objects. Instead through the coordinated action of many domain objects. In the last post we introduced entities and records to handle some of the concerns of modeling a system. In this post we discuss repository as a solution to our need to store and recover those entities and records.

I would recommend reading the last post first. Don't want to? Well in summary records are dumb data stores where the state of the system resides. Entities are stateless wrappers for records that hold behavior that requires some of the records contents. We do not need to know of the existence of the records and should interact with them only through the entities that wrap them.

### The problem of persistence
The database is a small concern in a system. It is easy to forget this when developing in frameworks where you have to choose the database up front, where the methods on a model are exact reflections of your database schema. The littering of database concerns across the codebase is a distraction from writing the code most suited to your domain. For this reason we treat the database as a detail and want it robustly isolated from the core entities.

At this point we have record objects that I do not interact with directly and entities that have no knowledge of the database. This is excellent we have achieved separating  knowledge of the database and the main behavior.

If we want to persist the records we need a way to do this. This task falls to a repository, The repository will be the only thing that knows the storage mechanism supporting the application.

### Using a Repository

Here is a quick one sentence summary of the repository pattern from Martin Fowler:

> A Repository mediates between the domain and data mapping layers, acting like an in-memory domain object collection.

The key feature of our repository it should act as a collection of entities. It does not need to look like a database, semantics of SQL or other query language should not be evident in api it exposes. The only difference between a repository and typical in memory collection is that it will probably have more sophisticated set query methods than a normal collection.

Let's design how we would like a repository to be used. We will want simple queries such as `first` and `last`. Retrieving by an id value is like a key lookup and so a `[]` method is useful or even better a `fetch` method. Then there will be queries that are specific to the collection, finding all users by a family name is the example here. **Important** always design your api with pagination in mind unless you are sure it is unnecessary as adding it later can be trouble.

```rb
# {% highlight ruby %}

# Simple query
adam = User::Repository.first
# => #<User fullname="Adam Smith">

# Complex query, perhaps involving paginated results
johnes = User::Repository.family "johnes", page: 2
# => [#<User fullname="Emma Johnes">, #<User fullname="Kate Johnes">]

# Handling missing entries with useful null objects
user = User::Repository.fetch('ID_001') { GuestUser.new }
# => #<GuestUser>

# Building empty entities with correct record structures
new_user = User::Repository.build
# => #<User fullname="">

# Editing user
new_user.full_name = "Peter Saxton"
# => #<User fullname="Peter Saxton">

# Adding user to collection of users.
User::Repository.save new_user
# => #<User::Repository>

# {% endhighlight %}
```

### Building the Repository
Once we know how we would like to use our repository we can set about implementing it. There are several ways to go about this, I try to go for the simplest. There are some very capable Object Relational Mappers available, the best being [Sequel](). I don't want to reinvent handling SQL so I build on top of the sequel library. The following implementation will realise the behavior above.

```rb
# {% highlight ruby %}
class User::Repository
  class << self
    def build
      User.new record_class.new
    end

    def save(entity)
      entity.record.save
      self
    end

    def remove(entity)
      entity.record.destroy
      self
    rescue Sequel::NoExistingObject
      raise RecordAbsent
    end

    def first
      record = User::Record.first
      User.new record
    end

    def last_login
      record = User::Record
        .sort(:last_login_at)
        .first
      User.new record
    end

    def family(last_name, page: 1, page_size: 15)
      records = User::Record
        .where(:last_name => last_name)
        .sort(:first_name)
        .paginate(page, page_size)
      records.map User.new
    end
  end
end
# {% endhighlight %}
```

### Design overkill
This example repository does everything that is required of it. Despite that it fails on being elegant code in a few places. The line `entity.record.save` fails the good advice of Demeter. It is also implicity dependant on the `User Record` and `User`. There are ways to get fix some of those design complaints. If you would like to see how have a look at [Lotus Model]() or [Ruby Object Mapper (ROM)](). BUT in all the projects where I have employed a repository I have not gone much further than the code shown above. As this series is all about well designed code and the benefits that can be got from doing the right thing, so why do I stop here.

To make a generic repository that can be backed by a sequel backend or an in memory backend is complex. It requires adapters and an in memory implementation of storage. If queries are sophisticated then it seams like semantics of them needs to be reproduced at every level under the repository.

So i don't do it I stick with the bad code. However the dirty code that is rather closely tied to the semantics of sequel queries is contained it is in the repository class only. The repository cannot be tested without the database in place, BUT the repository is all about the database. These tests are slower BUT as the repository is limited to a single responsibility the number of tests are small.

If I want to change my storage mechanism I may need to write a brand new repository, this is going to be a bit of work. It wont be too much work tho that I feel it prevents me from changing the database. In reality the database will rarely change, knowing which battles to pick is part of a pragmatic approach to programming. As ROM and lotus model mature I might find myself using these and getting the separation I currently lack. However to build it myself, not a worthwhile use of time.


http://smashingboxes.com/ideas/domain-logic-in-rails
https://medium.com/@KamilLelonek/why-is-your-rails-application-still-coupled-to-activerecord-efe34d657c91
http://hawkins.io/2014/01/pesistence_with_repository_and_query_patterns/
