---
title: MarkDown
tags:
- markdown
- jekyll
- hugo
---

Markdown is a lightweight markup language with plain-text-formatting syntax. Its design allows it to be converted to many output formats.
<!--more-->

## Italics

To italises a word just wrap it in either a `_` or `*`, for example:

With underscores `_italics_` => _italics_
With asterix `*italics*` => *italics*

## New line

By default Markdown will collapse lines separated but just a carriage return will be collapsed into one line.  
To display the lines separately add **two (2) spaces and a carriage return**.

## Collapsible Sections

[This gist post](https://gist.github.com/pierrejoubert73/902cc94d79424356a8d20be2b382e1ab) shows how you can use a collapsible section in markdown.

``` markdown
    # A collapsible section with markdown
    <details>
    <summary>Click to expand!</summary>
    
    ## Heading
    1. A numbered
    2. list
        * With some
        * Sub bullets
    </details>
```

## Blockquotes
To create a blockquote, add a > in front of a paragraph.

``` md
> Dorothy followed her through many of the beautiful rooms in her castle.
```
The rendered output looks like this:

> Dorothy followed her through many of the beautiful rooms in her castle.
