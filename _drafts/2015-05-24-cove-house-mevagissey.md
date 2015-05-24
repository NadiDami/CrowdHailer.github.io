---
layout: post
title: Cove House Mevagissey
description: Static site with great performance from using the correct tools
date: 2015-05-24 10:36:05
tags: portfolio performance
author: Peter Saxton
share-image: http://insights.workshop14.io/assets/cove_house_mevagissey_home_page.png
---

#### *A luxury holiday cottage with stunning views over the harbour, village and along the beautiful Cornish coast.*

[![Homepage of the Cove House Mevagissey site](/assets/cove_house_mevagissey_home_page.png)](http://covehousemevagissey.co.uk/)

## Performance is a feature

my first project
recently I have been review performance of websites
in addition I have also discovered divshot
price rumptions

### Step 0,  Know thy enemy
The unbreakable rule of optimization is to measure measure measure. Us humans are extremely poor at understand how fast a computer will be. Optimization is a compromise with other features of a project, such as ease of understanding to new developers or speed of development. There are several tools online to measure a pages speed the two I use are [web page test](http://www.webpagetest.org/) and [page speed insights]() from google. It is possible to get lost in the details of these metrics. To keep things simple I will use the google tool. It has an overall score out of 100 and provides useful suggestions on what to improve.

So to establish a baseline put in the old site, and there was a bit of a surprise here.

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 0/100       |
| Mobile  | 0/100       |

So what is happening here. The number one suggestion is to optimize the images. On further investigation it turns out that I had been serving up full size images. 1000's of pixels wide so that leaves an obvious first step.

### Step 1. Resize images
To resize images I use a tool called [ImageMagick](), this is easy to install on Linux and provides a number of command line tools. The one we want is the convert tool. Another good rule when optimizating is to always try the simple solution first. Instead of creating multiple images at different resolutions what improvement can be made by in bulk just limiting all pictures to a width of 500px. This is about the largest image on the page and resizing them all to the same size means I can move them around in the design without revisiting the optimization steps.
To resize every image in my `images/` directory and save a copy in the `www/` directory execute

```
for file in images/*; do convert $file -resize 500 ./www/$file; done
```

**So what is the benifit?** Checking with page speed insights our new score is now substantially improved. Our simple resize has worked and google now has a new suggestion of what to tackle next

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 55/100      |
| Mobile  | 51/100      |

### Step 2. Optimize images
Pixel image formats save images as an array of pixels. The best way to save this data is dependant on the image and for that reason it is possible to reduce the size of an image file without in any way changing the image. This really is as close to a no compromise win you can get.

To do this compression I use a node tool [imagemin](). To use this requires you to have node and npm installed[^1].

As I am only interested in the original copies of the images and the fully optimized images I overwrite the resized images with the resized and optimized images.

```
./node_modules/.bin/imagemin www/images/* www/images/
```

**So what is the benifit?** Checking with page speed insights our score has again seen substantial improvment. Our opmtimization was worth it and google now has a new suggestion of what to tackle next

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 86/100      |
| Mobile  | 75/100      |

### Step 3. Compress CSS
CSS is an external resource to the page an each request has an overhead associated with setting it up. To speed loading the CSS it is important to reduce the total size but to also minimize the number of requests.

CleanCSS is another node tool[^1]. It's function is to remove cruft from the code and minify what remains. It also has a nice method for concatinating multiple files by simply piping in multiple files. To concatinate and minify all my source css I execute the following

```
cat css/*.css | cleancss > www/css/main.min.css
```

**So what is the benifit?** A much smaller improvement now, So already we can see that for this site the images are the major factor

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 87/100      |
| Mobile  | 77/100      |

### Step 4. Inline CSS and minify html
As there is no JavaScript on this page the only resource loaded in the head is the stylesheet. Googles next suggestion was to eliminate this render blocking fetch of the CSS. To do this I copied the entire minified css into the document. As this site has only one page and changes infrequently the simplest way to do this was to simply copy and paste it in. At the same time I also ran the HTML through html-minifier (another node tool[^1]).

**And the final Score?**

| Platfom | Speed Score |
| ------- | ----------: |
| Desktop | 92/100      |
| Mobile  | 88/100      |

### Reflect on progress
At this point we have a very respectable score, and the time spent has been minimal. We could go further but the question is now do we need to. This page is image rich and that will not change, hunting for higher scores can be a [drain on time](premium.wpmudev.org/blog/why-trying-to-get-95-on-google-pagespeed-insights-will-drive-you-mad/).  

Simply put performance is a feature but it is not the only feature. This site is no longer held back by its performance and the success will now depend on other factors such as its optimization for search engines(SEO) and social media(SMO).


[^1]: *Getting these is explained [here]().* There are tools which do not require node. However the node community is most active in pushing front end development and if moving on to more advanced builds a node environment will be invaluable.
