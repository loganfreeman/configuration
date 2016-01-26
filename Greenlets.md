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
The real power of gevent comes when we use it for network and IO bound functions which can be cooperatively scheduled. Gevent has taken care of all the details to ensure that your network libraries will implicitly yield their greenlet contexts whenever possible. In this case the select() function is normally a blocking call that polls on various file descriptors.
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
Another somewhat synthetic example defines a task function which is non-deterministic (i.e. its output is not guaranteed to give the same result for the same inputs). 
```python
import gevent
import random

def task(pid):
    """
    Some non-deterministic task
    """
    gevent.sleep(random.randint(0,2)*0.001)
    print('Task %s done' % pid)

def synchronous():
    for i in range(1,10):
        task(i)

def asynchronous():
    threads = [gevent.spawn(task, i) for i in xrange(10)]
    gevent.joinall(threads)

print('Synchronous:')
synchronous()

print('Asynchronous:')
asynchronous()
```
In a more common use case, asynchronously fetching data from a server, the runtime of fetch() will differ between requests, depending on the load on the remote server at the time of the request.
```python
import gevent.monkey
gevent.monkey.patch_socket()

import gevent
import urllib2
import simplejson as json

def fetch(pid):
    response = urllib2.urlopen('http://json-time.appspot.com/time.json')
    result = response.read()
    json_result = json.loads(result)
    datetime = json_result['datetime']

    print('Process %s: %s' % (pid, datetime))
    return json_result['datetime']

def synchronous():
    for i in range(1,10):
        fetch(i)

def asynchronous():
    threads = []
    for i in range(1,10):
        threads.append(gevent.spawn(fetch, i))
    gevent.joinall(threads)

print('Synchronous:')
synchronous()

print('Asynchronous:')
asynchronous()
```
Spawning Greenlets
---
```python
import gevent
from gevent import Greenlet

def foo(message, n):
    """
    Each thread will be passed the message, and n arguments
    in its initialization.
    """
    gevent.sleep(n)
    print(message)

# Initialize a new Greenlet instance running the named function
# foo
thread1 = Greenlet.spawn(foo, "Hello", 1)

# Wrapper for creating and running a new Greenlet from the named
# function foo, with the passed arguments
thread2 = gevent.spawn(foo, "I live!", 2)

# Lambda expressions
thread3 = gevent.spawn(lambda x: (x+1), 2)

threads = [thread1, thread2, thread3]

# Block until all threads complete.
gevent.joinall(threads)
```

```python
import gevent
from gevent import Greenlet

class MyGreenlet(Greenlet):

    def __init__(self, message, n):
        Greenlet.__init__(self)
        self.message = message
        self.n = n

    def _run(self):
        print(self.message)
        gevent.sleep(self.n)

g = MyGreenlet("Hi there!", 3)
g.start()
g.join()
```

Greenlet State
---
- started -- Boolean, indicates whether the Greenlet has been started
- ready() -- Boolean, indicates whether the Greenlet has halted
- successful() -- Boolean, indicates whether the Greenlet has halted and not thrown an exception
- value -- arbitrary, the value returned by the Greenlet
- exception -- exception, uncaught exception instance thrown inside the greenlet
```python
import gevent

def win():
    return 'You win!'

def fail():
    raise Exception('You fail at failing.')

winner = gevent.spawn(win)
loser = gevent.spawn(fail)

print(winner.started) # True
print(loser.started)  # True

# Exceptions raised in the Greenlet, stay inside the Greenlet.
try:
    gevent.joinall([winner, loser])
except Exception as e:
    print('This will never be reached')

print(winner.value) # 'You win!'
print(loser.value)  # None

print(winner.ready()) # True
print(loser.ready())  # True

print(winner.successful()) # True
print(loser.successful())  # False

# The exception raised in fail, will not propagate outside the
# greenlet. A stack trace will be printed to stdout but it
# will not unwind the stack of the parent.

print(loser.exception)
```
Events
---
Events are a form of asynchronous communication between Greenlets.
```python
import gevent
from gevent.event import Event

'''
Illustrates the use of events
'''


evt = Event()

def setter():
    '''After 3 seconds, wake all threads waiting on the value of evt'''
    print('A: Hey wait for me, I have to do something')
    gevent.sleep(3)
    print("Ok, I'm done")
    evt.set()


def waiter():
    '''After 3 seconds the get call will unblock'''
    print("I'll wait for you")
    evt.wait()  # blocking
    print("It's about time")

def main():
    gevent.joinall([
        gevent.spawn(setter),
        gevent.spawn(waiter),
        gevent.spawn(waiter),
        gevent.spawn(waiter),
        gevent.spawn(waiter),
        gevent.spawn(waiter)
    ])

if __name__ == '__main__': main()
```
An extension of the Event object is the **AsyncResult** which allows you to send a value along with the wakeup call. This is sometimes called a future or a deferred, since it holds a reference to a future value that can be set on an arbitrary time schedule.
```python
import gevent
from gevent.event import AsyncResult
a = AsyncResult()

def setter():
    """
    After 3 seconds set the result of a.
    """
    gevent.sleep(3)
    a.set('Hello!')

def waiter():
    """
    After 3 seconds the get call will unblock after the setter
    puts a value into the AsyncResult.
    """
    print(a.get())

gevent.joinall([
    gevent.spawn(setter),
    gevent.spawn(waiter),
])
```
**Queues** are ordered sets of data that have the usual put / get operations but are written in a way such that they can be safely manipulated across Greenlets.
Each of the put and get operations has a non-blocking counterpart, `put_nowait` and `get_nowait` which will not block, but instead raise either `gevent.queue.Empty` or `gevent.queue.Full` if the operation is not possible.
```python
import gevent
from gevent.queue import Queue

tasks = Queue()

def worker(n):
    while not tasks.empty():
        task = tasks.get()
        print('Worker %s got task %s' % (n, task))
        gevent.sleep(0)

    print('Quitting time!')

def boss():
    for i in xrange(1,25):
        tasks.put_nowait(i)

gevent.spawn(boss).join()

gevent.joinall([
    gevent.spawn(worker, 'steve'),
    gevent.spawn(worker, 'john'),
    gevent.spawn(worker, 'nancy'),
])
```
