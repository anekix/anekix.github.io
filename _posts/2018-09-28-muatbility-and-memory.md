
---
layout: post
title: mutability & memory management in python!
categories:
- blog
---


Everything in python is an `object`. even the simplest primitives like numbers.to quickly verify this.

{% highlight python %}
x = 20
isinstance(x,object)
>>> x = 20
>>> isinstance(x,object)
>>> True
{% endhighlight %}
