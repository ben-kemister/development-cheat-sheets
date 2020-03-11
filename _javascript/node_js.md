---
title: Node.js
---

Node.js is an open-source, cross-platform, JavaScript runtime environment that executes JavaScript code outside of a browser. 

Node.js lets developers use JavaScript to write command line tools and for server-side scripting—running scripts server-side to produce dynamic web page content before the page is sent to the user's web browser. 

Consequently, Node.js represents a "JavaScript everywhere" paradigm, unifying web-application development around a single programming language, rather than different languages for server- and client-side scripts. [Wikipedia](https://en.wikipedia.org/wiki/Node.js)

# Node Versions and ES Support

According to [node.green](https://node.green/) version 10, or higher, covers 99%  of ES2015 (ES6) support.

# NPM (Node Package Manager)

## Project Dependencies

### Dev Dependencies

To install a library as a Dev dependency use the `--save-dev` at the end:

{% highlight powershell %}

    PS> npm i ts-jest --save-dev
    // Installs as a devDependencies in the package.json
    PS> npm i jest --save-dev
   
{% endhighlight %}

## Global Libraries

In global mode (ie, with `-g` or `--global` appended to the command), it installs the current package context (ie, the current working directory) as a global package. For more information see [npm-install](https://docs.npmjs.com/cli/install).

### Install a Global Library

{% highlight powershell %}

    PS> npm install -g serverless
   
{% endhighlight %}

### List Global Libraries (and locations)

{% highlight powershell %}

    // To see which global libraries are installed and where they're located
    PS> npm list -g

    // To see just the path run
    PS> npm list -g | head -1
   
{% endhighlight %}

