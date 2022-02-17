---
title: Node.js
---

Node.js is an open-source, cross-platform, JavaScript runtime environment that executes JavaScript code outside of a browser. 

Node.js lets developers use JavaScript to write command line tools and for server-side scripting—running scripts server-side to produce dynamic web page content before the page is sent to the user's web browser. 

Consequently, Node.js represents a "JavaScript everywhere" paradigm, unifying web-application development around a single programming language, rather than different languages for server- and client-side scripts. [Wikipedia](https://en.wikipedia.org/wiki/Node.js)

# Node Versions and ES Support

According to [node.green](https://node.green/) version 10, or higher, covers 99%  of ES2015 (ES6) support.

# NPM (Node Package Manager)

>Note: Most of the commands below can be run in **Global** mode by appending `-g` or `--global` to the command.

## `install` a package

Project dependencies (packages) are installed by using the cli command `npm install <package>` or `npm i <package>`.

### Install Dev Dependencies

To install a library as a Dev package use the `--save-dev` at the end:

{% highlight powershell %}

    PS> npm i ts-jest --save-dev
    // Installs as a devDependencies in the package.json
    PS> npm i jest --save-dev
   
{% endhighlight %}

### Install a specific version

By default npm will install the latest version of a package, you can override this behavior if you need to install a specific version by using the following cli syntax `npm i <package>@<version>`. If you want that exact version specified in your `package.json` dependencies add the `--save --save-exact` flags. for example:

{% highlight powershell %}

    // Removes the package and updates the package.json
    PS> npm i serverless-offline@5.12.1 --save-dev --save-exact
{% endhighlight %}

## `remove` a package

Packages can be removed by using the cli command `npm remove <package>`. To also update the `package.json` file add the `--save` flag:

{% highlight powershell %}

    // Removes the package and updates the package.json
    PS> npm remove jest --save
{% endhighlight %}

## [list](https://docs.npmjs.com/cli/ls.html) packages installed

The `npm list` command will print to stdout all the versions of packages that are installed, as well as their dependencies, in a tree-structure.

{% highlight powershell %}

    // Prints out all packages for the project
    PS> npm list

    // Prints out the version of the `tuyapi` package
    PS> npm list tuyapi

    // To see which global libraries are installed and where they're located
    PS> npm list -g

    // To see just the path run
    PS> npm list -g | head -1
{% endhighlight %}

## [outdated](https://docs.npmjs.com/cli/outdated) packages

The `npm outdated [<package> ...]` command  will check the registry to see if any (or, specific) installed packages are currently outdated.

{% highlight shell %}

    /etc/openhab2/scripts/tuya-mqtt$ npm outdated
    Package        Current  Wanted  Latest  Location
    color-convert    1.9.3   1.9.3   2.0.1  tuya-mqtt
    mqtt             3.0.0   3.0.0   4.0.0  tuya-mqtt
    tuyapi           5.1.3   5.2.1   5.2.1  tuya-mqtt
    /etc/openhab2/scripts/tuya-mqtt$

{% endhighlight %}

## [update](https://docs.npmjs.com/cli/update) packages

The `npm update [-g] [<pkg>...]` command will update all the packages listed to the latest version.

{% highlight shell %}

    /etc/openhab2/scripts/tuya-mqtt$ npm update tuyapi
    + tuyapi@5.2.1
    updated 3 packages and audited 357 packages in 10.155s
    /etc/openhab2/scripts/tuya-mqtt$

{% endhighlight %}


