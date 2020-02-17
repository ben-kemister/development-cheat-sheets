---
tool: jekyll
name: Jekyll
--- 

Jekyll is a Ruby tool which can generate static websites and blogs using plain text files.

# Running (Locally)

You can run Jekyll locally by using the following command `bundle exec jekyll serve`

# Configuration ( `_config.yml`)

## baseurl

Use `baseurl` when you are building a site that doesn’t sit at the root of the domain. For example:

'http://jekyll.github.io/example'

In this case you would need to set `baseurl: /example` in the `_config.yml` file otherwise the automatically generated links will not include the `/example`  in the url path. 

For more info see [Clearing Up Confusion Around baseurl -- Again](https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/)

# Links (to site pages)

Links to other pages in Jekyll follow the basic [markdown]({% link _languages/markdown.md %}) format, but with a little liquid (I think) difference to insert the url details.

{% highlight markdown %}

   [Link to a document]({% link _tools/jekyll.md %})

{% endhighlight %}