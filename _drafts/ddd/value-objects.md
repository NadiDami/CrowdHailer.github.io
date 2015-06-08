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

Let us imagine the system is asking about months and seasons and we want to implement the `winter?` method how do we do that. Well we can reopen the string class and monkey patch in a `winter?` method. However as we can see the question is it winter doesn't make sense to 'B-'. So this method can't be added to the string class.

Now DDD had an answer to this all along. Our problem domain doesn't have strings it has months. When talking to stakeholders no one ever said 'then the user enters the string of there birthday', its month of there birthday. And thus the answer is to create a Month object to encapsulate the data and behavior of what our what a month means to our system. With that concept added to our program we can write code that looks like the following.

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

The solution to our problem is to create a value object for our program that represents a month.

### What are value objects?

A value object is any object who's identity is characterized by it's attributes. Ruby primitives such as `String`, `Integer`, `DateTime` are all value objects. It is not possible for value objects to have a history, any instance of our Month class should be treated as exactly the same regardless of source Lets clear this up with an example. For example with strings.

```rb
# {% highlight ruby %}
string1 = 'hello'
string2 = 'hello'

string1 == string2
# => true
# {% endhighlight %}
```

This is in contrast to entities that can have an identity that is based on more than there attributes. For example a user object that has only a single attribute.

```rb
# {% highlight ruby %}
user1 = User.new :name => 'Peter'
user2 = User.new :name => 'Peter'

user1 == user2
# => false
# {% endhighlight %}
```

Entities can have a history user2 can update there name and they will still be user2.

### Using value objects.
Values can be used in place of a Ruby primitive anywhere your problem domain specifies extra behavior. In recent projects I have discovered this means almost everywhere there is a primitive conceptual gains can be made by changing to a value object.
The classic extra behavior is validations. If a username must be at least 3 characters then it is not just a string. If an quantity cannot be negative then it is not simply an integer.


Input should will always come in as strings and should be wrapped as soon as possible and converted back to a string as far as possible.

### Building a value object
Implementation is up to the programmer and as long as the interface is passing all your tests you should not care whats going on inside. However there are some things to bear in mind when building your value objects.

First think hard about subclassing the primitive you are trying to escape when building your domain object. It can implement things that might not make any sense any more. For example lets extend our Month class so it can be initialized with 'Jan' but still keeps the internal string representation as 'January'. We now have this code.

```rb
# {% highlight ruby %}
# if subclassed from String
january = Month.new 'Jan'
january.length
# => 7
# {% endhighlight %}
```

In reality we probably want the length of January to be 31days, or no method. If there is no requirement for the length of a month don't implement it.

Second remember to implement basic ruby methods as needed. Don't know the difference between `to_s` and `to_str`? now is a good time to find out.

### Conclusion
Value objects allow you to define more of your program in the language of the domain. Most importantly The objects you create should be specific to your domain this means they are not necessarily reusable.

If my project is UK based then January is in winter. I Australia the answer should be false and my buisness is international then maybe we shouldn't be deciding the season on month alone.

I think value objects had the greatest impact on the code I write. I think this is not because they are the most important but because they are often neglected. It is obvious you don't want to model each user as a gigantic hashes. This quickly become cumbersome and extra behavior is more. It is however much easier to leave usernames represented as strings because creating a string is just so convenient.

A downside to using value objects for everything is that you need to build them, which takes time. It's just so much easier to use the ever versatile string. Even then the time spent clarifying these concepts into dedicated objects helps with understanding the domain that saves time in the long run.

So have you used value objects. Let me know how it went and what you think.

### Resources

There are some great tutorials out there are on value objects in ruby, as well as when to use them. So check out these articles for another point of view on them. I want to build on top of these value objects so lets quickly introduce them.
- [Overcoming our obsession with stringly typed Ruby](https://www.youtube.com/watch?v=7Obobjq8g_U)  
  An excellent video from [David Copeland](https://twitter.com/davetron5000) on overusing Ruby strings
- [String, the original value object](http://erniemiller.org/2012/11/01/ruby-tidbit-string-the-original-value-object/)
  Article suggesting times when you should subclass string to create your value objects by [Ernie Miller](https://twitter.com/erniemiller)
https://github.com/tcrayford/Values
- [Using VAlue Objects](http://www.informit.com/articles/article.aspx?p=2220311&seqNum=11)
  Using value objects as ActiveRecord attributes.
- [Value Objects Explained with Ruby](http://www.sitepoint.com/value-objects-explained-with-ruby/)
  A thorough grounding in the concept of value objects with ruby.
- [Typetanic](https://github.com/CrowdHailer/typtanic)
  My repository of value objects that I have found do cross between projects.
