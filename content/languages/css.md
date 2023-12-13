---
title: Cascading Style Sheets (CSS)
tags:
 - html
 - css
---

Cascading Style Sheets (CSS) is a style sheet language used for describing the presentation of a document written in a markup language such as HTML or XML (including XML dialects such as SVG, MathML or XHTML). 
<!--more-->
CSS is a cornerstone technology of the World Wide Web, alongside HTML and JavaScript.

## Comments

```css
/* This is a single-line comment */
p {
  color: red;
}
```

## Selectors

CSS selectors are very powerful. You can use CSS selectors to target styles to particular html node(s).

### Class selector

```css
/*
    Targets nodes with class="code"
*/
.code {
    /* styles to apply */
}
```

You can also use the selector with an element type, for example:

```css
/*
    Targets <pre> nodes that have class="code"
*/
pre.code {
    /* styles to apply */
}
```

### Id selector

```css
/*
    Targets nodes with id="menu"
*/
#menu {
    /* styles to apply */
}
```

### DOM Descendant combination

```css
/*
    Targets all <a> tags that are a child of <li> tags
*/
ls a {
    /* styles to apply */
}
```