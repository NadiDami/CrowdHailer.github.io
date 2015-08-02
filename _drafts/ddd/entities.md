---
layout: post
title: Tackling God Objects in Ruby
description: Managing Ruby models with entities and records
date: 2015-07-23 17:20:06
tags: ruby design
author: Peter Saxton
---

### The worst advice I have followed

> Fat Model, Skinny controller

This advice is only good advice in that a fat model is slightly preferable to a fat controller. When creating projects with Rails I would have a single model for a single database table. So as a database table grows then data in the model representation grows. Object Oriented Programming (OOP) is about packaging behavior with data, as the data in the model increases the behavior on the model tends to follow this increase. Quite soon you end up with a fat model and shorty afterwards a God object. God objects are objects that seem to know and act upon all parts of the system. They are an antipattern that are in violation of the single responsibility principle.

Recently I have seen people blaming Rails and particularly ActiveRecord (the library) for encouraging God objects. ActiveRecord is a beast but also a fantastic piece of software. Most importantly it is a tool and doesn't encourage or discourage God objects. ActiveRecord does, however, facilitate making over sized objects if you are not disciplined. What does encourage creating God objects is simplistic advice such as 'fat model skinny controller'.

So what is good practise? Well I think that it is small, well defined, single responsibility objects everywhere.

### The persistence problem

Handling the data in a single database table, sounds like a single responsibility so why not a single object. That is a valid starting point but for anything beyond a trivial application it is too simplistic. To break apart our model let's look at some of the key functionality we need when handling a database table.

- Data serialization. The database will not understand our domain objects, so we need to transform them to and from a format that the database does understand. We should do this as near the database as possible so that the database has the smallest possible influence on the rest of our code.

- Querying. We will want to be able to fetch the correct items from our database. This may be simple or it may involve filtering on many fields, such as find all the declined orders for this month for customers who signed up more than a year ago.

- Business Logic. What you want to accomplish with your data, this is specific to your problem and as such there is no limit to how complex this part might be.

### Building a solution
First we want to separate querying, this functionality acts on the whole database table while the other two parts need to act of individual entries. To handle querying we will introduce repositories in the next post.

To split serialization and behavior I employ the decorator pattern. I have a core Record object that formats the data saved to it. Record objects are just dumb data buckets. Business logic is built into entities - each entity wraps a single record and has no state other that the record object it is decorating.

#### Records
These are the only objects anywhere in my system that know how data is stored in the database. We often don't think about databases as out of our control but we did not build them and so the API is not of our design. Therefore it most likely will not be the best representation of data in our domain. For this reason Record objects act as gatekeepers to information from the database in a way analagous to form objects normalising input from user input.

The only logic a Record object should have should be packing domain objects into the database and initializing domain objects from entries in the database.

Talking to a database is a solved problem and I use an ORM, either ActiveRecord or Sequel. The benefits of using an ORM library is that I can write Record very quickly. However both ORMs have many features that allow them to act as far more than a data bucket. It is only by being disciplined that the functionality of a record remains serialization only.

```rb
# {% highlight ruby %}
class UserRecord < Sequel::Model(:users)
end
# {% endhighlight %}
```

#### Entities

These objects encapsulate business logic and so have very little in common with each other, or across projects. The only consistent feature is that the are initialized with a record and that they cannot have any state on themselves. They must always update the record if something changes.

Multiple entity classes can be created for a single record class. These entities are separated by business concerns and there is no limit to how many can be made for a single record. Now the behavior can be built up of small manageable parts no matter how large our database table grows.

Other parts of the program should never need to interact with a record directly but only with an entity that is decorating it. Good DDD code should communicate in a language understood by the client. They will understand user but not 'user entity' or 'user record' for this reason the user is represented by the user entity and the user record is just an implementation detail

### Examples
A very typical example would be similar to the following. A user represents the information kept in the users database table. There is a good amount of meta information associated with the account such as authentication credentials.

Following good design principles we have dedicated value objects for email, password and name. This means we have moved validation away from our entities. Our record layer will happily dump and load these value objects to the database. However features continue to be added and our user needs a combined full name and also the ability to authenticate. These methods are not part of the same set of responsibility and so we look for a way to separate them.

Authentication is often a good candidate to be separated to a single purpose entity. In this example we have done just that and named the extracted class 'Credentials'.  

```rb
# {% highlight ruby %}
class UserRecord < Sequel::Model(:users)
  # Turns database friendly representations of values in to rich objects specific to our domain
  plugin :serialization

  serialize_attributes [Email.method(:dump), Email.method(:load)], :email
  serialize_attributes [Password.method(:dump), Password.method(:load)], :password
  serialize_attributes [Name.method(:dump), Name.method(:load)], :first_name, :last_name
end

class User
  # Enities make no sense if they don't also have a record to preserve state
  def initialize(record)
    @record = record
  end

  attr_reader :record
  private :record

  # Behaviour that the user can handle itself.
  def full_name
    "#{record.first_name} #{record.last_name}"
  end

  def full_name=(full_name)
    first_name, last_name = full_name.split ' '
    record.first_name = first_name
    record.last_name = last_name
  end

  # Behaviour that the user delegates to a dedicated credentials object
  def authenticate(submitted_password)
    credentials.authenticate submitted_password
  end

  # The credentials object also uses the same record
  def credentials
    @credentials ||= Credentials.new record
  end
end

class Credentials
  # Logic for authentication can grow and is cleanly separated from the main user object
  def authenticate(submitted_password)
    return nil unless correct_password? submitted_password
    record_login
    self
  end

  private

  def correct_password?(submitted_password)
    record.password == submitted_password
  end

  def record_login_at(date_time = DateTime.now)
    self.last_login_at = date_time
  end
end

# {% endhighlight %}
```

### Testing
Testing records is done in much the same way as you would have tested methods on any ActiveRecord model. The main difference is that there should be much fewer tests and these tests should be simply setting a value and testing the same value is retrieved.

Tests that hit the database are slower because of their interaction with the database. I don't stub the database out at this level as a record is only about talking to the database. These tests are few enough that slower tests are not a problem.

```rb
# {% highlight ruby %}
class UserRecordTests < MiniTest::Test
  # My helper to refresh the database after each test
  include DatabaseTesting

  def test_can_save_first_name
    # create domain object
    first_name = Name.new 'Peter'

    # set record entry
    record = UserRecord.new first_name: first_name

    # assert against domain object
    assert_equal first_name, record.first_name
  end
end
# {% endhighlight %}
```

Testing for entities can easily be separated from the database by providing them with a stand in record. My tests all initialize the entity with an openstruct to stand in for the record. There are then two kinds of tests. First setting values on the openstruct and testing the entities behave correctly. Otherwise, manipulating the entity and asserting the correct values are in the openstruct

```rb
# {% highlight ruby %}
class UserTest < Minitest::Test
  def record
    @record ||= OpenStruct.new
  end

  def user
    @user = User.new record
  end

  def teardown
    @user = nil
    @record = nil
  end

  def test_user_has_correct_full_name
    # Setup record
    record.first_name = 'Peter'
    record.last_name = 'Saxton'

    # Check behavior
    assert_equal 'Peter Saxton', user.full_name
  end

  def test_user_sets_first_name_on_record
    # Manipulate user
    user.full_name = 'Peter Saxton'

    # Check record
    assert_equal 'Peter', record.first_name
  end

  def test_user_sets_last_name_on_record
    user.full_name = 'Peter Saxton'
    assert_equal 'Saxton', record.last_name
  end
end
# {% endhighlight %}
```

### Conclusion

This is my take on entities and records. I encourage you to give them a try as multiple entities can be an effective cure for god objects. This is not quite the whole persistence problem solved as we still need to pull the correct records out of our database. That is my next post and it's on repositories.

### Resources

- [Fat model skinney controller is a load of rubbish](http://blog.joncairns.com/2013/04/fat-model-skinny-controller-is-a-load-of-rubbish/)
  Don't make my mistakes wish I had found this post earlier, thanks [Jon Cairns](https://twitter.com/joonty)
- [Skinny Controllers, Skinny Models](https://robots.thoughtbot.com/skinny-controllers-skinny-models)
  Worth saying one more time, this time from thoughtbot and [Joe Ferris](https://twitter.com/joeferris)
- [How I use ActiveRecord serialize with custom data types]( http://viget.com/extend/how-i-used-activerecord-serialize-with-a-custom-data-type)  
  Explaining the serialize method in active record by [Zachary Porter](https://twitter.com/zporter9)
- [Building rich domain objects](http://victorsavkin.com/post/41016739721/building-rich-domain-models-in-rails-separating)
  Separating models on similar lines using models, data objects and repositories from [Victor Savkin](https://twitter.com/victorsavkin)
