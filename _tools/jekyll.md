---
tool: jekyll
name: Jekyll
tags:
 - jekyll
--- 

[Jekyll](https://jekyllrb.com/) is a Ruby tool which can generate static websites and blogs using plain text files.
<!--more-->
## Running (Locally)

You can run Jekyll locally by using the following command `bundle exec jekyll serve`

## Configuration ( `_config.yml`)

### baseurl

Use `baseurl` when you are building a site that doesn’t sit at the root of the domain. For example:

'http://jekyll.github.io/example'

In this case you would need to set `baseurl: /example` in the `_config.yml` file otherwise the automatically generated links will not include the `/example`  in the url path. 

For more info see [Clearing Up Confusion Around baseurl -- Again](https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/)

## Links (to other site pages)

Links to other pages in Jekyll follow the basic [markdown]({{site.baseurl}}{% link _languages/markdown.md %}) format, but with the use of a little liquid to insert the url details.

If you do not use the `baseurl` configuration mentioned above, then use:
``` markdown
   [Link to a document]({% link _tools/jekyll.md %})
```

If your site makes use of the `baseurl` configuration you need to prepend this to your link like this:
``` md
   [JavaScript]({{site.baseurl}}{% link _languages/javascript.md %})
```
