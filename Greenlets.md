Greenlets
---
The primary pattern used in gevent is the Greenlet, a lightweight coroutine provided to Python as a C extension module. Greenlets all run inside of the OS process for the main program but are scheduled cooperatively.
> Only one greenlet is ever running at any given time.

A switch between the two subtasks is known as a *context switch*.
