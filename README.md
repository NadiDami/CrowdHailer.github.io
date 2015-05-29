# Insights at Workshop 14
**[insights.workshop14.io](http://insights.workshop14.io/)**

Work and insights from Crowdhailer at Workshop 14. This blog showcases projects, best practises and curated miscellany

## Install

Clone this repo to a file called blog.

```
git clone git@github.com:CrowdHailer/CrowdHailer.github.io.git blog
```

change into the blog directory

```
cd blog
```

*Jekyll is installed from recommendation for setting up with github pages http://jekyllrb.com/docs/github-pages/*
*Note Gemfile.lock not needed in version control*
load dependencies

```
bundle install
```

## Usage

To start the server execute:

```
bundle exec jekyll serve
```
## Development

#### syntax highlight
{% highlight python %}
{% endhighlight %}

#### Working with drafts
Posts saved in the `_drafts` directory will not be available when using the serve command or on the production site(github pages). To see the draft posts execute

```
bundle exec jekyll serve --drafts
```


## Development
[seo fundamentals](http://webdesign.tutsplus.com/series/seo-fundamentals-for-web-designers--webdesign-9715)

Opened issue on sitemaps, checkup
ahakins state of ruby community, jorja scala require, makis clojure require, james swift require.

simple link to tweat key text
http://blog.codeship.com/ruby-class-to-write-functional-code/?utm_source=rubyweekly&utm_medium=email

- sitemap
*Does site map use date for last updated*
*sitemap generating with drafts*
- Microdata
http://edusagar.com/articles/view/72/how-to-add-microdata-to-markup-structured-data-in-your-blog
- Blog word count for schema
- Twitter cards
- Create a portfolio page

- mark posts as posts and make comments and ratings available in Microdata
- loose is harmful title and put up a redirect page
