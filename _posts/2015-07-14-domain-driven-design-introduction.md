---
layout: post
title: Discovering the joy of over design
description: Introducing Domain Driven Design with Ruby.
date: 2015-07-14 17:20:05
tags:
- Ruby
- Domain Driven Design
author: Peter Saxton
---

### Beginning with design

Ruby has been my language of choice for about 18 months now. In that time the programs I have worked on have become larger. Considering design is a priority in larger programs. To improve, I have had to learn the difference between hacking code and designing an application.

There are many good sources that can help when designing applications and some of the best are included in the links at the end of this post. The design ideas that have influenced me the most are the concepts and practices from Domain Driven Design (DDD). This series of blog posts is aimed towards sharing what I have learned from DDD.

When you first explore DDD, it can seem as though there is minimal concrete advice for you to take away. However I think it is full of extremely well thought-through, practical advice. It is important to realise DDD is a large topic. This is because it acknowledges that software does not exist in isolation — it takes a complete world view. From this foundation are derived best practices in design patterns. In the first few posts I will cover the most common of these patterns, such as value objects and form objects.

As a developer I have many times tried to separate the technical issues of writing code from the non-technical concerns of the wider project. DDD encourages me to consider both parts together. Non-technical stakeholders are present throughout the life of a project and their collaboration can help clarify problems at many stages of the development process. The final part of this series will discuss how the strategies of DDD helps this collaboration.

### Useful Terms

I will expand on them further but to get started some brief summaries of the most important terms.

**Model View Controller (MVC)** is the architecture of Rails and as such something that most Ruby developers are very familiar with. This architecture simply breaks the code into layers. The part describing the system data and state belongs in the Model, all presentation belongs in the View and the Controller connects the other two. This is a simple architecture that is very accessible. It can lack the sophistication required to help organise complex problems. It was while I struggled with what constituted a model in MVC that I started looking for other ways of thinking.

**Domain driven design (DDD)** is one of several 'frameworks' to add structure to your thinking. The idea is to add abstractions to your program that mean it can be discussed using the terms familiar to the non technical stakeholders, people who care about what your software is doing, usually a user or client often both. It has more similarities than differences with 'hexagonal architecture' and 'clean code'. Reading about any of these will expose you to good ideas that will compliment what I want talk about here.

**Test driven development** is the discipline of writing tests before the code that will make those tests pass. The argument between test first and test after is not important. All that is required of tests, in my opinion, is that as a developer you are confident your code works.

### What's coming?

It is widely accepted that modularity in code is beneficial. It allows developers to understand a large complex program one manageable piece at a time. MVC helps with modularity by providing a way to break a program into 3 basic layers. However, if a program is very large then when separated into three, each piece is still large.

The principles of Domain Driven Design help add more lines of division to a program. This more sophisticated way to add separation can be used to create an arbitrary number of components until they are again understandable. I will begin with some of the patterns promoted in DDD, such as the Repository and Value objects. I will also discuss how and when to use them, as well as how I test them. The patterns discussed are,

1. [Value objects](/2015/07/15/value-objects-in-ruby.html) - Object Oriented Programming all the way down
2. [Form objects](/2015/07/23/application-border-control-with-ruby-form-objects.html) - Keeping the real world from making a mess
3. [Entities & Records](/2015/08/02/tackling-god-objects-in-ruby.html) - Keeping track of the state of affairs
4. [Repositories](/2015/08/09/untangle-your-domain-model-from-the-database.html) - Finding the answers using data
5. [Interactors](/2015/08/16/introducing-interactors-to-represent-getting-stuff-done.html) - Putting it all together

There are many patterns I don't have time to discuss so I have picked the ones I find most useful. To finish I will discuss the strategy behind the DDD in general terms and compare it to other design thinking in the Ruby community, such as "hexagonal architecture" and "The Rails Way".

1. [Strategy of Domain Driven Design](/2015/08/20/domain-driven-design-where-the-real-value-lies.html)

### Why over design?

I started exploring Domain Driven Design by deliberately over designing, This was necessary to really experiment with some of the patterns. If you are making a simple blogging platform then a lot of the things we will discuss are far from necessary. You could have a separate view object, interactor, repository, entity and data layer but it is not going to be worthwhile for anything other than a learning exercise. As with everything in software there is a trade-off, sophisticated architecture has a time cost associated with understanding the code. The benefits will not always outweigh these costs. If your problem is simple, the solution should also be simple.

### Resources
Domain Driven Design and tangential topics.

- [Domain Driven Design building blocks in Ruby](http://www.iain.nl/domain-driven-design-building-blocks-in-ruby)
  Quickly introduces entities, value objects, and services according to [Iain Hecker](https://twitter.com/iain_nl)
- [Hexagonal Architecture](http://alistair.cockburn.us/Hexagonal+architecture)
  (a.k.a. Ports and Adapters) by [Alistair Cockburn](https://twitter.com/totheralistair)
- [The Clean Architecture](https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  Layers in domain architecture and respecting boundaries described by [Robert Martin](https://twitter.com/unclebobmartin)
- [DDD building blocks](https://medium.com/@KamilLelonek/ddd-building-blocks-for-ruby-developers-cdc6c25a80d2)
  Clearly explaining the language used by people employing DDD principles by [Kamil Lelonek](https://twitter.com/KamilLelonek)
