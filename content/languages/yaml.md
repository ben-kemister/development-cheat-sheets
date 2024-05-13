---
title: YAML
tags:
- yaml
---

YAML is a human-readable data serialization language that is often used for writing configuration files. 
<!--more-->
Depending on whom you ask, YAML stands for yet another markup language or YAML ain't markup language (a recursive acronym), 
which emphasizes that YAML is for data, not documents.

## Arrays

You can represent arrays in YAML in two different ways:

```yaml
# Block sequence
colors:
  - red
  - green
  - blue
  - orange
```

```yaml
# Flow sequence
colors: [red, green, blue, orange]
```
