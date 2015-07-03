---
layout: post
title: Dicovering the joy of over design
description: Domain Driven Design practical advice with Ruby.
date: 2015-04-23 17:20:05
tags: ruby design
author: Peter Saxton
---

### Introduction

The Ruby language and me have been working together for over a year now. In that time I have learned the difference between hacking code and designing a program. Now I hope that I do more of the latter than the former. I wanted to write up some of the things I learned while making the change.

Software development suffers from a chronic abundance of nebulous terms, particularly when it comes to architecture. They all sound very helpful when summed up in a sentence or two, but the process of summing up usually removes what is interesting about them. The first piece of advice for this series is to always read at least two opinions before you discount or adopt an idea.

### Some Tems

Lets get started with some overly brief summaries of some of the concepts that will be brought up.

**Model View Controller (MVC)** is the architecture of Rails and as such something that most ruby developers are very familiar with. Code describing the system data and state belongs in the Model, all presentation belongs in the View and the Controller connects the other two. This is a simple architecture that can lack the sophistication required to think about complex problems. It was while struggled what constituted a model that I started exploring other ways of thinking.

**Domain driven design (DDD)** is one of several frameworks to add structure to your thinking. The idea is to add abstraction to your program so that it can be discussed using the terms familiar to the user or client. It has more similarities than differences with 'hexagonal architecture' and 'clean code'. Reading about any of these will expose you to good ideas that will compliment what I want talk about here.

**Test driven development** is the discipline of writing tests before the code that will make those tests pass. The argument between test first and test after is not important. All that is required of tests is that as a developer you are confident your code works.

### Whats coming

It is accepted that modularity in code is beneficial. It allows developers to understand a large complex program one managable piece at a time. MVC helps with modularity by providing a way to break a program into 3 basic layers. If a program is very large when separated into three each piece is still large.

The principles of DDD help add more lines of division to a program, this more sophisticated way to add separation can be used to create an arbitrary number of components until they are again manageble.

This blog series will introduce the following domain objects from DDD.

1. Value objects - Object Oriented Programming all the way down
2. Form objects - Keeping the real world from making a mess
3. Entities & Records - Keeping track of the state of affairs
4. Repositories - Finding the answers using data
5. Interactors - Putting it all together

There are many more but these are the ones I have found most useful so far. I will also discuss how and when to use them, as well as how I test them.

### Why over design?

If making a simple blogging platform then a lot of the things discussed here are far from necessary. You could have a separate view object, interactor, repository, entity and data layer but it is not going to be worthwhile for anything other than a learning exercise.

I started this process with a desire to deliberately over design so I could learn about the patterns discussed. As with everything in software there is a tradeoff and most likely some of these patterns can be used in your next project.


### Resources

- [Domain Driven Design building blocks in Ruby](http://www.iain.nl/domain-driven-design-building-blocks-in-ruby)
  Quickly introduces entities, value objects, and services
