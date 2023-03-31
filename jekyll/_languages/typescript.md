---
name: TypeScript
language: typescript
---

TypeScript is effectively a method to bring Strong typing (and more) to the JavaScript Language.
<!--more-->
TypeScript is a Typed superset of [JavaScript]().

It adds helpful and productive features without changing JavaScript, and is often used in larger or more complex projects.

# Static typing

TypeScript brings static typing to the JavaScript language, this means:
* Variables are assigned a type, and that type never changes.
* Fields must be predefined and can't be added manually.

Static typing enables better tooling (refactoring, autocomplete etc.) and catches error at compile time, rather than runtime. 

It also better enables the Developer to convey their intent of their code.

# The TypeScript Transpiler

The transpiler converts TypeScript code into JavaScript. As a part of this process it strips out all of the type decorations out of the code.

# Initialising a TypeScript Project

## Dependencies
First, you need TypeScript installed of course. You can decide to have it installed globally on your machine, or locally to your project.

And to install locally, run:

{% highlight powershell %}

    PS > npm i typescript --save-dev
{% endhighlight %}

## Setup
To initialize a TypeScript project, simply use the `--init` flag.

{% highlight powershell %}

    PS > tsc --init
{% endhighlight %}

After running tsc with the --init flag, a tsconfig.json will be added to your project folder with a few sensible defaults and an extensive list of commented-out possible configurations. 
