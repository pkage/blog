---
layout: post
title: Jupyter Lab and Virtual Environments with uv
tags: python
---

# uv and jupyter lab

[`uv`](https://astral.sh/uv) is a new python package manager from Astral which
is blazingly fast at installing packages (thanks to being written in Rust).
Separately, [Jupyter](https://jupyter.org/) is a wonderful environment for
interactive computing. Getting them to work together can be tricky, but here's
the magic script:

Installing Jupyter Lab:

```shell
$ uv tool install jupyter-core --with jupyterlab
```

And then to install environments as with `uv`:

```shell
$ uv add --dev ipykernel
$ uv run python -m ipykernel install --user --name=kernel
```
