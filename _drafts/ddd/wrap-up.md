Domain Driven Design, Where the real value lies.
have conversation once
concepts not constructs
found the rails way
A model is not made of models
A view is not made of views
and controllers are not inherited from controllers
Pushing logic below the model layer

The problem with misunderstood MVC. Breaking things into small pieces is pretty universally accepted as a good idea. MVC offers a nice way to break up an application into logically distinct layers. A model, a view and a controller. Three nice pieces, However as an application grows each piece grows and you end up with 3 big pieces. Obviously we want to break into more pieces and the question becomes where. This is not a problem with MVC there is noting in MVC that prevents you have an M made from 10 nice pieces but it doesn't care what these pieces are. It also means it is no help in deciding what these pieces should be.

The problem with rails. Rails is an excellent too which has a problem that because it does everything you need for so long you don't know when to leave.

###
Where entitises really shine is once you start making muliple for each record class from that point on there is now a path to handle any amount of business logic.  A user might have complicated authetication records with remember me tokens, password reset tokens, oauth this can all be farmed out to a credentials objects which. There might be a bunch of questions that where asked during signup make a Questionaire Entity that works off the same record

This pattern is not limited to database records, user sessions are often represented by a simple object that

## Round up
#### Layout
In the blog application that I have mentioned along with this series I have tried to push as far away from rails conventions as possible. No due to any dislike of rails but to get some perspective with distance.
For example I group templates with controllers before I group controllers together.
I am not sure its a better way but I definetly learnt alot exploring it. What I am sure about is that you could blindly copy my layout and work off it and if you did you would end up just as much in a muddle as following any other layout blindly

lessons
same lessons for non basic collections as stringly
prefer vertical grouping
Do not redesign code for tests but if its hard to test put that as one red mark
Design patterns are most useful in creating a language. Language is important.
I want to be able to discuss about value objects without explaining about them first. A value object is an object with no history. An entity is an object with durable identity and not something that subclasses ActiveEntity.
I think that template better describes the erb files we have and view is better for the page object. However with the history view is missleading and viewModel is just a disaster.
I now have one remain peeve with rails that I feel is justified. I dislike if for convolution the 'Active record' pattern with the 'ActiveRecord' implementation.

separation has to be complete or it is not useful at all. If two pieces of code have just one weird interdepencey then changing either can break the other. Only it will happen less often so be more distructive when you create that new bug.
I like the separation of the the brains of the application from the delivery method. To insist on it I enter the console using
```
$ irb -r ./config/boot
# NOT
$ irb -r ./config/application
```

and I also run the following
NB after commiting
```
$ rmdir app test/app
$ rake test
$ git reset --hard HEAD
```
best talks

east oriented

stringly

confident ruby
tell dont ask, eastward oriented code
sandy metz bath ruby talk on small talk


# END


## Building form objects

There are a few ruby libraries that help with constructing form objects. One of these is Virtus which I have used in several projects but recently decided can lead to some problems. These shortcomings I feel are worth discussing.


However so far I have not found any of them to work as I would like.

There are several very helpful libraries when it comes to writing forms.
One of the best is virtus which I used in a few projects. It has a very convenient DSL that lets you quickly write forms.
What I discovered after a while is a convenient DSL has two problems.
Or to be more specific there are two problems it can't help tackle.

1. It doesn't help you write test any faster
2. It doesn't reduce repetiton, just make it look less obvious because there are fewer lines repeated.

To illustrate this lets look at some code.
This is code taken directly from a project that was using virtus


```rb
# {% highlight ruby %}
class PasswordResetForm
  include Virtus.model

  attribute :password, String
  attribute :password_confirmation, String, :reader => :private

  def password
    (super || '').strip
  end

  def password_confirmation
    (super || '').strip
  end

  def password_confirmed?
    password == password_confirmation
  end

end

class UpdatePasswordForm

  include Virtus.model
  attribute :password, String
  attribute :password_confirmation, String, :reader => :private
  attribute :current_password, String

  def password
    (super || '').strip
  end

  def password_confirmation
    (super || '').strip
  end

  def current_password
    (super || '').strip
  end

  def password_confirmed?
    password == password_confirmation
  end

end
# {% endhighlight %}
```

So this code is quite declarative. The parts written in the DSL are very clear and the rest is not too confusing.
We decided for this project that we would strip whitespace from the start and end of the password.
Similar situations is deciding that names should be capitalized or emails should be lowercase.

The issue with this code was only visible after looking at the tests.
There were 5 tests which where there to check that various forms stripped whitespace from passwords.
There were actually more because the create user form also had a password and password confirmation.
It got out of hand when we set a minimum and maximum length for the password. which each needed to be checked 7 times.
There are shortcuts i.e. you don't  need to validate the password confirmation as you will only be checking it is equal to the password.

However the problem as I see it is that the forms where told to expect strings but then had to realise they where working with a supset of all possiblestrings.
With a full featured password object. It would be possible to have the following code.
```rb
# {% highlight ruby %}
class PasswordResetForm
  include Virtus.model

  attribute :password, Password
  attribute :password_confirmation, Password, :reader => :private

  def password_confirmed?
    password == password_confirmation
  end

end

class UpdatePasswordForm

  include Virtus.model
  attribute :password, Password
  attribute :password_confirmation, Password, :reader => :private
  attribute :current_password, Password

  def password_confirmed?
    password == password_confirmation
  end

end

class Password
  def initialize(raw="")
    @value = raw.strip
  end

  def to_s
    @value
  end
end
# {% endhighlight %}
```

Such value objects as the password object are described in my previous post.

A form object should know when an item is invalid.
However it should not need to know the validation logic of every input it might receive.

## Typtanic extension
A value object when given invalid input throws an error. This is because the string 'ballons' is NOT an email.
Therefore I do not want to represent it as an email with an invalid flag or other check.

This is however less helpful if you want to handle the errors in some way other than crashing the program.
Often with form objects we want to collect all the errors and display them to the user.
For this purpose I include the `forge` method on all of my value objects.

Forge is inspired from the fetch method available on hashes. and works as a great patern.

```rb
hash.fetch(:item)
# This is important and I don't know what to do if its missing
hash.fetch(:item) { NullItem.new }
# I want this but if it is missing I have a backup plan
```

```rb
Email.forge('bad')
# Nuts this is bad and I don't have a solution, fail with error

Email.forge('valid@lovely.org') do |error|
  # I know what to do with this error.
  # Probably add it to a collection of errors for this form
end
```

With this a form object can use the forge method for each attribute.
It knows if the result is invalid. It doesn't know how it is invalid.
The form has no concept of what is too long for a password. or disallowed for a username

https://github.com/paulrayner/ddd_sample_app_ruby
https://github.com/emadb/ruby_loves_ddd
https://www.youtube.com/watch?v=rGA_FNew-6g
https://www.youtube.com/watch?v=CjNBnkMHjh4


Talk on DDD at 10. really good talk. explain that what i havent talked about bounded context. is the most important part. http://www.se-radio.net/2015/05/se-radio-episode-226-eric-evans-on-domain-driven-design-at-10-years/
