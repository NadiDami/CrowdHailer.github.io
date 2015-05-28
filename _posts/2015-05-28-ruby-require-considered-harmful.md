---
layout: post
title: Ruby require considered harmful
description: What's wrong with Ruby's require and the bad code that follows
date:   2015-05-28 07:05:00
tags: opinion ruby
author: Peter Saxton
---

### Overview

In Ruby the require method is used to include code from other sources. As such it is an integral tool in adding structure to a project. It can be used to load a gem or a ruby file and each item is required only once. From the [ruby documentation](http://ruby-doc.org/core-2.2.2/Kernel.html#method-i-require)

> **require(name) → true or false**
>
> Loads the given name, returning true if successful and false if the feature is already loaded.

To make available any modules/classes that are defined in the required file it is executed in the global namespace. This means anything in the program can be modified by the required file including the standard library.

This all sounds very simple and intuitive so what is the problem?

### Monkey-patching

The problem comes when what you are requiring is not exactly what you need. If the code is from a gem then you can't quickly make changes to it. Fortunately Ruby is very flexible and you can just monkey-patch on the methods you need.

Let's clarify this with an example the [money gem](https://github.com/RubyMoney/money). I have a project that works with several currencies-some are accepted and some are not. It would be nice to have a method saying if a given money instance was in an accepted currency. We can easily implement one as follows.

{% highlight ruby %}
require('money')

class Money
  def accepted?
    ACCEPTED_CURRENCIES.include? self.currency
  end
end
{% endhighlight %}

This is very clear so why am I saying it is a problem? Maybe the 'accepted?' method was already defined and other code using this class is now broken. Don't forget we have changed the class everywhere. In addition what was a very clean value object now has an unexpectedly sophisticated level of knowledge about our business.

The accepted fix for this is to instead subclass it or use a wrapper/adapter class.

{% highlight ruby %}
require('money')

class MyMoney < Money
  def accepted?
    ACCEPTED_CURRENCIES.include? self.currency
  end
end
{% endhighlight %}

Domain knowledge has now not leaked into the core class and we can use that core class if we ever need to use the old implementation of accepted.

However I don't like the fact that my new class cannot have the name 'money'. In my project I never want to use the original money class and only use its subclass. The subclass encapsulates the concept of money in my domain, whereas the parent was just a helpful stepping stone to making that class.

### Other languages

#### Python

The equivalent process in Python is import. This has several extensions above the ruby functionality. The relevant one being 'as' which allows you to rename imports.

{% highlight python %}
import money as base_money

# Now extend base_money
{% endhighlight %}

The money variable is now free to use anyway you wish or indeed not at all.

#### JavaScript

Possibly even better is the node 'require' function which allows code to be imported anonymously.

{% highlight javascript %}
var money = require('money').extend({
  is_available: function () {
    // Code goes here
  },
})
{% endhighlight %}

The money variable is the one we want and there is no way to access the base object without rerequiring it.

### A possible solution

I have recently been building a collection of types such as email for use in my projects. To tackle exactly this issue, I have put them in a silly namespace. It's not quite anonymous but hopefully I will not ever need to use the [Typetanic](https://github.com/CrowdHailer/typtanic) namespace for anything else.

{% highlight ruby %}
require 'typetanic/email'

class Email < Typetanic::Email
  # Anything here, for example
  def company_email?
    self.domain == 'workshop14.io'
  end
end
{% endhighlight %}

### Final thoughts

I would like to see ruby move in the direction laid out by [Adam Hawkins](http://hawkins.io/2015/05/the-ruby-community-the-next-version/) with a preference for small libraries. I think the JavaScript 'require' system is not only enabling but encouraging the current trend of small, focused modules in that community.
It would be a huge change but I would rather name my required objects than have the gem assume it knows what I want them to be.

{% highlight ruby %}
Money = require 'money'

# and

class Country < require 'country'
  # Custom code
end
{% endhighlight %}

In the meantime the Ruby Money and Countries gems are among my favorite because of their focus. However with the current system I would rather they took up a fun namespace and left the business domain to me.
Perhaps `Gaia::Countries` and `MonopolyMan::Money`.
