---
layout: post
title: Dependency Injection - a thorough analysis.
categories:
- blog
---

Conside the  code below which represents the following conditions.
We have a ***class A*** which needs an instance of another ***class B*** to carry out some computation.
There is something clearly wrong with it.

{% highlight python %}

class B:
    def doSomething(self):
        return "hello"

class A:

    def __init__(self, foo, bar):
        self.foo = foo
        self.bar = bar
        self.b = B()

    def makeCalculations(self):
        return self.foo + self.bar + self.b.doSomething()


if name ==__main__:

    a = A(foo="some text", bar="some other text")
    a.makeCalculations()

{% endhighlight %}

The two major problems with the above code are:
1) Both classses are tightly coupled together, Suppose if later on there is a change to the constructor of class B, class A has to explicitly changed to reflect the new requirements.

2)We cannot test class A in isolation.

To imporve the above code , we can rewrte it as:

{% highlight python %}

class B:
    def doSomething(self):
        return "hello"

class A:

    def __init__(self, foo, bar, B):
        self.foo = foo
        self.bar = bar
        self.b = B

    def makeCalculations(self):
        return self.foo + self.bar + self.b.doSomething()


if name ==__main__:

    
    b = B()
    a = A(foo="some text", bar="some other text", B=b)
    a.makeCalculations()

{% endhighlight %}

Now if there is any change is there in class B it can simple be paseed to class A without changing the class A itself,

The advantages are:

We can now test both classes independent of each other.(to test class A we can simply pass a  
mock of class B) & We also gained he ability to control functionality from a singl eplace insted of spreading it throughout the program.

Consider the case when ***class B*** needs some parameter in its constructor, class A ***has*** to know
about all parameters that ***class B*** accepts, which is something completely unrelated to ***class A***.it never 
***has***/***need*** to know about what class A needs.


Passing an already constructed instance of ***class B***  Bexplicitly to ***A's*** constructor solves the above problema and make A depend only on what A needs.    

Looking at the above example we are free to conclude that dependency injection is a way to create your dependencies outside of the class that uses it. Inject them from the outside, and take control about their creation away from the inside of your class. 








