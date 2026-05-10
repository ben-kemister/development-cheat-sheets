---
title: inotify
tags:
- linux
---

`inotify` is a Linux kernel subsystem that monitors file systems and immediately notifies applications when files or 
directories are created, modified, deleted, or moved.
<!--more-->

## Viewing current settings

You can query the current values of the system with `sysctl fs.inotify.<PROPERTY>` for example:

```shell
# Check max_user_instances value
$sysctl fs.inotify.max_user_instances
fs.inotify.max_user_instances = 128

# Check max_user_watches value
$ sysctl fs.inotify.max_user_watches
fs.inotify.max_user_watches = 122962
```

> It should be noted that container runtime engines (like Podman and Docker) inherit these values from the host.

## Reaching limits

If you have an application (or many applications) that uses a lot of watches/instances you may run into issues and see 
errors in the log like:

```text
Unhandled exception. System.IO.IOException: The configured user limit (128) on the number of inotify instances has been reached, or the per-process limit on the number of open file descriptors has been reached.
at System.IO.FileSystemWatcher.StartRaisingEvents()
at Microsoft.Extensions.FileProviders.Physical.PhysicalFilesWatcher.TryEnableFileSystemWatcher()
at Microsoft.Extensions.FileProviders.Physical.PhysicalFilesWatcher.CreateFileChangeToken(String filter)
at Microsoft.Extensions.Primitives.ChangeToken.ChangeTokenRegistration`1..ctor(Func`1 changeTokenProducer, Action`1 changeTokenConsumer, TState state)
   at Microsoft.Extensions.Primitives.ChangeToken.OnChange(Func`1 changeTokenProducer, Action changeTokenConsumer)
at Microsoft.Extensions.Configuration.FileConfigurationProvider..ctor(FileConfigurationSource source)
at Microsoft.Extensions.Configuration.Json.JsonConfigurationSource.Build(IConfigurationBuilder builder)
at Microsoft.Extensions.Configuration.ConfigurationBuilder.Build()
at Jellyfin.Server.Program.CreateAppConfiguration(StartupOptions commandLineOpts, IApplicationPaths appPaths)
at Jellyfin.Server.Program.StartApp(StartupOptions options)
at Jellyfin.Server.Program.<Main>(String[] args)
```

### Why This Happens

* **Inotify Instances**: Each application creates an "instance" to start monitoring file changes. 
    The default limit of 128 is often too low if you run multiple containers or complex apps.
* **Inotify Watches**: These are the actual directories being monitored. 
    Applications like Jellyfin's "Real-time monitoring" feature uses these to detect new media, and large libraries can 
    easily exceed the default system limit.
* **File Descriptors**: Every open file or network connection uses a descriptor. 
    While increasing inotify limits usually fixes the error, you can also check your Docker ulimits if the problem persists.

## Increasing limits

### Temporary Change

You can temporarily increase the limits with the command `sudo sysctl -w fs.inotify.<PROPERTY>=<NEW_VALUE>`, for example:
```shell
# Temporarily increase limits for the current session
sudo sysctl -w fs.inotify.max_user_instances=1024
sudo sysctl -w fs.inotify.max_user_watches=524288
```

### Permanent Change

To ensure these changes survive a reboot, you must add them to the system configuration file or a `*.conf` file in 
`/etc/sysctl.d`.

The commands below increase the `max_user_watches` and `max_user_instances` and reloads the configuration on **Ubuntu**:

```shell
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.d/40-max-user-watches.conf
echo fs.inotify.max_user_instances=1024 | sudo tee -a /etc/sysctl.d/40-max-user-watches.conf
sudo service procps force-reload
```

