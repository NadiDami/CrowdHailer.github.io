---
layout: post
title:  "Introducing the new blog"
description: "Building on a blank canvas, my new blog without bootstrap."
date:   2015-04-23 17:20:05
tags: css
author: Peter Saxton
---

Introducing the new blog
========================

A first blog post on a new blog. It is tricky to know what to write about. My first thought was to write about why I have a new one and making the change. It certainly is a [traditional](http://blog.55minutes.com/2012/02/migrating-from-tumblr-to-jekyll/) starting point. I have chosen to build my blog with jekyll and the choice was easy - I wanted the support with github pages. That was it. I trust github, so that was my decision made. Is it really better than Tumblr where I was [before](http://crowdhailer.tumblr.com/)? I don't know and I won't know for a while.

What else have I learned from making this blog and first page? Well most surprisingly to me writing naked CSS, without a framework, is not as painful as I had worried it might be. Over the course of the last year I have moved from [foundation](http://foundation.zurb.com/) to [purecss](http://purecss.io/) and finally on to this blog with no framework at all. Working with no framework does require a few adjustments- these are my recommendations.

### 1. You are not alone
Abandoning a framework does not have to mean starting from a blank canvas. On this page I have used the syntax highlighting from the default jekyll template. I have whole heartedly taken the [github-markdown-css](https://github.com/sindresorhus/github-markdown-css) made available by sindresorhus. Thank you very much. The latter handles all the styling of text content in the center of my page. This means I can quickly have a page that looks good but on my terms. If I remove this css I know it only affects content within `.markdown-content` and not every link. Finally a notable mention to [scut](https://davidtheclark.github.io/scut/list-inline.html) a great resource even if just used as inspiration for solving common but tricky problems.

### 2. Have some content
Create a full page of content to work with, set up as much of markup as possible. Too many times I have made it look great for paragraphs with one line of text only to find out in the real system that the content often goes on for 20 lines. Also if you setup, you markup before the styling and it will be obvious when you are adding an element for the purposes of styling only. The greater part of this page is generated from a [markdown example](http://www.unexpected-vortices.com/sw/rippledoc/quick-markdown-example.html). I just copied this in and will leave it at the end of this article for reference whenever I change the styles here.

### 3. Know what you like
Perhaps best shown with an example.

{% highlight css %}
* {
    box-sizing: border-box;
}
{% endhighlight %}

You might or might not prefer working like this, however I know I do and so I set it up straight away. What would be impossible is sometimes working with border boxes and sometimes with content boxes.


### 4. avoid the &lt;div&gt;
This is not a rant against [divitis](https://csscreator.com/divitis) or a puritanical approach to the [semantic web](http://en.wikipedia.org/wiki/Semantic_Web). Not that I don't think they are both good points. Having total freedom with your CSS you will need to bring your own structure. Less elements of any type will help with this. When you start you won't know if things will be vertically spaced by padding or margins. Having classes called 'large-padding' or 'medium-margin' sprinkled on divs won't force you to think about your structure. Having a single class 'post' on your article elements will force you to think earlier about what is needed. Doing the difficult bits early is always a good idea. A hard problem is less intimidating when not accompanied by 5 other hard problems.

### 5. Don't rush
I have found that if I have the luxury to keep revisiting the styles I have written then a structure arises. Features that started buried in media queries can turn out to be generic. As this happens progress can accelerate, occasionally dramatically. In the same way a new bootstrap page looks like generic bootstrap, new pages on my site end up looking like generic pages on my site, which is great. It provides a site consistency and focus can be turned to the really interesting or new bits. So if a quick prototype, bootstrap everytime. My own blog in my own time - the perfect time to go for naked styling.


### *finally*
Two excellent resources that made me take the final leap to no framework.

- [Front End Design Patterns](https://vimeo.com/97315940)
- [Build a Better Bootstrap](https://vimeo.com/97318798)


An h1 header
============

Paragraphs are separated by a blank line.

2nd paragraph. *Italic*, **bold**, and `monospace`. Itemized lists
look like:

  * this one
  * that one
  * the other one

Note that --- not considering the asterisk --- the actual text
content starts at 4-columns in.

> Block quotes are
> written like so.
>
> They can span multiple paragraphs,
> if you like.

Use 3 dashes for an em-dash. Use 2 dashes for ranges (ex., "it's all
in chapters 12--14"). Three dots ... will be converted to an ellipsis.
Unicode is supported. ☺



An h2 header
------------

Here's a numbered list:

 1. first item
 2. second item
 3. third item

Note again how the actual text starts at 4 columns in (4 characters
from the left side). Here's a code sample:

    # Let me re-iterate ...
    for i in 1 .. 10 { do-something(i) }

As you probably guessed, indented 4 spaces. By the way, instead of
indenting the block, you can use delimited blocks, if you like:

~~~
define foobar() {
    print "Welcome to flavor country!";
}
~~~

(which makes copying & pasting easier). You can optionally mark the
delimited block for Pandoc to syntax highlight it:

{% highlight python %}
import time
# Quick, count to ten!
for i in range(10):
    # (but not *too* quick)
    time.sleep(0.5)
    print i
{% endhighlight %}



### An h3 header ###

Now a nested list:

 1. First, get these ingredients:

      * carrots
      * celery
      * lentils

 2. Boil some water.

 3. Dump everything in the pot and follow
    this algorithm:

        find wooden spoon
        uncover pot
        stir
        cover pot
        balance wooden spoon precariously on pot handle
        wait 10 minutes
        goto first step (or shut off burner when done)

    Do not bump wooden spoon or it will fall.

Notice again how text always lines up on 4-space indents (including
that last line which continues item 3 above).

Here's a link to [a website](http://foo.bar), to a [local
doc](local-doc.html), and to a [section heading in the current
doc](#an-h2-header). Here's a footnote [^1].

[^1]: Footnote text goes here.

Tables can look like this:

size  material      color
----  ------------  ------------
9     leather       brown
10    hemp canvas   natural
11    glass         transparent

Table: Shoes, their sizes, and what they're made of

(The above is the caption for the table.) Pandoc also supports
multi-line tables:

--------  -----------------------
keyword   text
--------  -----------------------
red       Sunsets, apples, and
          other red or reddish
          things.

green     Leaves, grass, frogs
          and other things it's
          not easy being.
--------  -----------------------

A horizontal rule follows.

***

Here's a definition list:

apples
  : Good for making applesauce.
oranges
  : Citrus!
tomatoes
  : There's no "e" in tomatoe.

Again, text is indented 4 spaces. (Put a blank line between each
term/definition pair to spread things out more.)

Here's a "line block":

| Line one
|   Line too
| Line tree

and images can be specified like so:

![example image](http://londonlayout.co.uk/img/tube.png "An exemplary image")

Inline math equations go in like so: $\omega = d\phi / dt$. Display
math should get its own line and be put in in double-dollarsigns:

$$I = \int \rho R^{2} dV$$

And note that you can backslash-escape any punctuation characters
which you wish to be displayed literally, ex.: \`foo\`, \*bar\*, etc.
