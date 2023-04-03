---
title: 'Migrating from Jekyll to Hugo'
date: 2023-03-30T22:12:32Z
draft: false
tags:
- jekyll
- hugo
- development
- github
---

This post contains information about the migration of my Github development-cheat-sheets page (this site) from Jekyll.
<!--more-->

## Background

Jekyll has a notoriously slow build time.

Ror me this is a real annoyance. I don't claim to be the best developer and believe that you need to be able to iterate
quickly through a change-test-fix cycle when developing anything.

Additionally, I like to use Docker containers for anything that has a complex setup/configuration. Using Docker containers
allows me to setup/configure something once and have it run the same way where-ever it is deplopyed/used.
 
Jekyll's slow build times, even with a relatively small static website like this one, and its relatively difficult Ruby 
installation and setup has made me look for an alternative static site generator.

Enter Hugo...

## Hugo

## Migration Process

    As stated above I prefer to use Docker where I can, so the details below all use the 
    [klakegg/hugo](https://hub.docker.com/r/klakegg/hugo) docker image.

1. Create the basic Hugo project structure with:
    ```shell
    docker run --rm -it `
       -v "$($pwd):/src" `
       $DOCKER_REG/klakegg/hugo:0.101.0-ext new site quickstart 
    ```

    Note: from here on out you need to use the dir **quickstart** for all of the hugo commands

2. (Optional) If your project/folder is not a git repo initalise one there with `git init`

3. Add a cool theme, I selected the [relearn](https://themes.gohugo.io/themes/hugo-theme-relearn/) theme, by:
   ```shell
    docker run --rm -it `
    -v "$($pwd)/quickstart:/src" `
    $DOCKER_REG/klakegg/hugo:0.101.0-ext mod init github.com/<your_user>/<your_project>
    ```
4. (Optional) I find it easier to use yaml files so I changed the `config.toml` to `config.yaml` and fixed its contents.
5. Updated the `config.yaml` with the theme module so:
    ```yaml
    module:
       imports:
          - path: github.com/spf13/hyde
    ```
6. Create the first post with:
    ```shell
    docker run --rm -it `
       -v "$($pwd)/quickstart:/src" `
       $DOCKER_REG/klakegg/hugo:0.101.0-ext new posts/my-first-hugo-post.md 
    ```
7. And start serving the site (showing drafts) with:
    ```shell
    docker run --rm -it --name hugo `
       -v "$($pwd)\quickstart:/src" `
       -p 1313:1313 `
       $DOCKER_REG/klakegg/hugo:0.101.0-ext server -D
    ```
   To open with windows...
   ``hugo serve -D --disableFastRender``


