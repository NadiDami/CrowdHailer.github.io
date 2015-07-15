---
layout: post
title: Building on Solid foundations
description: Moving mindset from language to domain with value objects.
date: 2015-07-15 21:00:05
tags: ruby design
author: Peter Saxton
---

### Background

A fundamental tenant of domain driven design (DDD) is that you create software in an environment rooted in the problem domain. Before prescribing some new objects, I would like to start with a question: which of these lines of code should return true and which should return false?

{% highlight ruby %}
'January'.winter?
'password'.secure?
'B-'.pass?
{% endhighlight %}

Perhaps you got it, perhaps not. Let's make it clearer with some small changes:

{% highlight ruby %}
'January'.secure?
'password'.pass?
'B-'.winter?
{% endhighlight %}

The answer is neither. Every line will, as it should, raise a `NoMethodError`. These two code samples have exactly the same outcome and yet they read very differently to us as humans. In the first example, we see 'January' as a month and so make sense of the question 'is it winter?'.

Let us imagine the system is asking about months and seasons and we want to implement the `winter?` method. How do we do that? Well, we can reopen the String class and monkey patch in a `winter?` method. However, the question 'is it winter?' doesn't make sense to 'B-'. So this method can't be added to the String class.

DDD will tell us the answer to this is as follows. Our problem domain doesn't have strings, it has months. When talking to stakeholders no one ever said 'then the user enters the string of their birthday'. It's the month of their birthday. The refinement we make is to create a Month object to encapsulate the data and behaviour of what a month means to our system. With that concept in mine, we can write the following code:

{% highlight ruby %}
month = Month.new 'January'
month.winter?
# => true
{% endhighlight %}

And if we start putting in invalid months, the program fails before we even ask the question 'is it winter?'.

{% highlight ruby %}
month = Month.new 'B-'
# !! InvalidMonth
{% endhighlight %}

The solution to our problem has been to create a value object for our program that represents a month.

### What are value objects?

A value object is any object whose identity is characterized by its attributes. Ruby primitives such as `String`, `Integer`, and `DateTime` are all value objects. It is not possible for value objects to have a history — any instance of our Month class should be treated as exactly the same regardless of source. For example, with strings:

{% highlight ruby %}
string1 = 'hello'
string2 = 'hello'

string1 == string2
# => true
{% endhighlight %}

This is in contrast to entities that can have an identity that is based on more than their attributes. For example, take a user object that has only a single attribute:

{% highlight ruby %}
user1 = User.new :name => 'Peter'
user2 = User.new :name => 'Peter'

user1 == user2
# => false
{% endhighlight %}

Our users can have a history —  user2 can update their name and they will still be user2.

### Using value objects.
Value objects can be used in place of a Ruby primitive anywhere that your problem domain specifies extra behaviour. In recent projects I have discovered this means that almost everywhere that there is a primitive, conceptual gains can be made by changing it to a value object. If a username must have at least 3 characters, then it is not just a string. If a quantity cannot be negative, then it is not simply an integer.

After creating your value objects they should be used throughout the program. This means input should be wrapped as soon as possible. They should also be cast back into strings as late as possible.

> Maybe something here or casting them to strings to save in a database as late as possible
> Or that values can be built on values.

### Building a value object
Implementation is up to the programmer and as long as the interface is passing all of your tests, you should not care what's going on inside. Still, there are a few things to bear in mind when building your value objects:

First ,think hard about subclassing the primitive you are trying to escape when building your domain object. It can implement things that might not make any sense any more. For example, let's extend our Month class so it can be initialized with 'Jan' but still keeps the internal string representation as 'January':

{% highlight ruby %}
# if subclassed from String
january = Month.new 'Jan'

january.to_s
# => 'January'

january.length
# => 7
{% endhighlight %}

In reality we probably want the length of January to be '31 days', or return a 'NoMethod' error. If there is no requirement for the length of a month, don't implement it.

Second, remember to implement methods for basic behaviour such as coercing to a string and sorting values. Ruby's `Comparable` module is most useful when building value objects. Don't know the difference between `to_s` and `to_str`? Now is a good time to find out.

### Conclusion
Value objects allow you to define more of your program in the language of the domain. The objects you create should be specific to your domain. This means they are not necessarily reusable. If my project is UK based, then January is in winter. In Australia `January.winter?` should return `false`, and if my buisness is international then maybe we shouldn't be deciding the season on month alone.

A downside to using value objects for everything is that you need to build them, which takes time. It's just so much easier to use the ever versatile string. Even then, the time spent clarifying these concepts into dedicated objects helps with understanding the domain — this saves time in the long run.

Input should be transformed to domain objects as soon as possible. Form objects are a good way to do that and will be the next post.

*Have you been building with value objects? If so, let me know how it went and what you think of them.*

### Resources

Here are some great tutorials on value objects in Ruby:

- [Overcoming Our Obsession with Stringly-Typed Ruby](https://www.youtube.com/watch?v=7Obobjq8g_U)  
  An excellent video from [David Copeland](https://twitter.com/davetron5000) on overusing Ruby strings
- [String, the original value object](http://erniemiller.org/2012/11/01/ruby-tidbit-string-the-original-value-object/)  
  Article suggesting times when you should subclass String to create your value objects by [Ernie Miller](https://twitter.com/erniemiller)
https://github.com/tcrayford/Values
- [Using Value Objects](http://www.informit.com/articles/article.aspx?p=2220311&seqNum=11)  
  Using value objects as ActiveRecord attributes.
- [Value Objects Explained with Ruby](http://www.sitepoint.com/value-objects-explained-with-ruby/)  
  A thorough grounding in the concept of value objects with ruby.
- [Why Value Objects](http://thepaulrayner.com/blog/why-value-objects/) Quick recap on why use value objects form [Paul Rayner]()
- [5 steps to mistake proof software design](http://mozaicworks.com/blog/5-steps-to-mistake-proof-software-design/)  
  Ways to reduce mistakes number 5 being to avoid primitive obsession.
- [Typetanic](https://github.com/CrowdHailer/typtanic)  
  A repository of general value objects starting with email.
