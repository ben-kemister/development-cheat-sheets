---
title: Hugo
tags:
- go
- github_pages
- hugo
---

[Hugo](https://gohugo.io/) is a **very fast** Static Site Generator (SSG) built with Go.
<!--more-->

## Installation (Windows)

Download the binaries from the [Hugo release page](https://github.com/gohugoio/hugo/releases), typically you will want
the `hugo_extended_<VERSION>_windows-amd64.zip` file.

Unzip the folder to somewhere on your computer, then add the directory to your `PATH`.

You should then be able to get the version using the console command:

```powershell
hugo version
# Returns
hugo v0.120.2-9c2b2414d231ec1bdaf3e3a030bf148a45c7aa17+extended windows/amd64 BuildDate=2023-10-31T16:27:18Z VendorInfo=gohugoio
```

## Running Hugo (locally)

From the root directory of the project run:

```powershell
hugo serve -D --disableFastRender
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

## Hosting on Github pages

See this [hugo page](https://gohugo.io/hosting-and-deployment/hosting-on-github/) for information.

## Handy Commands

| Command                                      | Description                                                           |
|----------------------------------------------|-----------------------------------------------------------------------|
| `hugo`                                       | Builds/compiles the website into the `./public` folder                |
| `hugo serve -D --disableFastRender`          | Serves the Hugo site, watching for changes and rebuilding as required |
| `hugo new posts/2023-06-17-new-blog-post.md` | Creates a new blog post using the appropriate `archetype`             |
