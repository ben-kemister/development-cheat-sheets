---
title: JPA
---

# Java Persistence API (JPA)

This page contains information, syntax, and simple code examples, about the use of the Java Persistence API (JPA).

# Case-insensitive **like** matching

To make your query case-insensitive, convert both your keyword and the compared field to lower case:

{% highlight java %}
    query.where(
        builder.or(
            builder.like(
                builder.lower(
                    root.get(
                        type.getDeclaredSingularAttribute("username", String.class)
                    )
                ), "%" + keyword.toLowerCase() + "%"
            )
    );
{% endhighlight %}


