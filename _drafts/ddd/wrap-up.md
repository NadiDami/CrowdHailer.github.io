---
layout: post
title: Domain Driven Design, Where the real value lies.
description: The line in the sand between domain code and the framework
date: 2015-08-20 16:56:05
tags: ruby design
author: Peter Saxton
---

### Admission

A small admission to lead this post. I have been miss-leading you, what I have discussed so far has not been domain driven design, or at least not all of it.
Up to this point we have talked about a set of useful patterns to build applications, has been the tactics of domain driven design.
However should not employ any new tactics without a strategy, without an understanding of the strategy you have a cargo cult.
In my defense I did not realise the importance of this when I started writing these posts and even eric evans has said he wishes that he put more emphasis on the strategy in the book that started it all.


### So Explaining the strategy.
Domain driven design has much to say on the working environment of a project and the communication structure that is most helpful to development. Key is that all relevent parties in a projects development are speaking the same language, these stakeholders will be the developers, managers and client and may include designers, customers and more. The process of defining this shared language may not always be fast however it allows conversations to be had between any members of the team. This language might not always be easy to generate because business people might use terms that they are all familiar with yet have never had to critically define them. and the developers will need terms to be specific enough that the can be turned into classes and methods within the program.

This strategy leads to much of the technical objects we have discussed. We want to talk in the domain specific language. This is entities for anything that has an id and value objects to represent buisness objects and interactors to represent business activity.
An entity is crucialy a model for a concept that a non technical business expert can inherintly understand.
The should be able to look and each method defined on that entity and understand why each one exists.
Most of the remaing patterns are to help with this goal, form objects coerce input so that any artifacts from the fact the data was transfered as strings over http has gone. Repositories are best described as just a collection of entities, the fact they might be in a databse is irrelevant. the records they wrap are just an implemenation detail, if you changed to a datamapper the entities would not change on the surface and that would make complete sense as we would still be modelling the same domain.


###
Where entitises really shine is once you start making muliple for each record class from that point on there is now a path to handle any amount of business logic.  A user might have complicated authetication records with remember me tokens, password reset tokens, oauth this can all be farmed out to a credentials objects which. There might be a bunch of questions that where asked during signup make a Questionaire Entity that works off the same record
This pattern is not limited to database records, user sessions are often represented by a simple object that
Ideas we have not covered CQRS, bounded context, event sourcing

### and the rest
lets first revisit our consideration of rails.
I this series I have tried to push as far away from rails conventions as possible.
No due to any dislike of rails but to get the perspective that comes with distance, admittedly I was not a huge fan but I perhaps did not understand its strategy properly.
The rails strategy as I see it is to define as much as possible that is common across a certain set of applications and provide that as a default without any thought on the developers part.
As a new developer because it did everything I needed I was not happy when I found things it did not help me with or that it got in the way of because I had left the rails way.
To anyone who has not experienced pain with rails yet just remember
- A model is not made of models
- A view is not made of views
- and controllers are not inherited from controllers

What about Domain driven design compared to hexagonal rails and clean architecture?
Domain driven design the tactics have similarities with both of these. the stratigic side of things is not embodied in the other two. They give you pattern practises that you should adopt for the technical benifits that will follow. You code is more extensible, more maintainable easier to test and separate into microservices. These will of course lead to faster development cycles and happier clients/customers but the emphasis is on the technical wins. I think this emphasis is important because DDD gives an easier dialogue for not implementing any of the advanced patterns. After tharashing out the details we discovered that what the client wanted was a rails app, so we build a rails app.

My mantra for domain driven design to developers is 'Use domain concepts not language constructs, have each conversation only once'. for the non technical I would say 'have each conversation only once'



The problem with misunderstood MVC. Breaking things into small pieces is pretty universally accepted as a good idea. MVC offers a nice way to break up an application into logically distinct layers. A model, a view and a controller. Three nice pieces, However as an application grows each piece grows and you end up with 3 big pieces. Obviously we want to break into more pieces and the question becomes where. This is not a problem with MVC there is noting in MVC that prevents you have an M made from 10 nice pieces but it doesn't care what these pieces are. It also means it is no help in deciding what these pieces should be.


I have been developing an example ddd project, it is a todo list as I feel that the strategy of DDD should be applicable to a project of any size. There should not be a day in you project where the complexity flips past a point where DDD starts. It is a work in progress and I would like to explore some of the tactics that I did not have time for this time round. If you want to ask about something an issue on this repo would be great.

A few final thoughts that might be DDD or not
- If there is such a thing as strings in your domain there length method should return a number of centimeters
- prefer vertical grouping, section not layers
- Do not redesign code for tests but if its hard to test put that as one red mark
- Design patterns are most useful in creating a language. Language is important.
  I want to be able to discuss about value objects without explaining about them first. A value object is an object with no history. An entity is an object with durable identity and not something that subclasses ActiveEntity.
- I now have one remain peeve with rails that I feel is justified. I dislike if for convolution the 'Active record' pattern with the 'ActiveRecord' implementation.
-
best talks

east oriented


stringly
confident ruby
tell dont ask, eastward oriented code
sandy metz bath ruby talk on small talk



https://github.com/paulrayner/ddd_sample_app_ruby
https://github.com/emadb/ruby_loves_ddd
https://www.youtube.com/watch?v=rGA_FNew-6g
https://www.youtube.com/watch?v=CjNBnkMHjh4


Talk on DDD at 10. really good talk. explain that what i havent talked about bounded context. is the most important part. http://www.se-radio.net/2015/05/se-radio-episode-226-eric-evans-on-domain-driven-design-at-10-years/
