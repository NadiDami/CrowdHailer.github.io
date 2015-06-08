---
layout: post
title: Building on Solid foundations
description: Moving from your tools to your domain.
date: 2015-07-23 17:20:05
tags: ruby design
author: Peter Saxton
---

A fundamental tenant of domain driven design(DDD) is that you create software in an environment rooted in the problem domain. Before prescribing some new objects, I would like to start with a question. Which of these lines of code should return true and which should return false?

{% highlight ruby %}
'January'.winter?
'password'.secure?
'B-'.pass?
{% endhighlight %}

Perhaps you got it, perhaps not. Lets make it clearer with some small changes.

{% highlight ruby %}
'January'.secure?
'password'.pass?
'B-'.winter?
{% endhighlight %}

The answer is neither. Every line will, as it should, raise a `NoMethodError`. These two code samples have exactly the same outcome and yet they read very differently to us as humans. In the first example we see 'January' as a month and so make sense of the question is it winter.

Let us imagine the system is asking about months and seasons and we want to implement the `winter?` method how do we do that. Well we can reopen the string class and monkey patch in a `winter?` method. However as we can see the question is it winter doesn't make sense to 'B-'. So this method can't be added to the string class.

Now DDD had an answer to this all along. Our problem domain doesn't have strings it has months. When talking to stakeholders no one ever said 'then the user enters the string of there birthday', its month of there birthday. And thus the answer is to create a Month object to encapsulate the data and behavior of what our what a month means to our system. With that concept added to our program we can write code that looks like the following.

{% highlight ruby %}
month = Month.new 'January'
month.winter?
# => true
{% endhighlight %}

And if we start putting in invalid months the program fails before we even ask the question 'is it winter?'
{% highlight ruby %}
month = Month.new 'B-'
# !! InvalidMonth
{% endhighlight %}



### What are value objects?
A value object is any object who's identity is characterized it's attributes. Ruby primitives such as `String`, `Integer`, `DateTime` are all value objects. The antithesis of value objects are entities, they have an identity beyond there attributes. Lets clear this up with an example.

value objects
```rb
string1 = 'hello'
string2 = 'hello'

string1 == string2
# => true
```

entity
```rb
user1 = User.new :name => 'Peter'
user2 = User.new :name => 'Peter'
user1 == user2
# => false
```
and most importantly user2 can update there name and still be user2.

### Building a value object
So I have announced there should be a month object but what should it look like.
One option is to subclass String when creating these new methods. I normally prefer being explicing and starting with a new class. We can show possible odd behaviour with out month implementation

```rb
# subclassed from String
january = Month.new 'January'
january.length
# => 7
```

### equality
if not just equality then comparison

### good methods to definetly
to_s
to_str

Almost certainly if you tell you buisness partner that january has a length of 7 they will not agree.

### Speed

One disadvantage is the speed of progress that is made when needing to specify all the details of each type that might be needed in a new application.
My leading principle is to work towards evergreen. i.e. don't rush at the beginning.
It is important to settle down for the long run, that said that people want to see progress quickly. Including ourselves as developers.

To combat this I have set up a library of types that I come across. It has or will have soon types for. emails, titles, names.

But wait surely the conditions on a title are different from project to project.
Yes but they don't all need to be in place from the off.
The typetanic title is full of some handy defaults. and if you want a new thing just write your own.

Good code means changing one thing.

So when developing values/title.rb looks like the following

```rb
class Title < Typetanic::Title

end
```

when our title has been specified
```rb
class Title
  # My code
  # It is a simple object. Just write one
end
```

My library is typetanic. It is open source and contributions are of course welcome.
but its real value is too me. Im not sure I want to create

```rb
class Title < Typetanic::String
  min_length 5
  max_length 10
  value_format /^[A-Z][a-zA-Z0-9\s]+$/
  capitalise true
end
```

it looks nice but i think is over kill

## Extensions

#### Forge protocol
The forge method can take any number of arguments to create a new value. It should also accept a block which is called with the error should creating the value object fail.

All typetanic types have the forge method implemented again to speed up development

#### stash protocol
this implements a dump and load method where the object can be reduced to primitive suitable for storage.
### Welcome to the Value objects
tl:dr

This post introduces value objects. I think they are a good idea. If you know what they are and agree then good. wait for the next article

There are some great tutorials out there are on value objects in ruby, as well as when to use them. So check out these articles for another point of view on them. I want to build on top of these value objects so lets quickly introduce them.
https://github.com/tcrayford/Values
http://erniemiller.org/2012/11/01/ruby-tidbit-string-the-original-value-object/
http://www.informit.com/articles/article.aspx?p=2220311&seqNum=11
http://www.sitepoint.com/value-objects-explained-with-ruby/
http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/
https://www.youtube.com/watch?v=7Obobjq8g_U

Comments
The most important sentance is 'in your problem'
