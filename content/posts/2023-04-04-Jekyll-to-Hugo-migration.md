---
title: 'Migrating from Jekyll to Hugo'
date: 2023-04-04T06:12:32+1100
draft: false
tags:
- jekyll
- hugo
- development
- github
- github_pages
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

## Migration Process

> As stated above I prefer to use Docker where I can. 
> Do to a limitation with [Docker on Windows](https://forums.docker.com/t/file-system-watch-does-not-work-with-mounted-volumes/12038/25)
> which prevents Windows file systems changes from propagating into the linux file system within the container. 
> 
> This means Hugo (in Docker) is unable to detect file systems changes and rebuild the site.
> 
> Unfortunately this meant that I couldn't use the [Hugo Docker image](https://hub.docker.com/r/klakegg/hugo) for the migration.

The initial steps below were based upon the [Hugo Quick Start guide](https://gohugo.io/getting-started/quick-start/).

1. Create the basic Hugo project structure with:
    ```shell
    hugo new site quickstart 
    ```

   > Note: from here on out you need to use the **quickstart** directory for all of the hugo commands/content.

2. (Optional) If your project/folder is not a git repo initialise one there with `git init`

3. Add a cool theme, I selected the [relearn](https://themes.gohugo.io/themes/hugo-theme-relearn/) theme, by:
   ```shell
    hugo mod init github.com/<your_user>/<your_project>
    ```
   In my case this was ``hugo mod init github.com/ben-kemister/development-cheat-sheets``

4. Update the `config.toml` with the theme module to:
    ```toml
    [module]
    [[module.imports]]
    path = 'github.com/McShelby/hugo-theme-relearn'
   
   # Change the default theme to be use when building the site with Hugo
   theme = "hugo-theme-relearn"
    ```
   
5. Create the first post with:
    ```shell
    hugo new posts/my-first-hugo-post.md 
    ```
   
6. Start serving the site (showing drafts) with:
    ```shell
    hugo serve -D --disableFastRender
    ```
   
7. Enable the search functionality, within `config.toml` add:
   ```toml
   # For search functionality
   [outputs]
   home = [ "HTML", "RSS", "SEARCH", "SEARCHPAGE"]
   ```
8. Add my favicon to the site `static/images/favicon.png` as per the [theme's instructions](https://mcshelby.github.io/hugo-theme-relearn/basics/customization/index.html#change-the-favicon).
9. Move and update the Jekyll pages (`*.md` files) to the Hugo content directory and syntax. This mostly involved the following changes:
   * copying the page to Hugo's `content` directory
   * updated the page's frontmatter by changing any `name` properties to be `title`
   * creating `_index.md` files for any group/sections
   * adding `{{% children sort="title" %}}` to autogenerate child pages where required
   * fixed syntax highlighting by replacing `{% highlight java %}` blocks with ```java blocks

10. Make sure to commit the `go.mod` and `go.sum` files

11. Deleted the remaining Jekyll files and folders and moved the contents of the `quickstart` directory into the root of the git repository/

12. Lastly, followed [Hugo's Host on GitHub instruction](https://gohugo.io/hosting-and-deployment/hosting-on-github/) to 
    be able to host the site on github pages.

**All Done!**
