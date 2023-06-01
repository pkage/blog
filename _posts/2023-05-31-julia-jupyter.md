---
layout: post
title: Installing Julia as a Jupyter kernel
tags: cad, python
---

As I've found myself doing this a lot recently, here's quick capture on how to
get the wonderful [IJulia](https://github.com/JuliaLang/IJulia.jl) configured
properly:

```julia
import Pkg
Pkg.add("IJulia")

import IJulia
IJulia.installkernel("Julia", "--project=@.", env=Dict("JULIA_DEBUG" => "Main"))
```

Which will install a kernelspec to your default Jupyter kernel installation
directory. For me (under Ubuntu 22.04), that's
`~/.local/share/jupyter/kernels`.
