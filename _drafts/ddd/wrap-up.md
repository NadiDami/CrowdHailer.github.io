---
layout: post
title: Domain Driven Design, Where the real value lies.
description: The line in the sand between domain code and the framework
date: 2015-08-20 16:56:05
tags: ruby design
author: Peter Saxton
---

Conclusion of [Domain Drive Design series](/2015/07/14/domain-driven-design-introduction.html) following on from [Interactors](/2015/08/16/introducing-interactors-to-represent-getting-stuff-done.html).

### Admission

This is the final post in this series, and I have an admission.
What has been discussed so far has not been  the whole story of domain driven design.

So far several useful patterns have been covered.
These make up the tactics of domain driven design.
However tactics should not be employed without a strategy. Without an understanding of the strategy you will end up with a cargo cult.
I did not realise the importance of the strategy when I started writing these articles.
And in my defence, even Eric Evans has said he wishes that he put more emphasis on the strategy in his [book](http://www.amazon.co.uk/Domain-driven-Design-Tackling-Complexity-Software/dp/0321125215) that started domain driven design.

### Explaining the strategy.
Domain driven design has much to say on the best environment for development.
Key is that all interested parties in a project are able to understand each other.
These parties could be developers, managers, client, designers, customers and more.
To ensue understanding, time should be spent to develop a domain specific language.
In addition barriers between those developing the software and the users of the software should be eliminated as much as possible.

Once this has been achieved the developers should not need to make assumptions and instead consult stakeholders when making decisions.
Implementing software is difficult enough and without distilled domain knowledge it has very little chance of solving the problem at hand.

Much of the strategy can be described as common sense but that doesn't mean it does not bear repeating or explicitly naming.
I think it is unhelpful that both the tactics and strategy of domain driven design are interlaced.
To encapsulate the core concepts of the strategy in Domain driven design I would like to try the following definition.

> **Knowledge Limited Programming:** The decision to not develop a piece of code until the situation requiring its development is known.  

### Revisiting the tactics

With an understanding of the strategy we can reexamine the patterns in a more informed context.
There are two distinct groups for the design objects.
First, those that represent concepts from the domain.
They are:

- [Entities](/2015/08/02/tackling-god-objects-in-ruby.html) - A domain object with an identity that can change over time, such as customer.
- [Values](/2015/07/15/value-objects-in-ruby.html) - A domain object without an identity separate its attributes e.g. price, discount code or address.
- [Interactors](/2015/08/16/introducing-interactors-to-represent-getting-stuff-done.html) - A domain object that represents an action or activity e.g. login, checkout, signup etc.

The rest with the purpose of protecting the core domain from technical concerns.

- [Form objects](/2015/07/23/application-border-control-with-ruby-form-objects.html) - Prevents the HTML form considerations effecting our domain
- [Repository](/2015/08/09/untangle-your-domain-model-from-the-database.html) - Prevents the database structure from penetrating the domain model

Without the strategy it is tempting to ask developers entities should look like.
With the strategy the answer is whatever makes most sense in the domain.
Of course there are many similarities between projects that allow common behaviour to be reused but the domain model should not be tied the libraries language or layout.
My mantra for employing the tactics of domain driven design would be.

> Use domain concepts not language constructs.

I have written gems to help with value objects and form objects, both are small so they encroach as little as possible on the domain.

- [Typetanic](https://github.com/CrowdHailer/typetanic) A library of value objects to help start a project, at the moment just email. As your domain grows richer you should throw out the Typetanic version and replace with custom implementations.
- [Vulcanize](https://github.com/CrowdHailer/vulcanize) A library for building form objects. Domain representation is handled by value objects that the consumer needs to provide.

### Rails, friend or foe?
When I started this exploration I tried to push as far away from rails conventions as possible, not due to any dislike of rails but to get the perspective that comes with distance.
With Rails I can also see a strategy, the Rails way, and tactics, all the Active* Gems.
Their strategy as I see it is to define as much as possible that is common across a certain set of applications and provide that as a default without any thought needed on the developers part.
This is exactly the opposite of defining a language specific your problem, it assumes your problem is using that common core and if that is the case allows a starting speed that is hard to argue with.
I am of the opinion that The Rails Way is incompatible with the strategy of Domain Driven Design.

To implement this strategy the Rails stack uses well defined MVC architecture.
MVC offers a nice way to break up an application into logically distinct layers, a model, a view and a controller.
Three helpful pieces, however as an application grows each piece grows and you end up with 3 big pieces.
Obviously we want to break into more pieces and the question becomes where.
MVC does not prevent you have a model layer made from many small decoupled pieces but it doesn't care what these pieces are.
This is not a problem of MVC but does mean that it provides no help in deciding how you should decompose your model layer.
The patterns from domain driven design do however help with breaking down the domain model.
It is possible to use the rails stack as a tool set to build the delivery mechanism for your domain model.
I am of the opinion that The Rails Stack is a great tool set to build a delivery mechanism and that if it is a good fit you should definetly use it.
A well separated domain means that if you discover that the rails stack is no longer a good fit it should be possible to leave rails without a rewrite of your application.

My personal journey with rails went through a prolonged period of frustration as I did not understand when to move away from the prescribed path.
After a time I stopped using rails all together and used smaller frameworks such as sinatra, scorched and padrino.
Once I started treating rails as a service to my domain and not a foundation I became much less pro or anti rails. The qustion was not as important as It was optional.

As a new developer because it did everything I needed I was not happy when I found things it did not help me with or that it got in the way of because I had left the rails way.
To anyone who has not experienced pain with rails yet just remember
- A model is not made of models
- A view is not made of views
- and controllers are not inherited from controllers

### Alternatives

What about Domain driven design compared to hexagonal rails and clean architecture?
Domain driven design the tactics have similarities with both of these.
Clean architecture makes use of interactors to separate a delivery mechanism from a core full of entities.
Hexagonal rails talks of ports to be filled by adpaters, the ui adapter is the delivery mechanism and a database adapter will be encapulated in a Repository.

The strategic side of Domain driven design is embodied less in the other two.
They suggest implementation of patterns for the technical benefits that will follow.
Your code is more extensible, more maintainable easier to test and separate into microservices.
These improvements will of course lead to faster development cycles and happier clients/customers but the emphasis is on the technical wins.
I think this emphasis is important because DDD gives an easier dialogue when deciding not to implement any of the advanced patterns, such as the following hypothetical conversation.

> After tharashing out the details we discovered that what the client wanted was a rails app, so we build a rails app.

### Where next


I have been developing an example ddd project, it is a todo list as I feel that the strategy of DDD should be applicable to a project of any size. There should not be a day in you project where the complexity flips past a point where DDD starts. It is a work in progress and I would like to explore some of the tactics that I did not have time for this time round. If you want to ask about something an issue on this repo would be great.

A few final thoughts that might be DDD or not

- If there is such a thing as strings in your domain there length method should return a number of centimeters
- prefer vertical grouping, section not layers
- Do not redesign code for tests but if its hard to test put that as one red mark
- Design patterns are most useful in creating a language. Language is important.
  I want to be able to discuss about value objects without explaining about them first. A value object is an object with no history. An entity is an object with durable identity and not something that subclasses ActiveEntity.
- I now have one remain peeve with rails that I feel is justified. I dislike if for convolution the 'Active record' pattern with the 'ActiveRecord' implementation.


- [The Two Sides of Domain-Driven Design (DDD)](http://gorodinski.com/blog/2013/03/11/the-two-sides-of-domain-driven-design/#.VdO9AIJSIUA.twitter)  
  This post from [Lev Gorodinski](https://twitter.com/eulerfx) explores Domain-Driven Design from the tactical and strategic perspectives
- [Clean Architecture](https://subvisual.co/blog/posts/20-clean-architecture)  
  A really comprehensive article on employing clean architecture with the rails stack by [Luis Zamith](https://twitter.com/zamith)
- [Eric Evans on Domain-Driven Design at 10 Years](http://www.se-radio.net/2015/05/se-radio-episode-226-eric-evans-on-domain-driven-design-at-10-years/)  
  Really interesting chat with [Eric Evans]() on the 10 years of Domain Driven Design since his book was released.
