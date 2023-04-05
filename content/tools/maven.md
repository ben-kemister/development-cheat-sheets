---
title: Maven
tags:
 - maven
 - java
 - development
--- 

Maven is a build automation tool used primarily for Java projects. Maven can also be used to build and manage projects written in C#, Ruby, Scala, and other languages. The Maven project is hosted by the Apache Software Foundation, where it was formerly part of the Jakarta Project.
<!--more-->
## Handy Commands

| Command | Description |
| ----  | ---- |
| `mvn dependency:tree` | Print Dependency Tree |
| `mvn test-compile` | Compiles the test classes (but doesn't run them) | 


### (Re) Download project dependencies

In Maven, you can use Apache Maven Dependency Plugin, goal `dependency:purge-local-repository` to remove the project dependencies from the local repository, and re-download it again.

``` sh
mvn dependency:purge-local-repository
```

### Publishing Artifacts

You will need a `distributionManagement` entry in your projects `pom.xml`

```xml
<project ...>
    ...
    <distributionManagement>
        <snapshotRepository>
            <id>snapshots</id>
            <url>https://<YOUR_REPO_HOST>/repository/maven-snapshots</url>
        </snapshotRepository>
    </distributionManagement>
    ...
</project>
```

And you will need an entry in your `~/.m2/settings.xml`

```xml
<settings ...>
    <servers>
        <server>
            <id>central</id>
            <username>USER_NAME</username>
            <password>PASSWORD</password>
        </server>
    </servers>
</settings>
```

Then you can publish/deploy the artefact with the command:

```sh
mvn clean deploy -Dmaven.test.skip=true
```

For more information [this baeldung article](https://www.baeldung.com/maven-deploy-nexus)

