---
layout: post
title: Dicovering the joy of over design
description: Domain Driven Design a practical introduction with Ruby.
date: 2015-07-14 17:20:05
tags: ruby design
author: Peter Saxton
---

### Introduction

The Ruby language and I have been working together for over a year now. In that time I have learned the difference between hacking code and designing a program. Now I hope that I do more of the latter than the former. I wanted to write up some of the things I learned while making the change.

Software development suffers from a chronic abundance of nebulous terms, particularly when it comes to architecture. They all sound very helpful when summed up in a sentence or two, but the process of summing up these concepts usually removes what is interesting about them. The first piece of advice for this series is to always read at least two opinions, preferably five, before you discount or adopt an idea. Your problem is never completely new and some of the people who have thought about it before are extremely smart.

### Some Terms

Let's get started with some overly brief summaries of some of the concepts that will be brought up.

**Model View Controller (MVC)** is the architecture of Rails and as such something that most Ruby developers are very familiar with. This architecture simply breaks the code into layers. The part describing the system data and state belongs in the Model, all presentation belongs in the View and the Controller connects the other two. This is a simple architecture that is very accessible. It can lack the sophistication required to help organise complex problems. It was while I struggled with what constituted a model in MVC that I started looking for other ways of thinking.

**Domain driven design (DDD)** is one of several 'frameworks' to add structure to your thinking. The idea is to add abstractions to your program that mean it can be discussed using the terms familiar to the non technical stakeholders, people who care about what your software is doing usually a user or client often both. It has more similarities than differences with 'hexagonal architecture' and 'clean code'. Reading about any of these will expose you to good ideas that will compliment what I want talk about here.

**Test driven development** is the discipline of writing tests before the code that will make those tests pass. The argument between test first and test after is not important. All that is required of tests, in my opinion, is that as a developer you are confident your code works.

### What's coming

It is widely accepted that modularity in code is beneficial. It allows developers to understand a large complex program one manageable piece at a time. MVC helps with modularity by providing a way to break a program into 3 basic layers. However, if a program is very large when separated into three, each piece is still large.

The principles of DDD help add more lines of division to a program. This more sophisticated way to add separation can be used to create an arbitrary number of components until they are again understandable.

This blog series will introduce the following domain objects from DDD.

1. [Value objects](/2015/07/15/value-objects-in-ruby.html) - Object Oriented Programming all the way down
2. [Form objects](/2015/07/23/application-border-control-with-ruby-form-objects.html) - Keeping the real world from making a mess
3. [Entities & Records](http://insights.workshop14.io/2015/08/02/tackling-god-objects-in-ruby.html) - Keeping track of the state of affairs
4. Repositories - Finding the answers using data
5. Interactors - Putting it all together

There are many more but these are the ones I have found most useful so far. I will also discuss how and when to use them, as well as how I test them.

### Why over design?

I started this process with a desire to deliberately over design so I could understand the patterns I was encountering. If making a simple blogging platform then a lot of the things we will discuss are far from necessary. You could have a separate view object, interactor, repository, entity and data layer but it is not going to be worthwhile for anything other than a learning exercise. As with everything in software there is a trade-off, sophisticated architecture has a time cost to understand it and the benefits do not always outweigh these costs. If your problem is simple, the solution should also be simple.

### Resources
Domain Driven Design and tangential topics.

- [Domain Driven Design building blocks in Ruby](http://www.iain.nl/domain-driven-design-building-blocks-in-ruby)
  Quickly introduces entities, value objects, and services according to [Iain Hecker](https://twitter.com/iain_nl)
- [Hexagonal Architecture](http://alistair.cockburn.us/Hexagonal+architecture)
  (a.k.a. Ports and Adapters) by [Alistair Cockburn](https://twitter.com/totheralistair)
- [The Clean Architecture](https://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
  Layers in domain architecture and respecting boundaries described by [Robert Martin](https://twitter.com/unclebobmartin)
