---
layout: post
title: Minimum Viable Product vs Accessability
description: An overview on getting setup as self employed, it's not that bad
date: '2013-12-02T14:50:00+00:00'
tags:
- web
- MVP
- accessability
author: Peter Saxton
share-image: http://londonlayout.co.uk/img/sample.png
tumblr_url: http://crowdhailer.tumblr.com/post/68781121765/minimum-viable-product-vs-accessability
---
<p>This post covers my thoughts about including extra information in a website/app, such as wheelchair access. Such things should be included but with reduced resources how do you handle it. This post is written as much for my benefit as anyone else. I have been reading a great deal from Peter Gasston and his <a href="http://petergasston.co.uk/writing-about-thinking-about-thinking/" title="Writing about thinking about thinking">writing about thinking resonates here</a>.</p>
<p><img alt="image" src="http://media.tumblr.com/e56635775ddf96ed39433c7a3dacf9b4/tumblr_inline_mx6mtcJGZq1s4ay8u.png"/></p>
<p><!-- more --></p>
<p>The current situation is that I have a solid demonstration website with most of the features required in place but with a few omissions. Most notably there is no route finder. Included is a tube only and full suburban version of the map. These two version have a different set of interchange symbols to deal with the fact these two maps have different sized stations. These stations then require different positions for text labels. In both maps name labels could need to move again to show disabled information, creating four possible locations for the text. The zone boxes that surround some station text could also have to occupy one of four positions. </p>
<p>This bulking out of code and features is a resistance to further progress. It was also increasing the size of the page to the point where it would have reduced performance on mobile devices. These features had grown so rapidly in a large part due to feedback from various backers and I had tried to implement as many as possible as quickly as I could. </p>
<p>Now I am developing an app and it needs to be streamlined to work yet I want all the current interactivity and possibly more. Adding all of these features to begin with is completely opposite to the Minimum Viable Product (MVP) principle. The focus of MVP is to develop the minimum product which can then start generating feedback. This feedback dictates which features you prioritize adding. For a more in-depth discussion see this <a href="http://theleanstartup.com/principles" title="The Lean Startup">lean start up page</a>. </p>
<p>This method is particularly valuable to small teams and they don&rsquo;t get much smaller than my team of one. The issue is that the majority of users will not be requesting disabled access information. For this reason it would be easy to neglect the accessibility question. </p>
<p>However I have come to a few principles of development to balance these two.</p>
<ul><li>At all points in the development stage the product is by definition not finished. During this stage features can be added in any order.</li>
<li>My map may be available to the public in the development stage but this is a preview and each utility is a bonus.</li>
<li>Thinking about a few features at a time allows more focused development</li>
<li>The development stage can not be considered over until all the important accessibility utilities are included. </li>
</ul><p>The first three points are reasons to not get caught up in accessibility details and as long as the fourth is also observed then I see this as a reasonable logic to advance with.</p>
<p><strong>What does this mean for the development of my App?</strong></p>
<p>Following thinking I have loosely grouped all the features I would &lsquo;like&rsquo; to include into rough groups of importance. The app is being developed around these points in the order that makes most sense to code. It will be considered out of development when everything useful and above is included.</p>
<p><strong>Essential</strong></p>
<ul><li><span>Map image (lines, names, interchanges, grouped stations)</span></li>
<li><span>Pan + zoom (mouse)</span></li>
<li><span>Pan + zoom (touch)</span></li>
<li><span>Side panel for options</span></li>
<li><span></span>Live line status (extra details on page)</li>
</ul><p><strong>Necessary</strong></p>
<ul><li><span>Zones on map</span></li>
<li><span>Live departures</span></li>
<li><span>Route finder (list)</span></li>
<li><span></span>Auto-fill station name</li>
</ul><p><strong>Important</strong></p>
<ul><li><span>Disabled access information (list)</span></li>
<li><span>Cycle access (routes)</span></li>
<li><span>Social Network links</span></li>
<li><span>Walking times</span></li>
<li><span>Route finder on map</span></li>
<li><span>Time since live info updated</span></li>
<li><span>Live info refresh button</span></li>
<li><span>Clicking name loads station name</span></li>
<li><span>Help page</span></li>
</ul><p><strong>Useful</strong></p>
<ul><li><span>Spelling corrections on input box</span></li>
<li><span>Show zone numbers on map</span></li>
<li><span>Indicate where special fares apply</span></li>
<li>Find stations on map</li>
<li>About page</li>
<li>Local area maps</li>
<li>Clicking interchange symbol loads station name</li>
<li>Live station notifications</li>
<li>Weekend notifications</li>
<li>Disabled information on map</li>
<li><span>Extended station information</span></li>
<li><span></span>Clicking line shows name</li>
</ul><p><strong><span>Additional</span></strong></p>
<ul><li><span>Delay information on map</span></li>
<li><span>Find stations near me</span></li>
<li><span>Find stations near post code</span></li>
<li><span>Times for cycle access by company</span></li>
<li><span>Operator contact information</span></li>
<li><span>Link to oyster page</span></li>
<li><span>Route finder by postcode</span></li>
</ul><p><strong><span>Extentions</span></strong></p>
<ul><li>Mutual stations finder</li>
<li>optional walking speed for out of station interchange and in station interchange</li>
<li>Filter nearby stations by line</li>
<li>Filter nearby stations by access</li>
<li>Direction to nearby stations</li>
<li>Buy posters button</li>
<li>Direction widget</li>
</ul><p><strong>Distractions</strong></p>
<ul><li><span>moving lines for tube map</span></li>
<li><span>moving name labels for tube map</span></li>
<li><span>changing zone boxes for disabled information on map</span></li>
<li><span>detailed walking route between stations</span></li>
<li><span>local attractions show on area map</span></li>
<li><span>Augmented reality station find</span></li>
</ul>
