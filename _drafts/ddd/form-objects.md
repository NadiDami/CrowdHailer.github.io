---
layout: post
title: Border control with form objects
description: Using form objects to enforce your domain language.
date: 2015-07-23 17:20:06
tags: ruby design
author: Peter Saxton
---

### Introducing the gatekeepers
At some point your programs will need to be available to the outside world. This will result in a need to send data to the application. In a web application data comes from the users in forms. This input is always as strings and we want to transform it into the form that is most useful to us. Input is most useful to us when it is transformed to concepts that are native to our problem. In Ruby, this means objects. This is to use our domain specific language, the value of which I discussed [in the previous post]().

Form objects exist to ensure we use our domain language. They take raw input and transform it into understandable data. Along the way they may also track errors in the transform. Once data has passed this layer of border control it is not verified again.

### Using form objects

A form object's singular responsibility is to shield the core of your program from unreliable and possibly unsafe input. In a web application a form object is normally used at the start of a controller action. There are always three steps. First, initialize the object with raw data. Then check the form is valid. Finally, use the form values in your process.

```rb
# {% highlight ruby %}

# Instatiate with raw input
input = request.params
form = Form.new input

# Check input is valid
return unless form.valid?

# Carry on processing assuming everything is good.
email = form.email

# {% endhighlight %}
```

There is no limit to how simple a form object can be. I almost always implement one even if there is only one input. An extremely basic form object could be created as follows.

```rb
# {% highlight ruby %}
class SignUpForm
  def initialize(**input)
    raw = input.fetch('email') { '' }
    @email = raw.strip
  end

  attr_reader :email

  def valid?
    !email.empty?
  end
end

# {% endhighlight %}
```

Trivial examples can make form objects seem very simple. However, they can quickly get complicated for a few reasons:

- They have to communicate with both the delivery mechanism and business logic
- Rapid change from the front end filters down to them. If an input changes from a datepicker to separate day/month/year inputs then these changes has to be reflected in the form object
- Error reporting: everywhere else in the system bad input should throw an error. However, in the form we need to keep a track of all the errors and invalid input so we can send it back to the user for changes.

That these three issues surface on the edge of the system is a good thing. It means that they are not issues in the core of the application. The error reporting in particular is a gritty issue. Sometimes you want to clear an input if the value was invalid, sometimes show the value they entered. Sometimes it's enough to report that an input was invalid whereas other times you need to say 'too short', 'too long' or 'invalid characters'.

### Handling Errors

When creating forms I always ensure I cannot return an invalid value. If I forget to check the form is valid, I want errors to be thrown if I try to access bad data. This is different to the behaviour that is implemented in most validation libraries, such as Active Model Validation, which will return invalid data so you can redisplay it to the user.

```rb
# {% highlight ruby %}
# Form based on ActiveRecord Validations
form = SignUpForm.new 'email' => '!!'

form.email
# => '!!'
# This is not a sensible response

# {% endhighlight %}
```

To be able to access the original input and the reason for failure during coercing input, I make the form methods accept a block to be called if the coercion fails. It is passed the original value and the error that would have been raised if no block was given. This allows me to access the details about the failure and still raise exceptions in code that does not know how to handle bad data.

```rb
# {% highlight ruby %}
form = SignUpForm.new 'email' => '!!'

# I require an email from the form
form.email
# !! ArgumentError

# I want an email from the form but have a backup plan if not possible
form.email do |raw, error|
  puts "Tried to create email '#{raw}'"
  puts "This was invalid: '#{error.message}'"
  return nil
end
# {% endhighlight %}
```

### Building a form object

A form object should know if input is missing or invalid, but I do not give my form objects the knowledge to decide why a given piece of data is invalid. I find that validation rules are best handled by initializing a dedicated type.

Earlier we had an example form that returned email input as a string. Let's revist that form assuming that we have an email class. The email class handles validation and will fail with an `ArgumentError` should it be unable to handle the input. [See an example implementation of email class](https://github.com/CrowdHailer/typtanic/blob/master/lib/typetanic/email.rb). The simple form can be changed to the following and gains the ability to handle errors:

```rb
# {% highlight ruby %}
class SignUpForm
  def initialize(**input)
    @input = input
  end

  def email
    raw = input.fetch('email') { '' }
    Email.new raw
  rescue ArgumentError => err
    raise unless block_given?
    return yield raw, err
  end
end
# {% endhighlight %}
```

### Conclusion

Form objects are so called because they handle form input. Coercing raw data to domain constructs as soon as possible is worthwhile for all input regardless of source. In other contexts, they have different names. For example in hexagonal architecture they are adapters and in clean architecture, interface adapters.

It can often be difficult working with form objects. They are at the point where your code and the outside world meet. Well constructed they can improve your experience working in most of the rest of your codebase.

*Are you protecting your code with form objects? If so, let me know it is going and what you think of them.*

### Resources

- [Writing form objects with virtus](http://hawkins.io/2014/01/form_objects_with_virtus/)  
  The post that convinced me to try form objects. However I no longer agree with bundling detailed validation rules into forms.

And

- [Vulcanize](https://github.com/CrowdHailer/vulcanize)  
  An early stage library to build form objects that work well with custom types.

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
