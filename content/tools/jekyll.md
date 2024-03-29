---
title: Jekyll
tags:
 - jekyll
 - ruby
 - github_pages
---

[Jekyll](https://jekyllrb.com/) is a Ruby tool which can generate static websites and blogs using plain text files.
<!--more-->

## Handy Commands

| Command                              | Description                            | 
|--------------------------------------|----------------------------------------|
| `bundle exec jekyll build`           | Build the Jekyll site                  |
| `bundle exec jekyll build --profile` | Build the site and profile the build   |
| `bundle exec jekyll serve`           | Run the Jekyll site                    |
| `bundle update github-pages`         | Update a plugin (example github-pages) |


## Configuration ( `_config.yml`)

### baseurl

Use `baseurl` when you are building a site that doesn’t sit at the root of the domain. For example:

'http://jekyll.github.io/example'

In this case you would need to set `baseurl: /example` in the `_config.yml` file otherwise the automatically generated links will not include the `/example`  in the url path. 

For more info see [Clearing Up Confusion Around baseurl -- Again](https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/)

## Links (to other site pages)

Links to other pages in Jekyll follow the basic [markdown]({{<ref "/languages/markdown">}}) format, but with the use of a little liquid to insert the url details.

If you do not use the `baseurl` configuration mentioned above, then use:
``` markdown
   [Link to a document]({% link _tools/jekyll.md %})
```

If your site makes use of the `baseurl` configuration you need to prepend this to your link like this:
``` md
   [JavaScript]({{site.baseurl}}{% link _languages/javascript.md %})
```
