---
title: Hibernate
tags:
- java
- persistence
---

[Hibernate ORM](https://hibernate.org/) is an object–relational mapping tool for the Java programming language.
<!--more-->
It provides a framework for mapping an object-oriented domain model to a relational database.

## Hibernate 6 

Used in Spring boot 3.x.x and Spring 6

### `@Type` annotation

In previous versions of hibernate you could use the `@Type` annotation which converts db column from String (Y or N) to 
java `boolean` value.

```java
    @Column(name = "IS_SPECIAL")
    @Type(type = "yes_no")
    private Boolean isSpecial;
```

In hibernate 6 the `@Type(type = "yes_no")` can be replaced with `@Convert(converter = YesNoConverter.class)`

```java
    @Column(name = "IS_SPECIAL")
    @Convert(converter = org.hibernate.type.YesNoConverter.class)
    private Boolean isSpecial;
```

More information see this [hibernate doco](https://docs.jboss.org/hibernate/orm/6.1/userguide/html_single/Hibernate_User_Guide.html#basic-boolean).

### NativeQuery parameter type

If you get an error like ``Could not resolve NativeQuery parameter type : org.hibernate.query.internal.QueryParameterPositionalImpl@22``
it is because you cannot use **self-made** java entities in native queries.


