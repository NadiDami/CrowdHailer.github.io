---
layout: post
title: Getting stuff done with interactors
description: The line in the sand between domain code and the framework
date: 2015-04-23 17:20:05
tags: ruby design
author: Peter Saxton
---

### Whats in a Name?
Interactors suffer from a bit of an identity crisis. I have seen them called 'interactors', 'use cases' and 'serice objects'. I believe interactor is the most useful term when talking about an implementation and will try to stick to that. Here is my take on each term.

> **Service object** is used to describe the encapsulation of an external service that you system uses. E.g. you might have a email service object.

> **Usecase** makes the most sense on a non technical level, so the Login usecase is what the customer does.

> **Interactor** is the technical term for the Ruby object that is used to perform a given usecase.

### What are interactors?
An interactor orchestrates components in a system to complete a specific business usecase. It is important that an interactor knows how to delegate and should achieve the result while carrying out none of the work itself. It should also know nothing about how the result of its action should be sent or presented to the user.

As something that is defined by what they do not do it is tricky to see there value out of context. However the separation they enforce between the code of rendering, http and session from the code with the important business logic is rapidly increases in value as the system grows. It also breaks dependence on a framework, when all the framework code is outside the interactors then it can be replaced while leaving the business logic untouched.

### Basic Structure
An interaction can occur only once. A user many try the same interaction multiple times but as a different out come is possible on each occasion it is a separation interaction. What happened as the result of an interaction cannot be changed after it has occurred.

An instance of an interactor is used to represent a single interaction. To reflect the desired behaviour it is initialized will all the parameters and context that is needed, these cannot be modified after initialization. On the object the only methods that are available are query methods to report on what occurred, people often add `success?/failure?` methods but I try to use terms that are specific to the interaction such as `created?/deleted?/approved?`.

That interactors cannot be modified means they are immutable. Ruby makes immutability hard so I do not write any code to enforce this and simply follow convention of only querying they response object.


```rb
# {% highlight ruby %}
def create
  form = CreatePostForm.new params[:post]
  create_post = CreatePost.new self, form

  if create_post.created?
    redirect_to post_url(create_post.post)
  else
    @form = create_post.form
    render :new
  end
end

class CreatePost
  def initialize(context, form)
    @form = form
    @user = context.current_user
    run
  end

  attr_reader :form, :user, :post

  private

  def run
    if form.valid?
      @post = Post.create form
      @created = true
    end
  end

end
# {% endhighlight %}
```

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

Integration test because hits all parts of the domain.
Much easer to write that an integration test that needs to make http request and understand html responses
```rb
# {% highlight ruby %}
class CreatePostTest < Minitest::Test
  def valid_form
    CreatePostForm.new :title => 'A nice title'
  end

  def invalid_form
    CreatePostForm.new :title => '<script>bad code</script>'
  end

  def context
    OpenStruct.new :current_user => nullUser
  end

  def test_will_not_create_post_with_invalid_title
    create_post = CreatePost.new context, valid_form
    refute create_post.created?
  end

end
# {% endhighlight %}
```

### Testing the view
stub the result, none of this improves dealing with HTML parsing to check things are working but it does make those tests as a whole much simpler. Not only are you not hitting the database but you are not hitting the entire domain. On occasion I have developed an applications front end with stubbed

```rb
interactor.stub :result, [:success, dummy_post]
assert_includes last_response.body dummy_post.title

interactor.stub :result, [:fail, form]
assert_includes last_response.body.input form.title
```
### Testing
It would certainly be possible to implement interactors without a gem. I am also a fan of not letting heavy dependencies get out of hand. However using a very small gem allows me reuse the tests. This is particularly important as I normally have a few tests crossing the interactiors. At this point I will introduce my gem 'AllSystems'

blocks with east oriented code confident code sandy at bath

http://technology.stitchfix.com/blog/2015/06/02/anatomy-of-service-objects-in-rails/

https://medium.com/@KamilLelonek/what-service-objects-are-not-7abef8aa2f99
