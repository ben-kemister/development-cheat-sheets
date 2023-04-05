---
title: Hugo
tags:
- go
- github_pages
---

[Hugo](https://gohugo.io/) is a very fast Static Site Generator (SSG) built with Go
<!--more-->

## Commands

| Command | Description |
| ------- | ------------ |
| `hugo serve -D --disableFastRender` | Serves the Hugo site, watching for changes and rebuilding as required |

## Page syntax

### Links

Within markdown files you can use links as follows:
```markdown
Github uses [git]({{< ref "/tools/git.md">}})
```

## Using Hugo in Docker

> **WARNING**: Changes to the pages will not be detected when running Hugo in Docker on a **Windows host**.
>
> Do to a limitation with [Docker on Windows](https://forums.docker.com/t/file-system-watch-does-not-work-with-mounted-volumes/12038/25)
> which prevents Windows file systems changes from propagating into the linux file system within the container, 
> Hugo will be unable to detect file systems changes and rebuild the site.

Hugo publishes a [docker image](https://hub.docker.com/r/klakegg/hugo). 
Using it is easy, you just need to make sure to:

* add the volume where the Hugo site files
* open a port to be able to view the site


To serve the site using docker run:

```powershell
docker run --rm -it --name hugo `
       -v "$($pwd)\quickstart:/src" `
       -p 1313:1313 `
       [$DOCKER_REG/]klakegg/hugo:0.101.0-ext server -D
```
