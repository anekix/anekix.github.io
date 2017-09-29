---
layout: post
title: Using @property in python for getters/setters/deleters
categories:
- blog
---
In a language such as Java or c++ in order to provide access to private methods, 
we use getters and setters to get and set the value of an attribute respectively.

But in python we have direct acces to the objects attribute so we can go without using them.

{% endhighlight %}

Class A:
    def __inti__(self, value):
        self.value = value


>>> a = A(10)
>>> a.value
10
{% highlight python %}


Now main reason to use getters/setters is to provide an interface such that the client code should be independent of how the values are being generated (picked form a file or some API)

For such cases to handle mmutations to the attribute wthut affeectig the clint code we can use the `@property` decorator in python


{% endhighlight %}

Class A:
    def __inti__(self, value):
        self.value = value


>>> a = A(10)
>>> a.value
10
{% highlight python %}

>>>a = A()
>>>b = a
>>>c = a

{% endhighlight %}







