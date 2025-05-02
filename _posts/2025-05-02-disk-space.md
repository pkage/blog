---
layout: post
title: Reserved free space for system rescues
tags: deployment, sysadmin
---

# Reserving free space for rescuing a system that's out of storage

Every once in a while I find myself setting up a new server, and one thing that
I always add is a reserved free space file. This makes sure that if a disk gets
completely full, I've got some headroom to undo my mistakes. There are a few
different ways to go about this, but I like `dd` as it's almost always
installed and usually supports human-readable byte size annotations.

Here's the incantation:

```
$ sudo dd if=/dev/urandom of=/FREESPACE bs=1M count=8192
```

This writes an 8 GiB file to the root directory called `/FREESPACE`, reading
out from `/dev/urandom` in 8192 1MiB blocks. Superuser access is required to
put that in your root directory, but you can put it anywhere. Reading from
`/dev/urandom` is recommended, as if you fill the file with e.g. just zeros, a
filesystem like ZFS will compress that down and you'll end up with much less
real space freed when you delete the file.

*Thanks to [Lev Novikov](https://metaist.com) for the inspiration to write this post.*
