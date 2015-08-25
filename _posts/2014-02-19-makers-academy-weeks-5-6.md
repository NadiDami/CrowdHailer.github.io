---
layout: post
title: 'Makers Academy: Weeks 5 & 6'
description: Introducing the web with Sinatra and data storage with postgres
date: '2014-02-19T09:17:00+00:00'
tags:
- Makers Academy
- Sinatra
- Ruby
author: Peter Saxton
tumblr_url: http://crowdhailer.tumblr.com/post/77160811925/makers-academy-weeks-5-6
---
<p><strong>Week five was our first introduction to programming for the web and therefore a week I had been looking forward to from the start.</strong> We began with <a href="http://www.sinatrarb.com/" title="Sinatra" target="_blank">Sinatra</a>, a fantastic lightweight framework or DSL (Domain Specific Language). With Sinatra just 4 lines of code is all it takes to make the most rudimentary server </p>
<pre><code>    require 'sinatra'

    get '/hi' do
      "Hello World!"
    end</code></pre>
<p>This was all very promising unfortunately the rest of the week I spent in a bit of a daze. The fatigue had caught up with me in a big way. At university this was called 5th week blues. I was surprised to run into them again but this is probably the hardest I&rsquo;ve had to think since university.</p>
<p><!-- more --></p>
<p>The rest of the week was focused on HTML and CSS which fortunately I had covered a fair bit before. It was also the first time I used Linux. That can be read about in another <a href="http://crowdhailer.tumblr.com/2014/02/11/converting-to-linux.html" title="Converting to Linux" target="_blank">blog post</a> but I will share the best here. That is the cowthink command.</p>
<p><strong>Week six was much more productive. We added databases to our repertoire.</strong> This was something I had not done before and with the ever increasing speed we attack topics the intensity just keeps going up. We used them to create a fairly large project, a bookmark manager. In some ways not the most enthralling project but with the ability to sign up and log in it is worlds away from what I have created before. Even if most of the heavy lifting is done by another magical gem, <a href="http://datamapper.org/" title="Datamapper" target="_blank">Data Mapper</a>, creating this has filled me with a lot of confidence that many of the projects I want to build are now possible. </p>
<p>With databases and the ability to record users data some time needed to be spent discussing security. Obviously this is a huge topic and one that it is best not to try and solve as a beginner with so many issues that need to be got <a href="https://crackstation.net/hashing-security.htm" title="Secure Salted Passwords" target="_blank">right</a>. There are a lot of <a href="http://code.tutsplus.com/tutorials/ruby-for-newbies-working-with-datamapper--net-19622" title="datamapper" target="_blank">&lsquo;plug and play</a>&rsquo; solutions to securing user data such as <a href="http://en.wikipedia.org/wiki/MD5" title="Wikipedia MD5" target="_blank">md5</a>. We used <a href="http://bcrypt.sourceforge.net/" title="BCrypt" target="_blank">BCrypt</a> a more secure system that plays well with Data Mapper.</p>
<p><strong>As always the internet came to the rescue with many helpful resources.</strong> Unusually this time I&rsquo;m going to start by recommending a book. The Code Book by Simon Singh, I read it a few years ago and mostly does not talk about computer security but general coding and code breaking. It starts from Roman era cyphers but leads up very well to the issues in all so called secure communications. Also was this site, <a href="http://www.devdocs.io" title="DevDocs" target="_blank">devdocs</a>. A massive library of reference for developers.</p>
