---
layout: post
title: A persistance problem
description: Solutions with entities, Repositories and ruby
date: 2015-04-23 17:20:05
tags: ruby design
author: Peter Saxton
---

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
