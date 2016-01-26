Greenlets
---
The primary pattern used in gevent is the Greenlet, a lightweight coroutine provided to Python as a C extension module. Greenlets all run inside of the OS process for the main program but are scheduled cooperatively.
> Only one greenlet is ever running at any given time.

A switch between the two subtasks is known as a *context switch*.

A context switch in gevent is done through yielding. 
```python
import gevent

def foo():
    print('Running in foo')
    gevent.sleep(0)
    print('Explicit context switch to foo again')

def bar():
    print('Explicit context to bar')
    gevent.sleep(0)
    print('Implicit context switch back to bar')

gevent.joinall([
    gevent.spawn(foo),
    gevent.spawn(bar),
])
```
The real power of gevent comes when we use it for network and IO bound functions which can be cooperatively scheduled. Gevent has taken care of all the details to ensure that your network libraries will implicitly yield their greenlet contexts whenever possible.
```python
import time
import gevent
from gevent import select

start = time.time()
tic = lambda: 'at %1.1f seconds' % (time.time() - start)

def gr1():
    # Busy waits for a second, but we don't want to stick around...
    print('Started Polling: %s' % tic())
    select.select([], [], [], 2)
    print('Ended Polling: %s' % tic())

def gr2():
    # Busy waits for a second, but we don't want to stick around...
    print('Started Polling: %s' % tic())
    select.select([], [], [], 2)
    print('Ended Polling: %s' % tic())

def gr3():
    print("Hey lets do some stuff while the greenlets poll, %s" % tic())
    gevent.sleep(1)

gevent.joinall([
    gevent.spawn(gr1),
    gevent.spawn(gr2),
    gevent.spawn(gr3),
])
```
