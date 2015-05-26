---
layout: post
title: Ruby require considered harmful
description: "What's wrong with Ruby's require and the bad code it creates"
date:   2015-05-26 13:02:30
tags: opinion ruby
author: Peter Saxton
---

### Overview

In Ruby the require method is used to include code from other files, as such it is an integral part of adding structure to a project. It can be used to load a gem or a ruby file. A single item may only be required once. To quote directly from the [ruby documentation](http://ruby-doc.org/core-2.2.2/Kernel.html#method-i-require)

> **require(name) → true or false**
>
> Loads the given name, returning true if successful and false if the feature is already loaded.

The required file is executed in the global namespace so any modules/classes etc that are defined in the required file are available to the program that called on them.

~~~rb
OpenStruct
# ! NameError: uninitialized constant Openstruct

require 'ostruct'
# => true

 OpenStruct
# => OpenStruct
~~~

This all sounds very simple and intuitive so what is the problem?

### Monkey-patching

The problem comes when what you are requiring is not exactly what you need. If the code is from a gem then you can't quickly make changes to it. Fortunetly Ruby is very flexible and you can just monkey-patch on the methods you need. Lets clarify this with an example the [money gem](https://github.com/RubyMoney/money). I have a project that works with several currencies some are accepted and some are not. It would be nice to have a method saying if a given money instance was in an accepted currency. We can easily implement one.

~~~rb
require('money')

class Money
  def accepted?
    ACCEPTED_CURRENCIES.include? self.currency
  end
end
~~~

This is very clear so why am I saying it is a problem. Maybe the 'accepted?' method was already defined and other code using this class is now broken. Don't forget we have changed the Class everywhere. In addition was a very clean value object now has an odd piece of business logic included. The accepted fix for this is to instead to subclass or use a wrapper/adapter class.

~~~rb
require('money')

class MyMoney < Money
  def accepted?
    ACCEPTED_CURRENCIES.include? self.currency
  end
end
~~~

Why I don't like this. I don't like the fact that my new class cannot have the name money. In my project I never want to use the original money class and only use the subclass, as that encapsulates the concept of money in my domain. Also if I ever do type Money.new instead of MyMoney.new there is now error as the completly unwanted class is defined.

### Other languages

#### Python

The equivalent process in Python is import. This has several options above the ruby functionality the relevant one being 'as' which allows you to rename imports.

~~~py
import money as base_money

# Now extend base_money
~~~

The money variable is now yours to use anyway to wish or indeed not at all.

#### JavaScript

Possibly even better is the node require function which allows code to be imported anonymously

~~~js
var money = require('money').extend({
  is_available: function () {
    // Code goes here
  },
})
~~~

The money variable is the one we want and there is no way to access the base object without rerequiring it.

### A possible solution

I have recently been building a collection of types such as email for use in my projects. to tackle exactly this issue I have put them in a silly namespace. It's not quite anonymous but it is hopefully not one I will ever need to use.

~~~rb
require 'typetanic/email'

class Email < Typetanic::Email
  # Anything here, for example
  def company_email?
    self.domain == 'workshop14.io'
  end
end
~~~

### Final thoughts

I would like to see ruby move in the direction laid out by [Adam Hawkins](http://hawkins.io/2015/05/the-ruby-community-the-next-version/) with a preference for small libraries. I think the JavaScript require system is integral to the current trend of small, focused and highly reusable modules they have. It's a huge change but I would rather place the required code than have the gem assume it knows where it should go.

~~~rb
Money = require 'money'

# and

class Country < require 'country'
  # Custom code
end
~~~

In the meantime the Ruby Money and Countries gems are among my favorite because of their focus. However with the current system I would rather they created the classes `Gaia::Countries` and `MonopolyMan::Money`.
