## Speed

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
