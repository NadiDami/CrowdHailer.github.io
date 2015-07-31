---
layout: post
title: Application border control with form objects
description: Using form objects to preserve your domain language against invalid input.
date: 2015-08-05 17:20:06
tags: ruby design
author: Peter Saxton
---

Part 5 in [Domain Drive Design series](/2015/07/14/domain-driven-design-introduction.html) following on from [Entities and Records]().

### The Domain model is not just Model objects

I have asserted that for any non-trivial program the modeling of the problem space should not be handled by individual model objects. It will be a rich interaction from the coordination of many objects. Not all of these objects will be easily classified and they certainly will not need to be derived from framework specific superclasses. Some common patterns will emerge. In the last post we introduced entities and records to handle some of the concerns of modeling a system. I would recommend reading that post first.

Don't want to? Well in summary records are dumb data stores where the state of the system resides. Entities are stateless wrappers for records that hold behavior that requires some of the records contents. We do not need to know of the existence of the records and should change their entries only through methods on entities.

### The problem of persistence

At this point we have record objects that I do not interact with directly and entities that have no knowledge of the database. This is excellent we have separated the concern of knowing about the database from the main description of behavior.

The database is a small concern in a system. It is easy to forget this when developing in frameworks where you have to choose the database up front, where the design of your models methods are exact reflections of your database schema. The littering of database concerns across the codebase is a distraction from writing the code most suited to your needs.

If we want to persist the records we need a way to do this. This task falls to a repository, The repository will be the only thing that knows the storage mechanism supporting the application.

### Repository

One sentence summary of the repository pattern from Martin Fowler:

> A Repository mediates between the domain and data mapping layers, acting like an in-memory domain object collection.

When using our repository it should act as a collection of entities.
It wil probably have more sophisticated query methods than a normal collection

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
User::Repository << new_user
# => #<User::Repository>

# {% endhighlight %}
```

building the repository
value of ORM,
ode to Sequel

Overkill
Not found a need


http://smashingboxes.com/ideas/domain-logic-in-rails
https://medium.com/@KamilLelonek/why-is-your-rails-application-still-coupled-to-activerecord-efe34d657c91
