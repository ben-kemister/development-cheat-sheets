---
tool: 
name: Jekyll
--- 

Jekyll is a Ruby tool which can generate static websites and blogs using plain text files.

# Configuration ( `_config.yml`)

## baseurl

Use `baseurl` when you are building a site that doesn’t sit at the root of the domain. For example:

'http://jekyll.github.io/example'

In this case you would need to set `baseurl: /example` in the `_config.yml` file otherwise the automatically generated links will not include the `/example`  in the url path. 

For more info see [Clearing Up Confusion Around baseurl -- Again](https://byparker.com/blog/2014/clearing-up-confusion-around-baseurl/)

# Links

`[Link to a document]({% link _javascript/tools.md %})`