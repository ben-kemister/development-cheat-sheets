---
title: Go Templates
date: "2026-07-12"
tags:
 - go
 - templates
---

Go templates are designed to generate output that is structured, such as HTML, XML, or plain text. 
They are part of the standard library via the `text/template` and `html/template` packages.
<!--more-->

Go templates use a specific syntax to inject data and control logic into your output. 
The core of the template is the "dot" (`.`), which represents the current cursor/context in the data structure.

## Basic Syntax

The most fundamental action in a template is accessing data using the dot notation.

### Accessing Fields
If you pass a struct to a template, you can access its exported fields directly.

```gotemplate
text User Name: {{ .Name }} User Email: {{ .Email }}
```

### The Current Context
Using a single dot `{{ . }}` refers to the entire data object passed into the template.

```gotemplate
text Current Data: {{ . }}
```

### Accessing map values

#### Using Dot Notation

If the key is a valid identifier and known ahead of time you can use:
```gotemplate
{{ .myMap.keyName }}
```

#### Using the `index` Function

Required if your key is stored in a variable, contains special characters, or has spaces.

```gotemplate
{{ index .myMap $keyVariable }}
```

## Control Structures

Templates allow you to use logic to control what content is rendered.

### Conditionals (if/else)
You can use `if`, `else if`, and `else` to perform conditional rendering.

```gotemplate
text {{ if .IsAdmin }}
Welcome, Administrator!
{{ else if .IsEditor }}
Welcome, Editor!
{{ else }}
Welcome, Guest!
{{ end }}
```

### Comparison Functions
To perform comparisons, use built-in functions like `eq` (equal), `ne` (not equal), `lt` (less than), and `gt` (greater than).

```gotemplate
text {{ if eq .Status "active" }} Status: Active {{ else }} Status: {{ .Status }} {{ end }}
{{ if gt .Age 18 }} Access Granted {{ end }}
```

### Iteration (range)
The `range` keyword is used to iterate over slices, arrays, maps, or channels.

```gotemplate
text
{{ range .Items }}
- Item: {{ . }}
{{ else }}
- No items found in the list.
{{ end }}
```

> Note: When using `range`, the context inside the loop (the dot `.`) becomes the current element of the iteration.*

## Variables and Pipelines

### Defining Variables
You can declare variables within a template using the `:=` operator.

```gotemplate
text {{ name := .Name }} {{age := .Age }}
Hello {{ name }}, you are {{age }} years old.
```

### Pipelines
The pipe operator `|` takes the output of one command and passes it as the last argument to the next command.

```gotemplate
text {{ .Title | printf "The title is: %s" }}
```

## Summary Table

| Syntax              | Description                                     |
|:--------------------|:------------------------------------------------|
| `{{ . }}`           | The current data context                        |
| `{{ .Field }}`      | Access a field or method of the current context |
| `{{ if ... }}`      | Conditional logic                               |
| `{{ range ... }}`   | Iteration over a collection                     |
| `{{ $var := ... }}` | Variable declaration                            |
| `{{ ... \| ... }}`  | Pipeline to pass data to a function             |

## Handy Links

* [Go Template Preview](https://gotemplate.io/) - Quickly test and visualize your Go templates live.

  