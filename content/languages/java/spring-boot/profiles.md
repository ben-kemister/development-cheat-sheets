---
title: "Profiles"
date: 2024-02-05T15:30:40+11:00
tags:
  - java
  - springboot
---

This page contains information, syntax, and simple code examples, about using SpringBoot profiles.
<!--more-->

## Using profiles

To use one (or more) SpringBoot profiles you can:

* Set the `spring.profiles.active` property in the main application property file, or
* Add the `-Dsping.profiles.active=<PROFILE_NAME>` system variable when running the app

## Profiles and properties files

You can use profiles to load specific [properties](./properties) files when they are active.
These additional properties files use the naming convention `application-<PROFILE_NAME>.yaml` or `application-<PROFILE_NAME>.properties`
and will load the contents of these files overriding any properties present in the default/main properties file.
