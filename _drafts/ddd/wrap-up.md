---
layout: post
title: Domain Driven Design, Where the real value lies.
description: The line in the sand between domain code and the framework
date: 2015-08-20 16:56:05
tags: ruby design
author: Peter Saxton
---

### Admission

A small admission to lead this post. What has been discussed so far has not been domain driven design, or at least not all of it.
Up to this point we have talked about a set of useful patterns to build applications, these are the tactics of domain driven design.
However tactics should not be employed without a strategy. Without an understanding of the strategy you will end up with a cargo cult.
In my defense I did not realise the importance of the strategy when I started writing these posts.
It also turns out that even Eric Evans has said he wishes that he put more emphasis on the strategy his book that introduced domain driven design.

### Explaining the strategy.
Domain driven design has much to say on the working environment of a project and the communication structure that is most helpful to development.
Key is that all interested parties in a project's development are speaking the same language.
These parties will be the developers, managers, client and may include designers, customers and more.
The process of defining this shared language may not always be fast but once uncovered it allows conversations to be had between any members of the team.
There are many reasons why this language might not be easy to define.
Business people use terms that they understand even without a critical definition.
Developers however need terms to be specific enough that the can be turned into classes and methods within the program.

Once this has been achieved the development should not need to make assumptions as the domain experts can be consulted whenever this is ambiguity to be resolved.
Implementing software is difficult enough and without distilled domain knowledge it has very little chance of solving the problem at hand.
I think it is unhelpful that both the tactics and strategy of domain driven design are interlaced.
Much of the strategy can be described as common sense but that doesn't mean it does not bear repeating or explicitly naming, to capture some of the core concepts of the strategy I would like to try the following definition.

> **Knowledge Limited Programming:** The decision to not develop a piece of code until you know the situation that is requiring its development.  
> not technical must know a business

### Revisiting the tactics

With an understanding of the strategy we can explore the patterns see so with more context.
Development should be discussed using the domain specific language that time was invested in to create.
We can split our tactics into patterns that represent concepts from the domain.

- Entities - A domain object with an identity that can change over time, such as customer.
- Values - A domain object that's identified by its attributes e.g. price, discount code or address.
- Interactors - A domain object that represents an action or activity e.g. login, checkout, signup etc.

Or patterns that protect those domain concepts from technical concerns.

- Form object - Prevents the html form considerations effecting our domain
- Repository - Prevents the database language from penetrating the domain model

It is tempting to focus on these tactics and to ask other developers what should my entities look like your project.
The answers to this can only be a suggestion as they should look like whatever makes most sense in the domain.
I have tried writing gems for most of these concepts but they have necessarily been very minimal.

In the readme for [Typetanic]()(my library of value objects) I have said if these do not match then you should throw them away and not try to extend them.  
[Vulcanize]()(my library for building form objects) handles the domain representation by value objects that the consumer needs to provide while it handles the HTML form side of things which is a technical consideration.

> My mantra for domain driven design to developers is 'Use domain concepts not language constructs, have each conversation only once'. for the non technical I would say 'have each conversation only once'


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



https://www.youtube.com/watch?v=rGA_FNew-6g
https://www.youtube.com/watch?v=CjNBnkMHjh4


Talk on DDD at 10. really good talk. explain that what i havent talked about bounded context. is the most important part. http://www.se-radio.net/2015/05/se-radio-episode-226-eric-evans-on-domain-driven-design-at-10-years/
