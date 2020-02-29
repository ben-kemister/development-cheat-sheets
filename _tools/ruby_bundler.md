---
tool: ruby_bundler
name: (Ruby) Bundler
--- 

[Bundler](https://bundler.io/) provides a consistent environment for **Ruby** projects by tracking and installing the exact gems and versions that are needed.
<!--more-->
# Update gems with Bundler

The `Gemfile.lock` contains the version information for each of the gems used in the Ruby project.

## Automatic Update
**Warning this can break your project!**
If you run `bundle update --all`, which will ignore the `Gemfile.lock`, it resolve all the dependencies (specified in the `Gemfile`) again. Keep in mind that this process can result in a significantly different set of gems, based on the requirements of new gems that the gem authors released since the last time you ran `bundle update --all`.

