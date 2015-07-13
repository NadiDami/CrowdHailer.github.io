---
layout: post
title: Cove House Mevagissey
description: Improving performance for a static webpage using page speed insights
date: 2015-07-13 21:05:00
tags: portfolio performance web
author: Peter Saxton
share-image: http://insights.workshop14.io/assets/cove_house_mevagissey_home_page.png
---

#### *A luxury holiday cottage with stunning views over the harbour, village and along the beautiful Cornish coast.*

[![Homepage of the Cove House Mevagissey site](/assets/cove_house_mevagissey_home_page.png)](http://covehousemevagissey.co.uk/)

## Performance is a feature
Recently I was revisiting the first project I ever did for a client. I had decided to move it from heroku to divshot. A far more appropriate choice as it was a single static page. While moving it across I decided to review the site's performance as I have learnt much since then. The original project was only a 2hr job to rescue the site out of a drag and drop builder so I had very little idea as to what to expect. Turns out I was in for a bit of a surprise. There were some small changes for a decent performance improvement and these are a few of them.

### Step 0,  Know thy enemy
The unbreakable rule of optimization is to measure measure measure. Us humans are extremely poor at understanding how fast a computer will be. Optimization is a compromise with other features of a project, such as having comprehensible code or a speedy development. Therefore it is worthwhile measuring the gains so you can weigh them against the costs.

There are many tools online to measure a sites speed. The two I use most are [page speed insights](https://developers.google.com/speed/pagespeed/insights/) from google and [web page test](http://www.webpagetest.org/). It is possible to get lost in the details of these metrics. To keep things simple I will use just page speed insights. It provides an overall score out of 100 as well as useful suggestions on what to improve.

So to establish a baseline I put in the old site, oh dear.

![Initial page speed insights for Cove House Mevagissey site](/assets/initial_page_speed_insights.png)

Here are the telling numbers again.

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 0/100       |
| Mobile  | 0/100       |

So what is happening here? Well, turns out it's a few gigantic images direct from a camera and optimizing images is the top suggestion. So on to the optimization.

### Step 1. Resize images
To resize images I use a tool called [ImageMagick](http://www.imagemagick.org). This is easy to install on Linux and provides a number of command line tools including convert for resizing images.

Another good rule when optimizing is to always try the simple solution first. Instead of creating multiple images at different resolutions I want to see  what improvement can be made by just limiting all pictures to a width of 500px. This is about the size of the largest image on the page and resizing them all to the same size means I can move them around in the design without having to resize again.
To resize every image in my `images/` directory and save a copy in the `www/` directory execute

```
$ for file in images/*; do convert $file -resize 500 ./www/$file; done
```

**So what's the improvement?** Checking with page speed insights our new score is now substantially improved. Our simple resize has worked and we have a new suggestion on what to tackle next

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 55/100      |
| Mobile  | 51/100      |

### Step 2. Optimize images
Pixel image formats save images as an array of pixels. The best way to save this data is dependent on the image and for that reason it is possible to reduce the size of an image file without in any way changing the image. This really is as close to a no compromise win as you can get.

To do this compression I use a node tool [imagemin](https://github.com/imagemin/imagemin). To use this requires you to have node and npm installed[^1].

As I am only interested in the original images and the fully optimized versions I overwrite the resized images with the resized and optimized images.

```
$ ./node_modules/.bin/imagemin www/images/* www/images/
```

**So what's the improvement?** Checking with page speed insights our score has again seen substantial improvement. Our optimization was worth it and yet again a new suggestion of what to tackle next.

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 86/100      |
| Mobile  | 75/100      |

### Step 3. Compress CSS
CSS is an external resource to the page an each request has an overhead associated with setting it up. To speed loading the CSS it is important to reduce the total size but to also minimize the number of requests. To reduce requests CSS should be concatinated into one file and to reduce the size the contents should be minimized.

CleanCSS is another node tool[^1]. It's function is to remove cruft from the CSS and minify what remains. It also has a nice method for concatinating multiple files by simply piping in multiple files. To concatinate and minify all my source CSS I execute the following.

```
$ cat css/*.css | cleancss > www/css/main.min.css
```

**So what's the improvement?** A smaller improvement this time. Already we can see that for this site the images are the major factor for performance.

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 87/100      |
| Mobile  | 77/100      |

### Step 4. Inline CSS and minify html
As there is no JavaScript on this page the only resource loaded in the head is the stylesheet. Google's next suggestion was to eliminate this render blocking fetch of the CSS. To do this I copied the entire minified CSS into the document. As this site has simple styling and changes infrequently this was the easiest solution. At the same time I also ran the HTML through html-minifier(another node tool[^1]). Giving us our final page.

**And the final Score?**

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 92/100      |
| Mobile  | 88/100      |

### Reflect on progress
At this point we have a very respectable score, and the time spent has been minimal. We could go further but the question is now do we need to. I would guess not, this page is image rich so we will always have those requests. Hunting for higher scores can be a [drain on time](premium.wpmudev.org/blog/why-trying-to-get-95-on-google-pagespeed-insights-will-drive-you-mad/).  

Simply put performance is a feature and one of many. This site is no longer held back by its performance and the success will now depend on other factors such as its optimization for search engines(SEO) and social media(SMO). Which is definitely a topic for another post.


[^1]: *Getting node and npm is explained [here](https://docs.npmjs.com/getting-started/installing-node).* There are tools which do not require node. However the node community is most active in pushing front end development and if moving on to more advanced builds, a node environment will be invaluable.
