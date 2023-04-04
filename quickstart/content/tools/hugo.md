---
title: Hugo
tags:
- go
- github_pages
---

Hugo is a very fast Static Site Generator (SSG) built with Go
<!--more-->

## Commands

| Command | Description |
| ------- | ------------ |
| `hugo serve -D --disableFastRender` | Serves the Hugo site, watching for changes and rebuilding as required |


## Links

Within markdown files you can use links as follows:
```markdown
Github uses [git]({{< ref "/tools/git.md">}})
```
