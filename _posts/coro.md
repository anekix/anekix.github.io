## ASYNC programming in python

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



generators can safely be idetified as subset of coroutines, while both can yield multiple times, suspending their execution and allowing re-entry at multiple entry points, they differ in coroutines' ability to control where execution continues immediately after they yield, while generators cannot, instead transferring control back to the generator's caller.[6] That is, since generators are primarily used to simplify the writing of iterators, the yield statement in a generator does not specify a coroutine to jump to, but rather passes a value back to a parent routine.


