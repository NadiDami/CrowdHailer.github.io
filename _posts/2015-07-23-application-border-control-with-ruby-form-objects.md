---
layout: post
title: Application border control with form objects
description: Using form objects to preserve your domain language against invalid input.
date: 2015-07-23 17:20:06
tags: ruby design
author: Peter Saxton
---

Part 3 in [Domain Drive Design series](/2015/07/14/domain-driven-design-introduction.html) following on from [value objects](/2015/07/15/value-objects-in-ruby.html).

### Introducing the gatekeepers
At some point your programs will need to be available to the outside world. This will result in a need to send data to the application. In a web application data comes from the users in forms. This input is always as strings and we want to transform it into the form that is most useful to us. Input is most useful to us when it is transformed to concepts that are native to our problem. In Ruby, this means objects. This is to use our domain specific language, the value of which I discussed [in the previous post](/2015/07/15/value-objects-in-ruby.html).

Form objects exist to ensure we use our domain language. They take raw input and transform it into understandable data. Along the way they may also track errors in the transform. Once data has passed this layer of border control it is not verified again.

### Using form objects

A form object's singular responsibility is to shield the core of your program from unreliable and possibly unsafe input. In a web application a form object is normally used at the start of a controller action. There are always three steps. First, initialize the object with raw data. Then check the form is valid. Finally, use the form values in your process.

{% highlight ruby %}

# Instatiate with raw input
input = request.params
form = Form.new input

# Check input is valid
return unless form.valid?

# Carry on processing assuming everything is good.
email = form.email

{% endhighlight %}

There is no limit to how simple a form object can be. I almost always implement one even if there is only one input. An extremely basic form object could be created as follows.

{% highlight ruby %}
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

{% endhighlight %}

Trivial examples can make form objects seem very simple. However, they can quickly get complicated for a few reasons:

- They have to communicate with both the delivery mechanism and business logic
- Rapid change from the front end filters down to them. If an input changes from a datepicker to separate day/month/year inputs then these changes has to be reflected in the form object
- Error reporting: everywhere else in the system bad input should throw an error. However, in the form we need to keep a track of all the errors and invalid input so we can send it back to the user for changes.

That these three issues surface on the edge of the system is a good thing. It means that they are not issues in the core of the application. The error reporting in particular is a gritty issue. Sometimes you want to clear an input if the value was invalid, sometimes show the value they entered. Sometimes it's enough to report that an input was invalid whereas other times you need to say 'too short', 'too long' or 'invalid characters'.

### Handling Errors

When creating forms I always ensure I cannot return an invalid value, for any given field. If I forget to check the form is valid, I want errors to be thrown when I try to access bad data. This is different to the behavior that is implemented in most validation libraries, such as Active Model Validation, which will return invalid data so you can redisplay it to the user.

{% highlight ruby %}
# Form based on ActiveRecord Validations
form = SignUpForm.new 'email' => 'not an email'

form.email
# => 'not and email'
# This is a confusing return value from an email method

{% endhighlight %}

To be able to access the original input and the reason for failure during coercing input, I make the form methods accept a block to be called if the coercion fails. It is passed the raw, uncoercable  value and the error that would have been raised if no block was given. This allows me to access the details about the failure and still raise exceptions in code that does not know how to handle bad data.

{% highlight ruby %}
form = SignUpForm.new 'email' => 'not an email'

# I require an email from the form
form.email
# !! ArgumentError

# I want an email from the form but have a backup plan if not possible
form.email do |raw, error|
  puts "Tried to create email '#{raw}'"
  puts "This was invalid: '#{error.message}'"
  NullEmail
end
{% endhighlight %}

### Building a form object

A form object should know if input is missing or invalid, but it should not have the knowledge to decide why a given piece of data is invalid. I find that validation rules are best handled by initializing a dedicated type.

Earlier we had an example form that returned email input as a string. Let's revisit that form assuming that we have an email class. The email class handles validation and will fail with an `ArgumentError` should it be unable to handle the input. [See an example implementation of email class](https://github.com/CrowdHailer/typtanic/blob/master/lib/typetanic/email.rb). The simple form can be changed to the following and gains the ability to handle errors:

{% highlight ruby %}
class SignUpForm
  def initialize(**input)
    @input = input
  end

  attr_reader :input

  def email
    raw = input.fetch('email') { '' }
    Email.new raw
  rescue ArgumentError => err
    raise unless block_given?
    return yield raw, err
  end
end
{% endhighlight %}

### Conclusion

Form objects are so called because they handle form input. Coercing raw data to domain constructs as soon as possible is worthwhile for all input regardless of source. In other contexts, they have different names. For example in hexagonal architecture they are adapters and in clean architecture, interface adapters.

It can often be difficult working with form objects. They are at the point where your code and the outside world meet. Well constructed they can improve your experience working with the rest of your codebase keeping internal data clean.

*Are you protecting your code with form objects? If so, let me know it is going and what you think of them.*

The next post is on the core actors in our domain, [entities](http://insights.workshop14.io/2015/08/02/tackling-god-objects-in-ruby.html).

### Resources

- [Writing form objects with virtus](http://hawkins.io/2014/01/form_objects_with_virtus/)  
  The post that convinced me to try form objects. Although I no longer agree with bundling detailed validation rules into forms, it is a good read. Thanks for the inspiration from [@adman65](https://twitter.com/adman65)
- [Eastward Ho! A Clear Path Through Ruby With OO](https://www.youtube.com/watch?v=kXcrClJcfm8)  
  Avoiding ambiguity by coding with a backup plan, by [@saturnflyer](https://twitter.com/saturnflyer). This talk explains the logic behind the bad attribute handling blocks I add to forms.
- [Nothing is Something](https://www.youtube.com/watch?v=9lv2lBq6x4A)  
  Creating an object to represent you missing or invalid input that will not blow up your program from [@sandimetz](https://twitter.com/sandimetz)

And

- [Vulcanize](https://github.com/CrowdHailer/vulcanize)  
  My library to build form objects that work well with custom types.
