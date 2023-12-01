---
title: Page syntax
tags:
- go
- hugo
---

This page provides examples of how to use the Hugo page syntax.
<!--more-->

## Front Matter

The `date` field in the front matter is used by Hugo to determine if the post should be rendered in the output.
If the value of `date` is in the future **it will not be rendered**.

## Links

Within markdown files you can use links as follows:
```markdown
Github uses [git]({{< ref "/tools/git.md">}})
```
