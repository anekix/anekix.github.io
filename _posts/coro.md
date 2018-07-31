## Async programming in python

NOTE: Before starting out , i want to clarify that all this was made possible beacuse of awesome work done by many people and the articles that they have written, infact this article dirctly picks up examples from other sources( which i will try to nention wherever possible ). 
This is written for my basic understanding of async programming in python & how everything fits together.There was a lot of info scattered around so i wanted to club them together.

```python

def gen():
  yield 1
  yield 2
  yield 3
  
  
g = gen()

for i in g:
  print (i)
  
#  output
1
2
3
```


generators alllows to pause a function execution in between & then resume it again from the pause point.
in PEP 342 it was proposed to add new API's to the exisiting generator construct so that they could be used
as simple coroutines.

##### What are coroutines & why coroutines?

Coroutines are computer-program components that generalize `methods/functions` for non-preemptive multitasking( The idea behind non-premmptive multitasking is that the OS is not responsible for performing  a context switch instead the controll is yielded voluntarily), by allowing multiple entry points for suspending and resuming execution at certain locations.


#### extending generators to coroutines.

generators can safely be idetified as subset of coroutines, while both can yield multiple times, suspending their execution and allowing re-entry at multiple entry points, they differ in coroutines' ability to control where execution continues immediately after they yield, while generators cannot, instead transferring control back to the generator's caller.[6] That is, since generators are primarily used to simplify the writing of iterators, the yield statement in a generator does not specify a coroutine to jump to, but rather passes a value back to a parent routine.



after the implementation of PEP 342 in python version 2.5 it became possible to use `yield` in an expression.a new api `send()` was added which allowed to send values to the generator which was previously not possible.

consider this code fragment below:
```python

def double_number(number):
  while True:
  number *= 2
  number = yield number

>>> c = double_number(4)
>>> 
>>> c.send(None)
8
>>> c.send(5) #10
10
>>> c.send(1500) #3000
3000
>>> c.send(3) #6

```

a call to `send()` & resumes the coroutine execution ( just like the call to `next()` would resume execution of a geenrator) & we can pass value to the coroutine.
initially with just the generator we could loop over it by calling `next(coro)` but with the prsence of newer api to generators we can now also send values to it using `send()` thus making it a coroutine.


now we have a coroutine but how does it helps in async programming?
we can create a simple scheduler for the illustration purpose how multiple coroutines can work together and thus allowing cooperative multitasking.

```python

import collections

class Trampoline:
    """Manage communications between coroutines"""

    running = False

    def __init__(self):
        self.queue = collections.deque()

    def add(self, coroutine):
        """Request that a coroutine be executed"""
        self.schedule(coroutine)

    def run(self):
        result = None
        self.running = True
        try:
            while self.running and self.queue:
               func = self.queue.popleft()
               result = func()
            return result
        finally:
            self.running = False

    def stop(self):
        self.running = False

    def schedule(self, coroutine, stack=(), val=None, *exc):
        def resume():
            value = val
            try:
                if exc:
                    value = coroutine.throw(value,*exc)
                else:
                    value = coroutine.send(value)
            except:
                if stack:
                    # send the error back to the "caller"
                    self.schedule(
                        stack[0], stack[1], *sys.exc_info()
                    )
                else:
                    # Nothing left in this pseudothread to
                    # handle it, let it propagate to the
                    # run loop
                    raise

            if isinstance(value, types.GeneratorType):
                # Yielded to a specific coroutine, push the
                # current one on the stack, and call the new
                # one with no args
                self.schedule(value, (coroutine,stack))

            elif stack:
                # Yielded a result, pop the stack and send the
                # value to the caller
                self.schedule(stack[0], stack[1], value)

            # else: this pseudothread has ended

        self.queue.append(resume)
```
