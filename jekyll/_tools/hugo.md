---
tool: hugo
name: Hugo
tags:
- go
- github
---


On Windows

```shell
docker run --rm -it `
  -v $(pwd):/src `
  klakegg/hugo:0.101.0-ext new site quickstart `

```
