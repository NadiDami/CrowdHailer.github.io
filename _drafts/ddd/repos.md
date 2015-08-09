---
layout: post
title: Solving a persistence problem
description: Simple repository pattern for Ruby
date: 2015-08-05 17:20:06
tags: ruby design
author: Peter Saxton
---

Part 5 in [Domain Drive Design series](/2015/07/14/domain-driven-design-introduction.html) following on from [Entities and Records]().

### The Domain Model is not just model objects

I have asserted that for any non-trivial program the modeling of the problem space should not be handled by individual model objects. Instead it should be handled through the coordinated action of many domain objects. In the last post we introduced entities and records to handle some of the concerns of modeling a system. In this post we discuss the repository pattern as a solution to our need to store and recover those entities and records.

I would recommend reading the post on [Entities and Records]() first. Don't want to? Well, in summary, records are dumb data stores where the state of the system resides. Entities are stateless wrappers for records that hold behaviour that requires some of the records contents. We do not need to know of the existence of the records and should interact with them only through the entities that wrap them.

### The Problem of Persistence
The database is a small concern in a system. It is easy to forget this when developing in frameworks where you have to choose the database up front and where the methods on a model are exact reflections of your database schema. The littering of database concerns across the codebase is a distraction from writing the code most suited to your domain. For this reason we treat the database as a detail and want it isolated from the core entities.

We want to get to a stage where we have record objects that we do not interact with directly and entities that have no knowledge of the database. This is excellent — we have achieved separating knowledge of the database and the main behaviour of the application.

Now we need a way to persist the records. This task falls to a repository. The repository will be the only thing that knows the storage mechanism supporting the application.

### Using a Repository

Here is a quick one sentence summary of the repository pattern by Martin Fowler:

> A Repository mediates between the domain and data mapping layers, acting like an in-memory domain object collection.

The key feature of our repository is that it should act as a collection of entities. It does not need to look like a database and semantics of SQL or any other query language should not be evident in the API it exposes. The only difference between a repository and typical in-memory collection is that it will probably have more sophisticated set of query methods than a normal collection.

Let's design how we would like a repository to be used. We will want simple queries such as `first` and `last`. Retrieving by an id value is like a key lookup and so a `[]` method is useful; or even better, a `fetch` method. Then there will be queries that are specific to the collection; for example, finding all users by a family name. 

**Important**: Always design your API with pagination in mind unless you are sure it is unnecessary. This is because adding it later can be trouble.

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
Once we know how we would like to use our repository, we can set about implementing it. There are several ways to go about this but I always try to go for the simplest. 

There are some very capable Object Relational Mappers available, the best being [Sequel](). I don't want to reinvent handling SQL so I build on top of the Sequel library. The following implementation will realise the behavior above:

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

### Design Overkill
This example repository does everything that is required of it. Despite that it fails on being elegant code. For example, the line `entity.record.save` goes against the good advice of [The Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter).It is also implicity dependent on the `User::Record` and `User` classes. 

There are ways to get fix some of these design complaints. If you would like to see some of these solutions have a look at [Lotus Model]() or [Ruby Object Mapper (ROM)](). However, in all the projects where I have employed a repository I have not gone much further than the code shown in the example above. As this series is all about well designed code and the benefits that can be got from doing the right thing, the question is: why do I stop here?

To make a generic repository that can be backed by a sequel backend or an in-memory backend is complex. It requires adapters and an in-memory implementation of storage. If queries are sophisticated then the in memory-adapter needs to support a similar set of queries.

Creating a generic query interface when I might never use it is overkill and so I choose to stick with the bad code. The saving grace with this dirty code is that it is contained within the repository.

Another reason to create a generic adapter to the database is to allow testing without the database in place. As shown in the previous post I can test entities without a database by replacing the record objects with simple `OpenStructs`. These tests are slower yet the number of tests are small because the repository is focused in handling only queries on the collection.

In reality I have found this level of sophistication in the repository a good compromise between isolated code and building adapters that might never be used. As ROM or the Lotus model mature I might find myself using these and getting the isolation I have currently traded away.

*Have you tried using the repository pattern or perhaps employ the datamapper pattern? Let me know in the comments below.*

The next post will be the last in the series and will introduce interactors. Then we can finally mate our domain model with the framework of choice and make our program available to the world.

Further reading:

[Domain Logic in Rails](http://smashingboxes.com/ideas/domain-logic-in-rails)

[Why is your Rails application still coupled to ActiveRecord?](https://medium.com/@KamilLelonek/why-is-your-rails-application-still-coupled-to-activerecord-efe34d657c91)

[Persistence with Repository and Query Patterns](http://hawkins.io/2014/01/pesistence_with_repository_and_query_patterns/)
