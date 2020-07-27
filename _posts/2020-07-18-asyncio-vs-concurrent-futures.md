---
layout: post
title: Bridging Python Asyncio and Concurrent Futures
tags: programming python
---

# Bridging Python Asyncio and Concurrent Futures

Python, in an apparent break from the "There should be one—and preferably only
one—obvious way to do it" principle, has introduced two different kinds of
`Future`s: `concurrent.futures.Future` and `asyncio.Future`. Even better, these
two are incompatible and represent fundamentally different models of concurrent
programming.

With the release of Python 3.5, Python added the `async` and `await` keywords to
the language, as well as adding the `asyncio` library into the standard library.
These were intended to make writing single-threaded event-loop-driven applications
easier to write—and they do! However, this created a confusing naming/purpose
conflict with the existing method of concurrent execution, `concurrent`. Both
expose `Future` objects to aid in writing parallel code.

Briefly, a `Future` is a promise to resolve to a value at some point in the
future. This is useful for parallel and concurrent programming, where a `Future`
holds the future result of a computation dispatched to the pool. The difference
between these two is that in a `concurrent.futures.Future` the computation occurs
on a separate process and happens *concurrently* with the rest of the jobs in
the pool, whereas with an `asyncio.Future` the computation is performed on a
single thread with all the other computations in the pool in the asyncio event
loop. Broadly, the `concurrent.futures.Future` construct is good for CPU-bound
operations, and the `asyncio.Future` construct is good for I/O-bound operations.

Unfortunately, due to the differences in the underlying execution models these
two `Future`s are incompatible with each other. For example, you cannot `await`
a `concurrent.future.Future`:

```python
async def async_function(future: concurrent.futures.Future):
    # can't do this! concurrent.futures.Future is not awaitable
    result = await future
```

You can kind of get around this by waiting on the result directly:

```python
async def async_function(future: concurrent.futures.Future):
    # at least it doesn't throw an error now?
    result = future.result()
```

But this comes with a major drawback: while the future is pending, `.result()` *blocks
until a result is available*. This means that if you take this approach,
your *entire async event loop will come to a grinding halt* for the duration of
the call. Clearly this is not ideal.

Thankfully, there is another approach. `asyncio` has the ability to run a task
in an executor and wrap it in a event-loop-compatible container. This handles
the underlying `concurrent.future.Future` for you. Here's an example (adapted
from the standard library documentation):

```python
async def main():
    loop = asyncio.get_running_loop()

    with concurrent.futures.ProcessPoolExecutor() as pool:
        result = await loop.run_in_executor(
            pool, cpu_bound)

    print(result)
```
_see more examples [from the docs](https://docs.python.org/3/library/asyncio-eventloop.html#executing-code-in-thread-or-process-pools)._

Even more easily, you can just use `asyncio.wrap_future()`! From our example
earlier:

```python
async def async_function(future: concurrent.futures.Future):
    result = await asyncio.wrap_future(future)
```

This is useful if you need to port existing code to use an async event loop. As
an anecdote, I used this approach to rewrite the web server portion of a
data-intensive application without having to rewrite the (very complex) scheduler
code.

Further reading:
  - [`concurrent` docs](https://docs.python.org/3/library/concurrent.futures.html#future-objects)
  - [async event loop](https://docs.python.org/3/library/asyncio-eventloop.html)
  - [async tasks](https://docs.python.org/3.5/library/asyncio-task.html)
