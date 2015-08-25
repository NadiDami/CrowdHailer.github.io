---
layout: post
title: Domain Driven Design, Where the real value lies.
description: Seeing the tactics and the strategy in Domain Driven Design
date: 2015-08-20 16:56:05
tags: Ruby Design
author: Peter Saxton
---

Conclusion of [Domain Drive Design series](/2015/07/14/domain-driven-design-introduction.html) following on from [Interactors](/2015/08/16/introducing-interactors-to-represent-getting-stuff-done.html).

### Admission

This is the final post in this series, and I have a confession.
What has been discussed so far has not been the whole story of Domain Driven Design.

So far several useful patterns have been covered.
These make up the tactics of Domain Driven Design.
However tactics should not be employed without a strategy. Without an understanding of the strategy you will end up with a cargo cult.
I did not realise the importance of the strategy when I started writing these articles.
And in my defence, even Eric Evans has said he wishes that he put more emphasis on the strategy in his [book](http://www.amazon.co.uk/Domain-driven-Design-Tackling-Complexity-Software/dp/0321125215) that started Domain Driven Design.

### Explaining the strategy.
Domain Driven Design has much to say on the best environment for development.
Key is that all interested parties in a project are able to understand each other.
These parties could be developers, managers, client, designers, customers and more.
To ensure understanding, time should be spent to develop a domain specific language.
In addition barriers between those developing the software and the users of the software should be eliminated as much as possible.

Once this has been achieved the developers should not need to make assumptions and instead consult stakeholders when making decisions.
Implementing software is difficult enough and without distilled domain knowledge it has very little chance of solving the problem at hand.

Much of the strategy can be described as common sense but that doesn't mean it does not bear repeating or explicitly naming.
I think it is unhelpful that both the tactics and strategy of Domain Driven Design are interlaced.
To encapsulate the core concepts of the strategy in Domain Driven Design I would like to try the following definition.

> **Knowledge Limited Programming:** The decision to not develop a piece of code until the situation requiring its development is known.  

### Revisiting the tactics

With an understanding of the strategy we can reexamine the patterns in a more informed context.
There are two distinct groups for the design objects.
First, those that represent concepts from the domain.
They are:

- [Entities](/2015/08/02/tackling-god-objects-in-ruby.html) - A domain object with an identity that can change over time, such as customer.
- [Values](/2015/07/15/value-objects-in-ruby.html) - A domain object without an identity separate to its attributes e.g. price, discount code or address.
- [Interactors](/2015/08/16/introducing-interactors-to-represent-getting-stuff-done.html) - A domain object that represents an action or activity e.g. login, checkout, signup etc.

The rest with the purpose of protecting the core domain from technical concerns.

- [Form objects](/2015/07/23/application-border-control-with-ruby-form-objects.html) - Prevents the HTML form considerations effecting our domain
- [Repository](/2015/08/09/untangle-your-domain-model-from-the-database.html) - Prevents the database structure from penetrating the domain model

Without the strategy it is tempting to ask developers what entities should look like.
With the strategy the answer is whatever makes most sense in the domain.
Of course there are many similarities between projects that allow common behaviour to be reused but the domain model should not be tied to a library's language or layout.
My mantra for employing the tactics of Domain Driven Design would be:

> Use domain concepts not language constructs.

I have written gems to help with value objects and form objects, both are small so they encroach as little as possible on the domain.

- [Typetanic](https://github.com/CrowdHailer/typetanic) A library of value objects to help start a project, at the moment just email. As your domain grows richer you should throw out the Typetanic version and replace with custom implementations.
- [Vulcanize](https://github.com/CrowdHailer/vulcanize) A library for building form objects. Domain representation is handled by value objects that the consumer needs to provide.

### Rails: friend or foe?
When I started investigating Domain Driven Design, I tried to push as far away from Rails conventions as possible.
This was not due to dislike of Rails but to get the perspective that comes with distance.

Within Rails there is a strategy and tactics, the "Rails way" and the "Rails stack" consisting of all the "Active* Gems".
The strategy is to define what is common across applications. Then to provide that core as a default without any thought needed on the developers part.
This is exactly the opposite of defining a domain language specifically for your problem.
I am of the opinion that the "Rails Way" is incompatible with the strategy of Domain Driven Design.

Rails strategy is implemented with a well defined MVC architecture.
MVC provides a clear way to break up an application into the three layers.
However, there is no help in deciding how to break down an application further.
The patterns from Domain Driven Design do however help with breaking down the domain model.
It is certainly possible to use Domain Driven Design to break down a model with an MVC application.
I am of the opinion that the "Rails Stack" is compatible within a domain driven application.

### Other views

What about Domain Driven Design compared to hexagonal rails and clean architecture?
The patterns of DDD are represented in both of these architectures.
For example, clean architecture makes use of interactors to separate a delivery mechanism from a core of entities.
Hexagonal Rails talks of ports in the domain for infrastructure adapters. The UI adapter is the delivery mechanism and a database adapter could be encapsulated by a repository.

The strategic side of Domain Driven Design is emphasised less in the other two.
Patterns are suggested for technical benefits.
Code is more extensible, more maintainable; easier to test.
These improvements will of course lead to faster development cycles and happier clients/customers but the emphasis seems to be on the technical wins.
I think this emphasis is important because DDD has a helpful dialogue on when not to implement any of the advanced patterns.
Take for example the following hypothetical conversation.

> After thrashing out the details we discovered that what the client wanted was a Rails app, so we build a Rails app.

This discussion focuses on the core of the Domain Driven Design strategy.

### What's next

I have been developing an [example ddd project](https://github.com/CrowdHailer/just-job) - it is a to-do list.
It may be small but the strategy of DDD should be applicable to a project of any size. It is a work in progress and would be a great place to discuss DDD. Just open an issue if you think I can do it better.

A few parting thoughts on lessons I have learned:

- If there is such a thing as strings in your domain their length method should return a number in centimeters
- Prefer vertical grouping, section not layers
- Do not redesign code for tests but if it's hard to test, that is a warning
- Design patterns are most useful for communication between humans

### Resources

- [The Two Sides of Domain-Driven Design (DDD)](http://gorodinski.com/blog/2013/03/11/the-two-sides-of-domain-driven-design/#.VdO9AIJSIUA.twitter)  
  This post from [Lev Gorodinski](https://twitter.com/eulerfx) explores Domain-Driven Design from the tactical and strategic perspectives
- [Clean Architecture](https://subvisual.co/blog/posts/20-clean-architecture)  
  A really comprehensive article on employing clean architecture with the Rails stack by [Luis Zamith](https://twitter.com/zamith)
- [Eric Evans on Domain-Driven Design at 10 Years](http://www.se-radio.net/2015/05/se-radio-episode-226-eric-evans-on-domain-driven-design-at-10-years/)  
  Really interesting chat with [Eric Evans](https://twitter.com/ericevans0) on the 10 years of Domain Driven Design since his book was released.
- [Just Job](https://github.com/CrowdHailer/just-job)  
  A to-do list built from the ground up using the principles of Domain Driven Design(DDD)
- [Dicovering the joy of over design](/2015/07/14/domain-driven-design-introduction.html)  
  The beginning of these articles, getting started with Domain Driven Design.
