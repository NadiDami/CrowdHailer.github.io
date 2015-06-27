---
layout: post
title: Dicovering the joy of over design
description: Domain Driven Design practical advise with Ruby.
date: 2015-04-23 17:20:05
tags: ruby design
author: Peter Saxton
---

### Background

The Ruby language and me have been working together for over a year now. In that time I have learned the difference between hacking code and designing a program. Now I hope that I do more of the latter than the former. I wanted to write up some of the things I learned while making the change.

Software development suffers from a chronic abundance of nebulous terms, particularly when it comes to architecture. They all sound very helpful when summed up in a sentence or two, but there is always a lot more to them. The first lesson is to always read at least two opinions before you discount or adopt an idea. Here is my take on some of the most relevant terminology.

**Model View Controller (MVC)** is the architecture of Rails and as such something that most ruby developers are very familiar with. This architecture can lack the sophistication required to think about complex problems. It was while I struggled to workout what was a model that I started exploring other ways of thinking.

**Domain driven design (DDD)** is one of several frameworks to add structure to your thinking. The idea is to add abstraction to your program so that it can be discussed using the terms familiar to the problem it is for. It has more similarities than differences with hexagonal architecture and clean code. Reading about either of those will lead to many good ideas that will compliment what I talk about here.

### Contents

It is universally accepted in programming that modularity in code is beneficial in allowing developers to understand a program. MVC provides a way to break a program into 3 basic layers. If a program is very large when divided by three each piece is still large. The principles of DDD help add more divisions to a program and it is certainly useful as an extension to MVC. This series will mostly be about outlining common domain objects from DDD. Along with how and when to use them. The items I want to cover are:

1. Value objects - Object Oriented Programming all the way down
2. Form objects - Keeping the real world from making a mess
3. Entities & Records - Keeping track of the state of affairs
4. Repositories - Finding the answers using data
5. Interactors - Putting it all together

### Over Design


This is I think apt because alot of the things discussed here are far from necessary, if you have a separate view object(not template), interactor, repository entity and data layer for a simple list of all entries then that is plainly overkill. I started this process with a desire to deliberatly over design and see which ones made sense. I discovered that in alot of cases several of these patterns made sense. There may be issues with scaling when all of these are implemented but issues with scaling are exactly the issues I want to have. A question that is often asked is do I put these in from the beginning. The answer is yes. Everything the same is valuable




### Resources

- [Domain Driven Design building blocks in Ruby](http://www.iain.nl/domain-driven-design-building-blocks-in-ruby)
  Quickly introduces entities, value objects, and services
