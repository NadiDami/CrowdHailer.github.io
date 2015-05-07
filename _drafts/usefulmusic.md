---
layout: post
title: "Useful Music"
description: "Report on Collaborative working, technologies and practises"
tags: portfolio git
author: Peter Saxton
---

Useful Music
============

*Sheet music for developing musicians, pieces expertly written to suit the instrument and the player's level.*

![Clarinet image](http://usefulmusic.com/images/home-page-main.jpg)

## Professional & Collaborative working

A [fast](http://workshop14.io#fast-section) workflow in a [collaborative](http://workshop14.io#professional-section) enviroment is stressed at Workshop 14. These two desirable features are often found to compliment each other. A faster workflow involves automation where possible and this reduced friction to making changes allows participation from people not versed in the deployment process. A close collaboration means better comunication and getting to the right solution faster.

This project saw [Workshop 14](http://workshop14.io) and [richardesigns](https://slack-redir.net/link?url=http%3A%2F%2Fwww.richardesigns.co.uk%2F&v=3) come together to work with [Useful Music](http://usefulmusic.com/). We made a dispersed team across the UK from Devon to Yorkshire. To make sure we kept the communication channels working we had to use the best tools.

### Github

I have loved [git](http://git-scm.com/) and [github](https://github.com/) ever since we were introduced. It solves so many problems that you didn't even know you had and leads to a better place with seamless (and [asynchronous](http://www.mattaboutbusiness.com/why-you-should-run-your-business-online-5-asynchronous-collaboration/)) collaboration. This project was the first in which I encouraged contributions from everyone including those not versed in git. There where two things which convinced me this was a good thing to try. First the [integrations](https://github.com/integrations) that are available we had automatic testing and deployment to both a staging site and the production site working off commits to our git repository. Second Github has an excellent web interface, it is possible to create and edit files as well as open and review pull requests without ever installing git locally. In addition the search this repository feature will search the repository source files, so locating a typo is easy.

### Slack

We used our channel a centralised place for chatter on the project. As this was my first use of [slack](https://slack.com/) I integrated everything I could (a slightly over excited moment of automate all the things). After a few days of a very noisy channel we turned off a few integrations and kept just the ones we needed. A failing build definetly want to hear about it. A new commit on a feature branch probably not necessary. It is a great tool, I do not use it for all my communications leaving the most important things to be sent by email. This works well because it means I can close my slack channel and still be prompted on developments requiring my attention.

### Bugsnag
An effective bug tracking solution was essential as the pool of contributors increased. [Bugsnag](https://bugsnag.com) allows me to separate bug reports on staging from those on production, combined with user tracking any unexpected behaviour in development became a productive conversation. I was able to see who had experienced the bug ask questions of what they had been doing and what the expected behaviour was. The source details on the error allowed me to write tests enforcing the correct behaviour from then onwards.

### Results
- Truely simple changes, such as typos, can now be made by anyone. This is great as a typo often wants fixing imediatly. Regardless of how simple they can be quite a distraction when wqorking on new features
- Trickier changes can be started by anyone and left on an open pull request. A stakeholder can write some new content and leave comments in the pull request on the dynamic content that needs adding, such as always showing the current date.
- Allowing people who would only see the running website a view of the source helps everyone see what has been developed. It is often difficult as a develpoer to explain why some changes are much harder than others, this can help.
