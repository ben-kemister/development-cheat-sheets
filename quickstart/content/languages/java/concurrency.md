---
title: Concurrency
tags:
- java
---

# Java Concurrency

This page contains information, syntax, and simple code examples, about the use of Java's concurrency functionality.

# Common ForkJoin Pool

Java has a common `static` ForkJoin pool which is appropriate for most applications.

Using the common pool normally reduces resource usage (its threads are slowly reclaimed during periods of non-use, and reinstated upon subsequent use).

In practice what this means is there is an easy way to submit tasks to a background thread without needing to deal with the complexities of an ExecutorService.

```java
javafx.concurrent.Task<String> longRunningTask = new Task<String>() {

	@Override
	protected String call() throws Exception {
		//The call() method is run on a background thread.
		return longRunningStringProducer();
	}
};

ForkJoinPool.commonPool().execute(longRunningTask);
```

See the [ForkJoinPool JavaDocs](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html) for more information.
