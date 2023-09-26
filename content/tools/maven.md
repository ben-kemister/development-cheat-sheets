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

### Overriding Parent Maven Plugins

If you are inheriting from a parent or using some defaults plugins you can override the version of the maven plugin(s) that 
are being used in the child project by adding an entry to the ``<build><pluginManagement>`` section of the `pom.xml` file.

```xml
<project>
    ...
    <build>
        <pluginManagemnt>
            <plugins>
                <!-- Overrides the version of the plugin defined the in the parent pom.xml file -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>3.1.2</version>
                </plugin>
            </plugins>
        </pluginManagemnt>
    </build>
</project>
```

### Troubleshooting

#### JUnit 5 Tests not Running Under Maven

If you run into an issue where your JUnit5 tests run within the IDE but don't run in maven it may be due to the version 
of the `maven-surefire-plugin` or `maven-failsafe-plugin` that is being used.
Older versions of these plugins do not work with JUnit 5.

The fix is to define a newer version (ideally the latest available) of these plugins in your projects `pom.xml`.

```xml
<project>
    ...
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.1.2</version>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>3.1.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

I stumbled across [this DZone article](https://dzone.com/articles/why-your-junit-5-tests-are-not-running-under-maven) 
which helped me solve my issue.

#### JDK8 sun.* classes missing in build???

If you are using any of the `sun.*` classes directly (such as `sun.security.x509.CertAndKeyGen`) in your project 
keep in mind that these fall outside the [standard Java platform](https://en.wikipedia.org/wiki/Java_Platform,_Standard_Edition).

These are JDK implementation specific classes so may not be available in all flavours of Java. 
If they are available in the version you are using you can use the `-XDignore.symbol.file` compiler argument to allow
access to these classes. 

You can add this argument to the `maven-compiler-plugin` in your projects `pom.xml` so that it is applied automatically.

```xml
<project>
    ...
    <build>
        <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.2</version>
              <configuration>
                 <fork>true</fork>
                 <compilerArgument>-XDignore.symbol.file</compilerArgument>
              </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

I came across [this stakoverflow post](https://stackoverflow.com/questions/29060064/sun-security-x509-certandkeygen-and-sun-security-pkcs-pkcs10-missing-in-jdk8) 
which helped me solve my issue.