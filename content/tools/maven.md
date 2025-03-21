---
title: Maven
tags:
 - maven
 - java
 - development
---

Maven is a build automation tool used primarily for Java projects. Maven can also be used to build and manage projects 
written in C#, Ruby, Scala, and other languages. 
<!--more-->
The Maven project is hosted by the Apache Software Foundation, where it was formerly part of the Jakarta Project.

## Handy Commands

| Command | Description |
| ----  | ---- |
| `mvn dependency:tree` | Print Dependency Tree |
| `mvn test-compile` | Compiles the test classes (but doesn't run them) |
| `mvn archetype:generate` | Interactively generates/initialised a maven project |

## Scopes

Maven has **six** default dependency scopes. It’s important to understand that each scope (except for import) has an impact on transitive dependencies.

* compile - default scope when no other scope is provided. Dependencies with this scope are available on the classpath of the project in all build tasks.
* provided - dependencies that should be provided at runtime by JDK or a container.
* runtime - dependencies with this scope are required at runtime, but are not needed for compilation of the project. Dependencies marked with the runtime scope will be present in the runtime and test classpath, but they will be missing from the compile classpath.
* test - indicates that the dependency isn’t required at standard runtime of the application but is used only for test purposes. Test dependencies aren’t transitive and are only present for test and execution classpaths.
* system (deprecated) - similar to the provided scope, but requires a direct reference to a specific jar on the system.
* import (pom) - indicates that this dependency should be replaced with all effective dependencies declared in its POM

For more information see [this Baelding article](https://www.baeldung.com/maven-dependency-scopes).

## Downloading an artifact to a specific directory

You can use maven to download an artifact from Nexus/Maven Central even if you don't have a `pom.xml` file using:

```shell
mvn dependency:copy -Dartifact="io.opentelemetry.javaagent:opentelemetry-javaagent:1.33.6:jar" -DOutputDirectory="." -DuseBaseVersion=true "-Dmdep.stripVersion=true"
```

This will download version 1.33.6 of the opentelemetry-javaagent jar file as `opentelemetry-javaagent.jar`.

## (Re) Download project dependencies

In Maven, you can use Apache Maven Dependency Plugin, goal `dependency:purge-local-repository` to remove the project dependencies from the local repository, and re-download it again.

``` sh
mvn dependency:purge-local-repository
```

## Excluding a module from being published

```xml
...
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-deploy-plugin</artifactId>
            <configuration>
                <!-- Do not deploy this artifact -->
                <skip>true</skip>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## Add local jars file to Maven

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>library</artifactId>
    <version>1.0</version>
    <scope>system</scope>
    <systemPaht>${project.basedir}/your-file.jar</systemPaht>
</dependency>
```

## Publishing Artifacts

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

## Generating sources from (SOAP) WSDL

This one took me a bit to figure out so recording it here for future use/recollection.

This solution is based on the [Apache cxf-codegen-plugin](https://cxf.apache.org/docs/maven-cxf-codegen-plugin-wsdl-to-java.html) which worked well for my case.

```xml
<project>
    ...
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.cxf</groupId>
                <artifactId>cxf-codegen-plugin</artifactId>
                <version>4.0.3</version>
                <executions>
                    <execution>
                        <id>generate-hello-world-sources</id>
                        <configuration>
                            <sourceRoot>${project.build.dir}/generated-sources</sourceRoot>
                            <wsdlOptions>
                                <wsdl>${project.baseDir}/src/main/resources/wsdl/HelloWorld.wsdl</wsdl>
                            </wsdlOptions>
                        </configuration>
                        <goals>
                            <goal>wsdl2java</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

You can also generate `toString()` methods and more using xjc plugins, which need to be added as dependencies of the plugin.

## Overriding Parent Maven Plugins

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

## Troubleshooting

### JUnit 5 Tests not Running Under Maven

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
Make sure you check [Maven Central](https://mvnrepository.com/artifact/org.apache.maven.plugins/maven-surefire-plugin) 
for the latest version of the plugins.


I stumbled across [this DZone article](https://dzone.com/articles/why-your-junit-5-tests-are-not-running-under-maven) 
which helped me solve my issue.

### JDK8 sun.* classes missing in build???

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
