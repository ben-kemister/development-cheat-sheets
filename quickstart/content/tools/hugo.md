---
title: Hugo
tags:
- go
- github_pages
---


On Windows

```shell
docker run --rm -it `
  -v $(pwd):/src `
  klakegg/hugo:0.101.0-ext new site quickstart `
```
