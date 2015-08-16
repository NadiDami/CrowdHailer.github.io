---
layout: post
title: Introducing Interactors to represent getting stuff done.
description: The line in the sand between domain code and the framework
date: 2015-08-16 16:30:05
tags: ruby design
author: Peter Saxton
---

### What's in a name?
Interactors suffer from a bit of an identity crisis. I have seen them called 'interactors', 'use cases' and 'service objects'. Here is my take on each of those terms:

> **Service object** is used to describe the encapsulation of an external service that your system uses. E.g. you might have an 'email' service object.

> **Use case** makes the most sense on a non-technical level, so the 'Login usecase' is something that a customer does.

> **Interactor** is the technical term for the Ruby object that is used to perform a given use case.

I believe 'interactor' is the most useful term when talking about an implementation and will therefore stick to that.

### What are interactors?
An interactor orchestrates components in a system to complete a specific business use case. It is important that an interactor knows how to delegate — the desired result should be achieved without the interactor carrying out any of the work itself. It should also know nothing about how the result of its action will be presented to the user.

As something that is defined by what they do not do, it is tricky to see the value out of context. However the separation they enforce between the code that deals with rendering, HTTP, and sessions from the code with the important business logic rapidly increases in value as the system grows. It also removes dependence on any particular framework; when all the framework code is outside the interactors then it can be replaced whilst leaving the business logic untouched.

### Basic Structure
An interaction can occur only once. A user many try the same interaction multiple times but as a different outcome is possible on each occasion it is classified as a separation interaction. What happens as the result of an interaction cannot be changed after it has occurred.

An instance of an interactor is used to represent a single interaction. To reflect the desired behaviour it is initialized with all of the required parameters and context; these cannot be modified after initialization. On the object the only methods that are available are query methods to report on what occurred. People often add `success?/failure?` methods but I try to use terms that are specific to the interaction such as `created?/deleted?/approved?`.

That interactors cannot be modified means they are immutable. Ruby makes immutability hard so I do not write any code to enforce this and simply follow the convention of only querying they response object.

An example interactor representing the user use case of creating a post for a blog might look something like the following.


{% highlight ruby %}
# Inside a controller action
def create
  # Coerce insecure user input into the domain context
  form = CreatePostForm.new params[:post]

  # Run the use case passing in everything that is needed as arguments
  create_post = CreatePost.new self, form

  # Query the interactor instance on the outcome of the interaction
  if create_post.created?
    redirect_to post_url(create_post.post)
  else
    @form = create_post.form
    render :new
  end
end

# in a create_post.rb file
class CreatePost
  # initialize with the params in the form and the user in the context
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
{% endhighlight %}

The controller action knows how to call an interactor as well as the methods it can query to find the outcome.
The controller may not interact with the domain in any other way, all entities, values and services and invisible to it.
The interactor does not know anything about the framework, the results it exposes have no knowledge of how they will be presented, i.e. there are no `to_html` methods. The controller or a dedicated presenter will convert the plain data objects into a rendered view.

The interactors demote a strong boundary between your domain code which should be pure ruby and your framework code which can be full of framework specific terms, from rails/sinatra/etc. This boundary allows you to test the two pieces in isolation.

#### Testing the domain
Testing the domain with interactors is easy because they explicitly passed all parameters during there initialization. The steps to test the domain are setup the arguments to be passed to the Interactor, initialize the interactor and finally query on the result. It is immutable so test can be run in any order. The benefits of this include.

- There is no need to have a step where you log in the user. Just pass any user into the context
- Any other awkward things like mailers or bug reporting can have null implementations, also part of the context.
- All return values are Ruby objects and these are much easier to test on.

With interactors it also becomes much easier to write an integration test than to write a test that needs to make a HTTP request and understand HTML responses. This is an example of testing an interactor.

{% highlight ruby %}
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
    create_post = CreatePost.new context, invalid_form
    refute create_post.created?
  end

  def test_will_create_post_with_valid_title
    create_post = CreatePost.new context, valid_form
    assert create_post.created?
  end
end
{% endhighlight %}

Compare this with the last test you wrote where you had to check an action from a logged in user which required RackTest or Capybara.

### Testing the controllers and views
Working with interactors does not improve dealing with HTML parsing or setting up session data. In those test you will still want to make use of RackTest or Capybara. What it does do is make those tests a whole lot simpler. You can write a single test for each possible outcome for a given interactor where the outcome is stubbed. With one logical test for each usecase outcome you can escape from trying to test the domain through the unwieldy web based interface.

### My implementation AllSystems
It would certainly be possible to implement interactors without a gem. I am also a fan of less dependencies where possible. However using a very small gem allows me not have to keep checking the plumbing of my interactor objects. I have written a gem called AllSystems that allows blocks to be passed for each possible outcome. This follows the principle of tell don't ask which is best laid out in the talk eastward oriented code.

So what do you think? Will you be tempted to try interactors or do you have a way to keep the framework you use encapsulated? Leave me a comment I will be very interested.

blocks with east oriented code confident code sandy at bath

http://technology.stitchfix.com/blog/2015/06/02/anatomy-of-service-objects-in-rails/

https://medium.com/@KamilLelonek/what-service-objects-are-not-7abef8aa2f99
