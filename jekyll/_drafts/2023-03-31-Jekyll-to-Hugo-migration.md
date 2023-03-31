---
title: 'Migrating from Jekyll to Hugo'
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

Additionally I like to use Docker containers for anything that has a complex setup/configuration. Using Docker containers
allows me to setup/configure something once and have it run the same way where-ever it is deplopyed/used.
 
Jekyll's slow build times, even with a relatively small static website like this one, and its relatively difficult Ruby 
installation and setup has made me look for an alternative static site generator.

Enter Hugo...

## Hugo

## Migration Process

```shell
docker run --rm -it `
  -v $($pwd):/src `
  klakegg/hugo:0.101.0-ext new site quickstart `

```