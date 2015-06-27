found the rails way
A model is not made of models
A view is not made of views
and controllers are not inherited from controllers
Pushing logic below the model layer

The problem with misunderstood MVC. Breaking things into small pieces is pretty universally accepted as a good idea. MVC offers a nice way to break up an application into logically distinct layers. A model, a view and a controller. Three nice pieces, However as an application grows each piece grows and you end up with 3 big pieces. Obviously we want to break into more pieces and the question becomes where. This is not a problem with MVC there is noting in MVC that prevents you have an M made from 10 nice pieces but it doesn't care what these pieces are. It also means it is no help in deciding what these pieces should be.

The problem with rails. Rails is an excellent too which has a problem that because it does everything you need for so long you don't know when to leave.


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
best talks 

east oriented

stringly

confident ruby
