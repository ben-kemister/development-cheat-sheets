---
title: SpringBoot Task Scheduling
tags:
- java
- springboot
- actuator
- webservices
- health
- monitoring
- schedule
---

This page contains information, syntax, and simple code examples, about [Springs' Task scheduling](https://spring.io/guides/gs/scheduling-tasks/).

## Basic Example

```java
@Component
public class ScheduledTasks {

	private static final Logger log = LoggerFactory.getLogger(ScheduledTasks.class);

	private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

	@Scheduled(fixedRate = 5000)
	public void reportCurrentTime() {
		log.info("The time is now {}", dateFormat.format(new Date()));
	}
}
```

## Troubleshooting

### My Scheduled Task runs twice

Make sure that you do not use the `@Scheduled` annotation within a configuration class (such as one annotated with `@Configuration`).
See the note below from the [spring docs](https://docs.spring.io/spring-framework/docs/3.0.x/reference/scheduling.html#scheduling-annotation-support-scheduled):

> Make sure that you are not initializing multiple instances of the same @Scheduled annotation class at runtime, 
> unless you do want to schedule callbacks to each such instance. 
> Related to this, make sure that you do not use `@Configurable` on bean classes which are annotated with `@Scheduled` and registered as regular Spring beans with the container: 
> You would get double initialization otherwise, once through the container and once through the @Configurable aspect, with the consequence of each @Scheduled method being invoked twice.

## Links

* [Baeldung: A Guide to the Spring Task Scheduler](https://www.baeldung.com/spring-task-scheduler)
* [Baeldung: The @Scheduled Annotation in Spring](https://www.baeldung.com/spring-scheduled-tasks)
* [Spring: Scheduling Tasks](https://spring.io/guides/gs/scheduling-tasks/)

