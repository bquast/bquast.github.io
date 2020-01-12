---
layout: single
title: "Running podman on Arch Linux"
permalink: running-podman-arch-linux
tags: [podman, Arch Linux]
---

> open /etc/subuid: no such file or directory

If you are trying to use podman on Arch Linux and get the above notification there is a simple fix.

In [this comment](https://github.com/containers/libpod/issues/2542#issuecomment-512634229) [jcaesar](https://github.com/jcaesar) says to do the following:

Kill any podman related processes

Run (note that this *deletes stuff*):

```
sudo rm -rf ~/.{config,local/share}/containers /run/user/$(id -u)/{libpod,runc,vfs-*}
```
