---
layout: post
title: Installing CADQuery and CQ-editor on M1 with Rosetta
tags: cad, python
---

# Installing CADQuery and CQ-Editor on M1 with Rosetta

I've recently found myself needing to CAD out some pieces for 3D printing, as
we've got two cats who now need different diets. We've set up some [microchip
feeders](https://www.surepetcare.com/en-us/pet-feeder/microchip-pet-feeder) as
recommended by our vet to try to keep them from eating each others food, but
unfortunately they're not so great. The feeders, while expensive, don't seem to
be designed to work with cats that are Highly Motivated^tm, and so we needed to
modify them extensively to ensure that they don't pry open the feeding cover or
squeeze in by each other. We're printing a part to restrict the size of the
feeding portal in order to ensure that the little cat doesn't sneak in next to
the big one during feeding.

I decided to use [CADQuery](https://github.com/CadQuery/cadquery), which
doesn't vendor a build for the M1 architecture. Building it from scratch is
apparently onerous, so a Rosetta setup is the way to go. Here's how to install
it:

## Installing Conda

If you don't have Conda installed already, install [Mambaforge](https://github.com/conda-forge/miniforge). This is pretty much standard Conda, but comes with Mamba (a faster package solver).

```sh
$ curl -L -O "https://github.com/conda-forge/miniforge/releases/latest/download/Mambaforge-$(uname)-$(uname -m).sh"
$ bash Mambaforge-$(uname)-$(uname -m).sh
```

Optionally, prevent Conda from activating unless you need it:

```sh
$ conda config --set auto_activate_base false
```

## Setting up a Rosetta environment

Next, we need to make a Conda environment running under x86_64 emulation:

```sh
$ CONDA_SUBDIR=osx-64 conda create -n rosetta python
$ conda activate rosetta
$ conda config --env --set subdir osx-64
```

You can verify this is running under Rosetta with the following:

```sh
$ python -c "import platform; print(platform.machine())"
```

...which should print out `x86_64`.

## Installing CADQuery

You can install CADQuery and CQ-editor all in one go:

```sh
$ conda install -c cadquery -c conda-forge cq-editor=master
```

Additionally, I found that I had to downgrade `pyqtgraph` to version `0.11.1`
to get around some deep errors:

```sh
$ conda install pyqtgraph=0.11.1
```

## Running CQ-editor

```sh
$ conda run cq-editor
```

And you're done!
