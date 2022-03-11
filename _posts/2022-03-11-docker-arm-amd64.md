---
layout: post
title: Building amd64 Docker Images on ARM in 2022
tags: programming devops
---

# Building amd64 Docker Images on ARM in 2022

Building a Docker image on ARM can be a bit hairy, as if you're not careful
you can end up building a fully-ARM version of your package which runs locally
but potentially not on Intel-based servers. It used to be a pain in the butt to
cross-build Docker images, requiring [experimental features to be
enabled](https://blog.jaimyn.dev/how-to-build-multi-architecture-docker-images-on-an-m1-mac/)
and some other fiddling around. However, in 2022 it's much, *much* easier and
only requires a tiny change:

```sh
# before
docker build . -t my_cool_docker_tag

# after
docker build . -t my_cool_docker_tag --platform linux/amd64
```

That's it!
