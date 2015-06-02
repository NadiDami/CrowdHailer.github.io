This is our first layer building on top of the basic types that we discussed in the previous post.
To work out what we are trying to do it is helpful to have a clear definition of the problem and what part of it that problem we are wanting to solve here.

A form object is one of the more important objects in this discussion as it can cross the interface from the delivery mechanism to the domain.
In all cases where this is possible the main language should be the language of the domain.
A form objects purpose is to take an input (read unsafe/unreliable).
It has to then present this input in the language of the domain.
As a secondary purpose it should keep track of any missing or invalid input.

```rb
form = Form.new input

form.valid?
form.validate?
```

## Note on validations
A form object should know when an item is invalid.
However it should not need to know the validation logic of every input it might receive.

There are several very helpful libraries when it comes to writing forms.
One of the best is virtus which I used in a few projects. It has a very convenient DSL that lets you quickly write forms.
What I discovered after a while is a convenient DSL has two problems.
Or to be more specific there are two problems it can't help tackle.

1. It doesn't help you write test any faster
2. It doesn't reduce repetiton, just make it look less obvious because there are fewer lines repeated.

To illustrate this lets look at some code.
This is code taken directly from a project that was using virtus

```rb
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
```

Such value objects as the password object are described in my previous post.

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
