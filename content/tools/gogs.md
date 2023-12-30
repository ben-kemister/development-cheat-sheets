---
title: "Gogs"
date: 2023-12-31T07:58:55+11:00
tags:
- git
- repository
- gogs
- development
---

[Gogs](https://gogs.io/) is a lightweight git server which is easy to in a self-hosted environment.
<!--more-->

## Local Webhooks

By default, gogs does not allow the use of local webhooks see: https://github.com/gogs/gogs/discussions/7048 .  
If you try to use local webhooks the gogs UI will display an error.

To allow the use of local you need to change the `LOCAL_NETWORK_ALLOWLIST` values in the `[security]` section of the 
gogs configuration file `/data/gogs/conf/app.ini`.

For example:

```toml
# /data/gogs/conf/app.ini
...
    [security]
    ...
    # specific network address, separated by commas, no port needed
    # For local webhooks to work you need to add the host(s) or '*' here see: https://github.com/gogs/gogs/discussions/7048
    LOCAL_NETWORK_ALLOWLIST = local.address,another.local.address
...
```

