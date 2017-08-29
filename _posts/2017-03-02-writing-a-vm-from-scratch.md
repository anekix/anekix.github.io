---
layout: post
title: creating a simple vm (virtual machine) from ground up!
categories:
- blog
---

I have been following my intense urge to learn thing that are as ..

Things like compilers and interpreters intrests me the quite a bit.
This would be my first step towards building a very simple `stack based vm`

Lets try to understand why we might need a vm.

a simple program in python.

{% highlight python %}
def foo():
  a = 3
  b = 4
  return a + b 
{% endhighlight %}
