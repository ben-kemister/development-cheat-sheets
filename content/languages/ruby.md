---
title: Ruby
tags:
 - ruby
 - jekyll
---

[Ruby](https://www.ruby-lang.org/en/) is an interpreted, high-level, general-purpose programming language which supports multiple programming paradigms. 
It was designed with an emphasis on programming productivity and simplicity. 
In Ruby, everything is an object, including primitive data types.
<!--more-->

# Ruby bundler
[Bundler](https://bundler.io/) provides a consistent environment for **Ruby** projects by tracking and installing the exact gems and versions that are needed.

The `Gemfile.lock` contains the version information for each of the gems used in the Ruby project.

## Updating gems

To update a specific gem use `bundle update <GEM_NAME>`

For example: `bundle update github-pages`

Use `bundle info [gemname]` to see where a bundled gem is installed.

### Automatic Update
**Warning this can break your project!**
If you run `bundle update --all`, which will ignore the `Gemfile.lock`, it resolve all the dependencies (specified in the `Gemfile`) again. Keep in mind that this process can result in a significantly different set of gems, based on the requirements of new gems that the gem authors released since the last time you ran `bundle update --all`.

