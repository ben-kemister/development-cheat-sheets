---
tool: maven
name: Maven
tags:
 - maven
 - java
--- 

Maven is a build automation tool used primarily for Java projects. Maven can also be used to build and manage projects written in C#, Ruby, Scala, and other languages. The Maven project is hosted by the Apache Software Foundation, where it was formerly part of the Jakarta Project.
<!--more-->
## Handy Commands

### (Re) Download project dependencies

In Maven, you can use Apache Maven Dependency Plugin, goal `dependency:purge-local-repository` to remove the project dependencies from the local repository, and re-download it again.

``` sh
mvn dependency:purge-local-repository
```
