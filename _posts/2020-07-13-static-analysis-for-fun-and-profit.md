---
layout: post
title: Static analysis for fun and profit
tags: programming
---

# Static analysis for fun and profit

I've had to do a bunch of static analysis over my career, but I've rarely (if ever)
seen chatter about it and its uses online. Static analysis is one of the most
powerful tools you can use to guide your approach towards refactoring (and in
extreme cases, rearchitecture). Static analysis is fantastic both for figuring
out how much a subsystem is used inside an application, as well as how the
application as a whole is structured.

This kind of insight is gained in two ways: either by naive `grep`ping against
a project folder, or by using a language-specific tool to create an AST and search
through that recursively.

The `grep` approach is quick and dirty, and it is not guaranteed to catch every
usage of a system or every import statement. However, the benefit is that it is
quick to set up and makes it easy to do interactively. For example, matching all
import statements in a Python project can be done super easily:

```
$ rg import
```
*note, using [ripgrep](https://github.com/BurntSushi/ripgrep)*

This can be narrowed down specifically to your subsystem of interest, or you can
run that through awk/sed to figure out how many times a system is imported, or even
to quickly count method usages.

You'll find, though, that after a while the regular expressions you're writing
become unwieldy. At that point, it becomes more useful to create a tool using a
proper AST parser. I've made one for Javascript/JSX called [depgraph](https://github.com/pkage/depgraph),
and it's proven to be very handy. It's built on top of [esprima](https://npm.im/esprima),
and can show cyclic dependencies and subsystem usages, as well as rendering a pretty graph.

![depgraph](/img/depgraph.png)

Links that may help:

 - [esprima](https://npm.im/esprima)
 - [python's `ast` module](https://https://docs.python.org/3/library/ast.html)
 - [semgrep](https://github.com/returntocorp/semgrep)


