---
layout: post
title: New Blog
author: Cruiser
---
Some things I figurd out setting up the blog to run on github pages with Jekyll

## Initial Setup
pretty easy
use the "project site" instructions at https://pages.github.com/
new repo, name after site
settings
  github pages
    source: master branch
    theme: pick one

## Theme


## Add Blogging
this was a little confusing
needed to add in the _layouts/post and the index.html with post listing.
needed to look in the source for the theme to see that it didn't have a post layout, initially added post and default layouts, but that totally overrode the theme. Once I figured out that I could leave out default and github pages would take care of it I was all set.

## Custom Domain

https://help.github.com/articles/quick-start-setting-up-a-custom-domain/
Add custom name to repo settings
add CNAME record that points to my site
wait for dns to propogate
remove and re-add custom name from repo settings
