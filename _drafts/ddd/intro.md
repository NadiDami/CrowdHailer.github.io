---
layout: post
title: A persistance problem
description: Solutions with entities, Repositories and ruby
date: 2015-04-23 17:20:05
tags: ruby design
author: Peter Saxton
---

##titles
last 18months I have been programming properly. I havd programmed for some time before that but not with careful consideration.
found the rails way
A model is not made of models
A view is not made of views
and controllers are not inherited from controllers
Pushing logic below the model layer

This series has been through a number of titles.
One was Discovering the Joy of Over design.
This is I think apt because alot of the things discussed here are far from necessary, if you have a separate view object(not template), interactor, repository entity and data layer for a simple list of all entries then that is plainly overkill. I started this process with a desire to deliberatly over design and see which ones made sense. I discovered that in alot of cases several of these patterns made sense. There may be issues with scaling when all of these are implemented but issues with scaling are exactly the issues I want to have. A question that is often asked is do I put these in from the beginning. The answer is yes. Everything the same is valuable

The problem with misunderstood MVC. Breaking things into small pieces is pretty universally accepted as a good idea. MVC offers a nice way to break up an application into logically distinct layers. A model, a view and a controller. Three nice pieces, However as an application grows each piece grows and you end up with 3 big pieces. Obviously we want to break into more pieces and the question becomes where. This is not a problem with MVC there is noting in MVC that prevents you have an M made from 10 nice pieces but it doesn't care what these pieces are. It also means it is no help in deciding what these pieces should be.

The problem with rails. Rails is an excellent too which has a problem that because it does everything you need for so long you don't know when to leave.

A note on gems
For alot of the concepts I explore here I have written gems/libraries. Some of them I use in production. Some of them where pulled out of production systems. None of them have been deployed in more than 2 systems. They should be considered as additional reading, at the moment they are not encumbered with code to handle rare edge cases. What this means is if you use them they will not handle your edge cases.

Topics are
1 Value Objects - TypeTanic
subclass TypeTanic
2 Form objects and validators - Explore the idea of forge/ Don't just new your objects forge them in the heat of requirements
Do forms belong withing usecases???
3 Persistance - Not that model
3.1 data - the real active record
3.2 entity - theres no state here
3.3 repository - finding the answers
4 service objects
5 pulling it all together interactors
6 presenter vs view || model up vs user down

## Round up
#### Layout
In the blog application that I have mentioned along with this series I have tried to push as far away from rails conventions as possible. No due to any dislike of rails but to get some perspective with distance.
For example I group templates with controllers before I group controllers together.
I am not sure its a better way but I definetly learnt alot exploring it. What I am sure about is that you could blindly copy my layout and work off it and if you did you would end up just as much in a muddle as following any other layout blindly

lessons
same lessons for non basic collections as stringly
prefer vertical grouping
Do not redesign code for tests but if its hard to test put that as one red mark
Design patterns are most useful in creating a language. Language is important.
I want to be able to discuss about value objects without explaining about them first. A value object is an object with no history. An entity is an object with durable identity and not something that subclasses ActiveEntity.
I think that template better describes the erb files we have and view is better for the page object. However with the history view is missleading and viewModel is just a disaster.
I now have one remain peeve with rails that I feel is justified. I dislike if for convolution the 'Active record' pattern with the 'ActiveRecord' implementation.

separation has to be complete or it is not useful at all. If two pieces of code have just one weird interdepencey then changing either can break the other. Only it will happen less often so be more distructive when you create that new bug.
I like the separation of the the brains of the application from the delivery method. To insist on it I enter the console using
```
$ irb -r ./config/boot
# NOT
$ irb -r ./config/application
```

and I also run the following
NB after commiting
```
$ rmdir app test/app
$ rake test
$ git reset --hard HEAD
```

questions
expand model with decorators or split views into templates and view models


covers quickly the three building blocks, entities, value objects, and services
http://www.iain.nl/domain-driven-design-building-blocks-in-ruby
