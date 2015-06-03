### Escaping the strings
Lets dive in with some code.

```rb
'January'.winter?
'password'.secure?
'B-'.pass?
```

Which of these should return true and which should return false? Perhaps it will be easier if I rephrase the question.

```rb
'January'.secure?
'password'.pass?
'B-'.winter?
```
They all will, as the should, raise NoMethodError's. In the first example we see 'January' as a month and so make sense of the question is it winter. However our system needs a concept of a month before we can ask it about winter.

Now maybe your system doesn't need to know about seasons but I bet you have come across validations

```rb
'peter@workshop14.io'.valid?
Month.new('peter@workshop14.io').valid?
```

Obviously the second example should return false.
*I would go even further and say that we shouldn't get to the point of asking a month if it is valid. Calling `Month.new` with anything that cannot be reconised as a month should fail with and exception*



### Welcome to the Value objects
tl:dr

This post introduces value objects. I think they are a good idea. If you know what they are and agree then good. wait for the next article

There are some great tutorials out there are on value objects in ruby, as well as when to use them. So check out these articles for another point of view on them. I want to build on top of these value objects so lets quickly introdue them.

### What are value objects?
A value object is any object who's identity is characterized it's attributes. Ruby primitives such as `String`, `Integer`, `DateTime` are all value objects. The antithesis of value objects are entities, they have an identity beyond there attributes. Lets clear this up with an example.

value objects
```rb
string1 = 'hello'
string2 = 'hello'

string1 == string2
# => true
```

entity
```rb
user1 = User.new :name => 'Peter'
user2 = User.new :name => 'Peter'
user1 == user2
# => false
```
and most importantly user2 can update there name and still be user2.

### Building a value object
So I have announced there should be a month object but what should it look like.
One option is to subclass String when creating these new methods. I normally prefer being explicing and starting with a new class. We can show possible odd behaviour with out month implementation

```rb
# subclassed from String
january = Month.new 'January'
january.length
# => 7
```

Almost certainly if you tell you buisness partner that january has a length of 7 they will not agree.

### Speed

One disadvantage is the speed of progress that is made when needing to specify all the details of each type that might be needed in a new application.
My leading principle is to work towards evergreen. i.e. don't rush at the beginning.
It is important to settle down for the long run, that said that people want to see progress quickly. Including ourselves as developers.

To combat this I have set up a library of types that I come across. It has or will have soon types for. emails, titles, names.

But wait surely the conditions on a title are different from project to project.
Yes but they don't all need to be in place from the off.
The typetanic title is full of some handy defaults. and if you want a new thing just write your own.

Good code means changing one thing.

So when developing values/title.rb looks like the following

```rb
class Title < Typetanic::Title

end
```

when our title has been specified
```rb
class Title
  # My code
  # It is a simple object. Just write one
end
```

My library is typetanic. It is open source and contributions are of course welcome.
but its real value is too me. Im not sure I want to create

```rb
class Title < Typetanic::String
  min_length 5
  max_length 10
  value_format /^[A-Z][a-zA-Z0-9\s]+$/
  capitalise true
end
```

it looks nice but i think is over kill
