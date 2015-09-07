---
layout: post
title: The trouble with Postgres and Rails
description: Setting up PostgreSQL on Linux Mint
date: '2014-03-10T08:30:00+00:00'
tags:
- postgresql
- linux
- database
author: Peter Saxton
share_url: https://31.media.tumblr.com/9f511acf35003536871163f0f11da829/tumblr_inline_n25uz2uZV31s4ay8u.jpg
tumblr_url: http://crowdhailer.tumblr.com/post/79150713896/the-trouble-with-postgres-and-rails
---
<p><strong>PostgreSQL is an object-relational database management system , snappy abbreviation <em>&lsquo;ORDBMS&rsquo;</em>.</strong> It is often known as postgres and is<span> available on many platforms. It has also been an unequaled pain for the last few weeks and I don&rsquo;t think it need be. Here are my notes on what to do next time. <em>NOTE all of this is related to installing onto Linux Mint.</em></span><span><br/></span></p>
<p><span><em><img alt="image" src="https://31.media.tumblr.com/9f511acf35003536871163f0f11da829/tumblr_inline_n25uz2uZV31s4ay8u.jpg"/></em></span></p>
<p><!-- more --></p>
<p><span><strong>Firstly to get postgresql is very simple as it is availabe in the main Personal Package Archive (PPA).</strong> This means it can be installed using </span></p>
<p><span>    ~ $ <em><span><a href="http://explainshell.com/explain?cmd=+sudo+apt-get+install+postgresql" title="Explain shell" target="_blank">sudo apt-get install postgresql</a></span></em></span></p>
<p><span><em><span>    ~ $ sudo apt-get install libpq-dev (Needed for pg Gem)</span></em></span></p>
<p><span>Installing it this way adds several commands from the terminal such as psql for accessing the database. For security reasons (I believe*) this install will create a new user on your machine called postgres and only that user will have a role when starting psql. Without making changes it is not possible to access the database as the current user and the following is shown.</span></p>
<p><span>    ~ $ psql</span><br/>    psql: FATAL: role &ldquo;peter&rdquo; does not exist</p>
<p>To correctly access psql you must act as the postgres user by executing the following command.</p>
<p>    ~ $ <a href="http://explainshell.com/explain?cmd=sudo+-u+postgres+psql" title="Explain shell" target="_blank">sudo -u postgres psql</a></p>
<p><strong>This option is however not available to applications such as rake that want to access and create/modify the database</strong>. I cam across two problems repeatedly and both could be solved by modifying the test and development environment in database.yml in the Rails project</p>
<ul><li><span><strong>Error</strong> - &quot;Could not connect to server: No such file or directory&quot;</span></li>
<li><span><strong>Solution</strong> - Add line &ldquo;host: localhost&rdquo;</span></li>
</ul><ul><li><span><strong>Error</strong> - &ldquo;fe_send auth: no password supplies&rdquo;</span></li>
<li><span><strong>Solution</strong> - Add lines &ldquo;user: postgres&rdquo; &amp; &ldquo;password: &lt;PASSWORD&gt;&rdquo;</span></li>
</ul><p><span>NB. It is possible to change the postgres password as a super user. Open psql and use the command</span></p>
<p><span>    ~# ALTER USER postgres with password '123&rsquo;;</span></p>
<p><span>Issues with the above system come when you push your project. Your postgres user password is saved in the git repository and won&rsquo;t be the same for all members working on the project. You will also need to add the to each project individually. A far more robust solution is to move the information to environmental variables. Setting the following environmental variables allows the all of the extra lines added to be removed.</span></p>
<ul><li>PGHOST=localhost</li>
<li>PGUSER=postgres</li>
<li>PGPASSWORD=123</li>
</ul><p>NB. set enviromental variables using the command<br/>    <a href="http://explainshell.com/explain?cmd=echo+%22export+PGHOST%3Dlocalhost%22+%3E%3E+~%2F.bash_profile" title="Explain shell" target="_blank">echo &ldquo;export PGHOST=localhost&rdquo; &gt;&gt; ~/.bash_profile</a></p>
<p>To finish is this brief but useful cheatsheet.</p>
<p>* If you know better let me know and I will make an update</p>
