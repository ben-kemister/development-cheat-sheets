---
title: "jq"
tags:
- unix
- linux
- json
---

[jq](https://jqlang.github.io/jq/) is like sed for JSON data - you can use it to slice and filter and map and transform 
structured data easily.
<!--more-->

## Updating json values

```shell
jq '.property = "Value" ./some.json
```

## Updating multiple values

```shell
jq '.firstProperty = "Value 1" | .secondProperty = "Value 2"' ./some.json
```

## Updating from variables

```shell
MY_VAR=my_value
jq --arg property = "$MY_VAR" '.aProperty = $property' ./some.json
```
