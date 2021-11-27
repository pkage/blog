---
layout: post
title: Virtual Environments in Jupyter
tags: python
---

# Installing virtual environment kernels in Jupyter

Jupyter, the evolution of the IPython Notebook system, uses the system Python
interpreters by default. Unfortunately, this makes it difficult to manage
dependencies—some projects require different versions of the same package, some
packages have peer dependencies, and some packages just litter your `$PATH`
with scripts. To avoid all that the Python community uses [virtual
environments](https://docs.python.org/3/tutorial/venv.html), which creates a
new python distribution for each new project.

Jupyter can't find these environments by default, but thankfully they're pretty
easy to install:

```bash
# create a virtual environment and activate it
$ python3 -m venv env_project
$ source env_project/bin/activate

# from inside the environment install ipykernel and register it
(env_project) $ pip install ipykernel
(env_project) $ python -m ipykernel install --user --name=project
```

And the kernel should now be available in Jupyter!

---

As an aside, [Jupyter
Lab](https://jupyterlab.readthedocs.io/en/stable/getting_started/overview.html)
comes strongly recommended as an alternative interface to Jupyter—run it by
simply invoking:

```
$ jupyter lab
```
