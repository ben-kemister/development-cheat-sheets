---
title: SpringBoot Maven plugin
tags:
- java
- springboot
- maven
- plugin
---

This page contains information, syntax, and simple code examples, about using the 
[spring-boot-maven-plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/).

## Creating runnable 'fat' jar

```xml
...
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>${springboot.version}</version>
            <!-- Create a runnable fat jar -->
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

