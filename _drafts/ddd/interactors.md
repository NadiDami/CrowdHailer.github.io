---
layout: post
title: Getting stuff done with interactors
description: The line in the sand between domain code and the framework
date: 2015-04-23 17:20:05
tags: ruby design
author: Peter Saxton
---

### What's in a name?
Interactors suffer from a bit of an identity crisis. I have seen them called 'interactors', 'use cases' and 'service objects'. Here is my take on each of those terms:

> **Service object** is used to describe the encapsulation of an external service that your system uses. E.g. you might have an 'email' service object.

> **Use case** makes the most sense on a non-technical level, so the 'Login usecase' is something that a customer does.

> **Interactor** is the technical term for the Ruby object that is used to perform a given use case.

I believe 'interactor' is the most useful term when talking about an implementation and will therefore to stick to that.

### What are interactors?
An interactor orchestrates components in a system to complete a specific business use case. It is important that an interactor knows how to delegate — the desired result should be acheived without the interactor carrying out any of the work itself. It should also know nothing about how the result of its action will be presented to the user.

As something that is defined by what they do not do, it is tricky to see the value out of context. However the separation they enforce between the code that deals with rendering, HTTP, and sessions from the code with the important business logic rapidly increases in value as the system grows. It also removes dependence on any particular framework; when all the framework code is outside the interactors then it can be replaced whilst leaving the business logic untouched.

### Basic Structure
An interaction can occur only once. A user many try the same interaction multiple times but as a different outcome is possible on each occasion it is classified as a separation interaction. What happens as the result of an interaction cannot be changed after it has occurred.

An instance of an interactor is used to represent a single interaction. To reflect the desired behaviour it is initialized with all of the required parameters and context; these cannot be modified after initialization. On the object the only methods that are available are query methods to report on what occurred. People often add `success?/failure?` methods but I try to use terms that are specific to the interaction such as `created?/deleted?/approved?`.

That interactors cannot be modified means they are immutable. Ruby makes immutability hard so I do not write any code to enforce this and simply follow the convention of only querying they response object.


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

#### Testing the domain
Testing the domain with interactors is easy because they explicitly depend on their context:
- There is no need to have a step where you log in the user. Just pass any user into the context
- Any other awkward things like mailers or bug reporting can have null implementations
- All return values are Ruby objects and these are much easier to test on

  ```rb
  assert_equal form.title, updated_post.title
  vs
  assert_includes last_response.body, form.title
  ```

With interactors it also becomes much easier to write  an integration test that needs to make a HTTP request and understand HTML responses:

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
Working with interactors does not improve dealing with HTML parsing but it does make those tests as a whole lot simpler. Not only are you not hitting the database, you won't be touching any part of the domain:

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
