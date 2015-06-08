---
layout: post
title: Building on Solid foundations
description: Moving from your tools to your domain.
date: 2015-07-23 17:20:05
tags: ruby design
author: Peter Saxton
---

### Background

A fundamental tenant of domain driven design(DDD) is that you create software in an environment rooted in the problem domain. Before prescribing some new objects, I would like to start with a question. Which of these lines of code should return true and which should return false?

```rb
# {% highlight ruby %}
'January'.winter?
'password'.secure?
'B-'.pass?
# {% endhighlight %}
```


Perhaps you got it, perhaps not. Lets make it clearer with some small changes.

```rb
# {% highlight ruby %}
'January'.secure?
'password'.pass?
'B-'.winter?
# {% endhighlight %}
```

The answer is neither. Every line will, as it should, raise a `NoMethodError`. These two code samples have exactly the same outcome and yet they read very differently to us as humans. In the first example we see 'January' as a month and so make sense of the question is it winter.

Let us imagine the system is asking about months and seasons and we want to implement the `winter?` method. How do we do that? Well we can reopen the string class and monkey patch in a `winter?` method. However as we can see the question is it winter doesn't make sense to 'B-'. So this method can't be added to the string class.

DDD will tell us the answer to this as follows. Our problem domain doesn't have strings it has months. When talking to stakeholders no one ever said 'then the user enters the string of there birthday', its month of there birthday. And so the refinement we make is to create a Month object to encapsulate the data and behavior of what a month means to our system. With that concept added we can write the following code.

```rb
# {% highlight ruby %}
month = Month.new 'January'
month.winter?
# => true
# {% endhighlight %}
```

And if we start putting in invalid months the program fails before we even ask the question 'is it winter?'

```rb
# {% highlight ruby %}
month = Month.new 'B-'
# !! InvalidMonth
# {% endhighlight %}
```

The solution to our problem has been to create a value object for our program that represents a month.

### What are value objects?

A value object is any object who's identity is characterized by it's attributes. Ruby primitives such as `String`, `Integer`, `DateTime` are all value objects. It is not possible for value objects to have a history, any instance of our Month class should be treated as exactly the same regardless of source. For example with strings.

```rb
# {% highlight ruby %}
string1 = 'hello'
string2 = 'hello'

string1 == string2
# => true
# {% endhighlight %}
```

This is in contrast to entities that can have an identity that is based on more than their attributes. For example a user object that has only a single attribute.

```rb
# {% highlight ruby %}
user1 = User.new :name => 'Peter'
user2 = User.new :name => 'Peter'

user1 == user2
# => false
# {% endhighlight %}
```

Our users can have a history user2 can update their name and they will still be user2.

### Using value objects.
Values can be used in place of a Ruby primitive anywhere your problem domain specifies extra behavior. In recent projects I have discovered this means almost everywhere there is a primitive, conceptual gains can be made by changing to a value object. If a username must have at least 3 characters then it is not just a string. If an quantity cannot be negative then it is not simply an integer.

After creating your value objects they should be used throughout the program. This means input should should be wrapped as soon as possible. They should also be cast back into strings as late as possible.

> Maybe something here or casting them to strings to save in a database as late as possible
> Or that values can be built on values.

### Building a value object
Implementation is up to the programmer and as long as the interface is passing all your tests you should not care whats going on inside. There are a few things to bear in mind when building your value objects.

First think hard about subclassing the primitive you are trying to escape when building your domain object. It can implement things that might not make any sense any more. For example lets extend our Month class so it can be initialized with 'Jan' but still keeps the internal string representation as 'January'. We now have this code.

```rb
# {% highlight ruby %}
# if subclassed from String
january = Month.new 'Jan'

january.to_s
# => 'January'

january.length
# => 7
# {% endhighlight %}
```

In reality we probably want the length of January to be 31days, or no method. If there is no requirement for the length of a month don't implement it.

Second remember to implement methods for basic behaviour such as coercing to a string and how to sort your values. Ruby's comparable module is most useful when building value objects. Don't know the difference between `to_s` and `to_str`? now is a good time to find out.

### Conclusion
Value objects allow you to define more of your program in the language of the domain. The objects you create should be specific to your domain this means they are not necessarily reusable. If my project is UK based then January is in winter. I Australia the answer should be false and if my buisness is international then maybe we shouldn't be deciding the season on month alone.

A downside to using value objects for everything is that you need to build them, which takes time. It's just so much easier to use the ever versatile string. Even then the time spent clarifying these concepts into dedicated objects helps with understanding the domain that saves time in the long run.

So have you used value objects. Let me know how it went and what you think.
Input should be transformed to domain objects as soon as possible. Form objects are a good way to do that and will be the next post. 

### Resources

There are some great tutorials on value objects in ruby. All of these I found very helpful in thinking about value objects.

- [Overcoming our obsession with stringly typed Ruby](https://www.youtube.com/watch?v=7Obobjq8g_U)  
  An excellent video from [David Copeland](https://twitter.com/davetron5000) on overusing Ruby strings
- [String, the original value object](http://erniemiller.org/2012/11/01/ruby-tidbit-string-the-original-value-object/)
  Article suggesting times when you should subclass string to create your value objects by [Ernie Miller](https://twitter.com/erniemiller)
https://github.com/tcrayford/Values
- [Using VAlue Objects](http://www.informit.com/articles/article.aspx?p=2220311&seqNum=11)
  Using value objects as ActiveRecord attributes.
- [Value Objects Explained with Ruby](http://www.sitepoint.com/value-objects-explained-with-ruby/)
  A thorough grounding in the concept of value objects with ruby.

And

- [Typetanic](https://github.com/CrowdHailer/typtanic)
  My repository of value objects that I have found do cross between projects.
