# `gevent` Notes

Python networking library.

Backends:

+ `greenlet`: Python concurrency package
+ `libev`/`libuv`: event loop by C -- `gevent` use them to let OS deliver an event when the job is done.

Features:

+ Coroutine

## Methods

`gevent.spawn()` (basically starts a `greenlet` instance and then start it) a set of operations, then `gevent.joinall()`. 

Inside of the spawning, replace standard `socket` with `gevent.socket`, so it doesn't not block the whole interpreter. For project which already built on standard `socket`, one may use monkey patch `gevent.monkey` for automatic replacement.

## References

1. [Official starter document](http://www.gevent.org/intro.html)