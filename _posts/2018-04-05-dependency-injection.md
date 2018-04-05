---
layout: post
title: Dependency Injection - a thorough analysis.
categories:
- blog
---

Conside the  code below which represents the following conditions.
We have a class A which needs an instance of another class B to carry out some computation.
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

    a = A()
    a.makeCalculations()

{% endhighlight %}






