---
layout: post
title: How to make React fast(er)
tags: programming webdev
---

# How to make React fast(er) when your project is slow.

React is wonderful, but when there are performance issues it can be difficult to
figure out why it's chugging. Part of the problem is that, due to React's
declarative nature, the flame charts produced by the browser's devtools doesn't
really help as they are dominated by React internal methods without any clear
indicator of where the time is being spent:

![unhelpful react flame]({{ "/img/unhelpful_react_flame.png" | relative_url }})

Here are a few quick tips on how to figure out where your pain points are and
work around them (in no particular order):

 - Use the [React Devtools](https://addons.mozilla.org/en-US/firefox/addon/react-devtools/)'s
   built-in profiler to show which components are taking a long time to render.
 - Offload CPU-bound operations into a web worker, and rely on [Transferables](https://developer.mozilla.org/en-US/docs/Web/API/Transferable)
   to preserve performance.
    - Workerization can be easily done with the webpack [worker-loader](https://npm.im/worker-loader)
      and the [worker-promise](https://www.npmjs.com/package/promise-worker) library.
      Paired with the [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
      syntax, this can result in very clean code.
 - Ensure tree updates only happen when you expect them to. Updating a value
   may incur a whole tree rerender if your components are not implemented
   correctly.
 - Make judicious use of the `useMemo` hook.
 - Take advantage of [Concurrent Mode](https://reactjs.org/docs/concurrent-mode-intro.html)
   when available. Note that this is still experimental.
 - Consider using [faster array types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)
   for numerical calculations.
 - Consider translating extremely hot codepaths into WASM with [Rust](https://rust-lang.org)
   (or even  direct [WAT](https://developer.mozilla.org/en-US/docs/WebAssembly/Understanding_the_text_format)
   if you're a masochist).
