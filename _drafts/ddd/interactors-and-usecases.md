---
layout: post
title: Your usecase
description: Using interactors to encapsulate you business logic
date:
tags: ruby design
author: Peter Saxton
---

As part of an my ongoing exploration of design patterns

### Whats in a Name?
Interactors suffer from a bit of an identity crisis particularly in the ruby community. I have seen then called interactors, use cases and serice objects. I feel have though somewhat on this and decided that I believe interactor to be the most useful term for the following logic.

> **Service object** is sometimes used to describe the encapsulation of an external service that you system uses. E.g. you might have a email service object.

> **Usecase** makes the most sense on a non technical level, so the Login usecase is what the customer does.

> **Interactor** is the technical term for the Ruby object that is used to realise a given usecase.

### What are interactors?
A general definintion of an Interactor is liable to end up nebulous as it is the part of the system that is most personal to a given project.
An interactor orchestrates a specific business usecase. It achives this by coordinating the interaction of other business objects in the system.

### Basic Structure
A interactor instance should run only once. They are initialized with parameters and context. should always report the same outcome once the interation has occurred and so have no changable state. They should also use the business logic terms as much as possible.
```rb
class CreatePost
  def initialize(form, context)

  end

  def user
    context.current_user
  end

  def log(*args)
    context.logger.log(*args)
  end
end
```

```rb
class UpdateProfile
  def initialize(id, form, context)
    @id = id
    @form = form
    @context = context
  end
  attr_reader :form, :id

  def authority
    context.current_user
  end

  def user_account
    @user_account || = Users.fetch(id) { raise 'No account'}
  end

  def run
    return if @run
    @run = true
    run!
  end

  private

  def run!
    raise 'Not your account' unless authority == user_account
    raise 'Invalid details' unless form.valid?
    user_account.update(form)
  end

end
```
### Why?
The hard boundary between the M and VC
The thin blue line between the M and VC
The line in the sand between the M and VC. We have talked about rich value objects and entities.
We have previously talked that the M in MVC is not just the user model for the user view the m is much much more. It is really nice to work with a County object and a Money object through a rich domain. Inevitably at some point in the processing of a view our objects have to turn into strings in HTML. Data from our inputs will arrive as a hash of strings. It may be the forms responsibility to coerce data in to ruby objects but it is the interactors role to initialize them and ensure.
I have found it to be immensely valuable to ensure that this line is unbroken. Changing the delivery mechanism is often given as the reason. I often thought I am making a rails app if I decide to make a desktop app thats a big change, its not likely to happen. What is far more likely is a complete redisgn of the front end. This separation allows you to change to returning a JSON api and knowing exactly where the boundaries are

### Testing
It would certainly be possible to implement interactors without a gem. I am also a fan of not letting heavy dependencies get out of hand. However using a very small gem allows me reuse the tests. This is particularly important as I normally have a few tests crossing the interactiors. At this point I will introduce my gem 'AllSystems'

#### Testing the domain.
Testing the domain with Interactors is helped because the explicitly depend on there context and so it is very easy.
- There is no need to have a step where you log in the user. Just pass any user in the context
- Any other awkward things like mailers, or bug reporting can have null implementations that are help on the context
- All return values are ruby objects and much easier to test on

  ```rb
  assert_equal form.title, updated_post.title
  vs
  assert_includes last_response.body, form.title
  ```

### Testing the view
stub the result, none of this improves dealing with HTML parsing to check things are working but it does make those tests as a whole much simpler. Not only are you not hitting the database but you are not hitting the entire domain. On occasion I have developed an applications front end with stubbed 

```rb
interactor.stub :result, [:success, dummy_post]
assert_includes last_response.body dummy_post.title

interactor.stub :result, [:fail, form]
assert_includes last_response.body.input form.title
```
