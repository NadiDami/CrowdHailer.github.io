source 'https://rubygems.org'

# Code to always use latest version of the github-pages gem
# Required to match with live github pages
# Explaination http://jekyllrb.com/docs/github-pages/
require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']
