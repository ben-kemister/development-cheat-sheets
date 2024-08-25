---
title: Modules (JMPS)
tags:
- java
- modules
---

The Java Platform Module System (JPMS) was introduced in Java 9 as a higher level of aggregation above packages.
<!--more-->
The key new language element is the **module**—a uniquely named, reusable group of related packages, 
as well as resources (such as images and XML files) and a module descriptor (`module-info.java`) specifying:

* the module’s name
* the module’s dependencies (that is, other modules this module depends on)
* the packages it explicitly makes available to other modules (all other packages in the module are implicitly unavailable to other modules)
* the services it offers
* the services it consumes
* to what other modules it allows reflection

## Module Declarations

To set up a module, we need to put a special file at the root of our package(s) named `module-info.java`, this file is 
known as the module descriptor.

Within the module descriptor (`module-info.java`) we use module directives to describe the module's dependencies and what 
it makes available to other consuming modules.

The module directives are:

* `requires` - allows us to declare module dependencies
* `requires static` - creates a compile-time-only dependency
* `requires transitive` - force any downstream consumers also to read our required dependencies
* `exports` - exposes all public members of the named package
* `exports to` - similar to the `exports` directive, but also lists which modules are allowed to import/requires this package
* `uses` - specifies an interface or abstract class that the module consumes
* `provides ... with` - specifies the interface/abstract class and the implementing class of a services our module provides
* `opens` - explicitly grants permission for other modules to reflect the classes within the module
* `opens ... to` - selectively `opens` our packages to a pre-approved list of modules

Example module descriptor:

```java
module my.module {
    requires com.google.common;
    requires com.google.guice;
    requires jakarta.inject;
    requires java.desktop;
    requires java.prefs;
    requires java.xml;
    //...
    requires transitive org.jfree.chart.fx;
    //...
    requires static org.immutables.value;
    //...
    exports com.example.app.main;
    //...
    exports au.com.kem.shares.main to
            javafx.graphics,
            com.google.guice,
            com.google.common;
    //...
    opens com.example.app.main.core.images;
    //...
    opens com.example.app.main.core to javafx.fxml;
}
```

### requires. 

The `requires` directive allows us to declare module dependencies


## Overriding a modules' configuration

The Java module system is very strict about access to internal APIs. If the package isn't exported or opened, access will be denied. 
But there are also the command line flags `--add-exports` and `--add-opens`, which allow the module's user to override the 
defaults of the module's author.

### Exporting Packages with --add-exports

The option `--add-exports $MODULE/$PACKAGE=$READING_MODULE`, available for the `java` and `javac` commands, 
exports `$PACKAGE` of `$MODULE` to `$READING_MODULE`. Code in `$READING_MODULE` can then access all public types and members 
in `$PACKAGE` but other modules can not. 

> Note that setting `$READING_MODULE` to `ALL-UNNAMED`, all code from the class path can access that package.

The space after `--add-exports` can be replaced with an equal sign `=`, which helps with some tool configurations 
(Maven, for example): `--add-exports=$MODULE/$PACKAGE=$READING_MODULE`

### Opening Packages with --add-opens

The command line option `--add-opens $MODULE/$PACKAGE=$REFLECTING_MODULE` opens `$PACKAGE` of `$MODULE` to `$REFLECTING_MODULE`. 
Code in `$REFLECTING_MODULE` can hence **reflectively** access all types and members, public and non-public ones, 
in `$PACKAGE` but other modules can not. 

> Note as above setting `$READING_MODULE` to `ALL-UNNAMED`, all code from the class path can reflectively access that package.

Since `--add-opens` is bound to reflection, a pure runtime concept, it is only used by the `java` command.
Often tools supply the same arguments to both `java` and `javac`, so `javac` does not reject the option and instead issues 
the warning that "--add-opens has no effect at compile time".

## Changes to loading non-class files

An important impact with the change to the Java modules system is the way in which non-class-file resources are encapsulated 
in a module and the change to operation of `Class.getResource(String someResourceUrl)`.

When in a _named_ module `Class.getResource(String someResourceUrl)` now searches the contents of the named module.
This means if you were previously using this method to load _non-class_ resources from another jar file, they will no longer 
be found by `Class.getResource(String someResourceUrl)` adn will return `null`.

If you need to load _non-class_ resources from another jar file, you either need to use another method (TBC) of you need 
to search across all available classes using a technique as discussed on [this StackOverflow post](https://stackoverflow.com/questions/62228114/getting-the-url-of-resource-in-another-module).

```java
public static Resource getRescoure(String urlStr) {
    try (ScanResult scan = new ClassGraph().scan()) {
        if (urlStr.startsWith("/"))
            urlStr = urlStr.substring(1);
        ResourceList rl = scan.getResourcesWithPath(urlStr);
        if (rl.size() > 0)
            return rl.get(0);
        return null;
    }
}
```

## Links & References

* [A Guide to Java Modularity - baeldung](https://www.baeldung.com/java-modularity)
* [Understanding Java 9 Modules - Oracle](https://www.oracle.com/au/corporate/features/understanding-java-9-modules.html)
* [Circumventing Strong Encapsulation with --add-exports and --add-opens](https://dev.java/learn/modules/add-exports-opens/)
