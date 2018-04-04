---
layout: post
title: Python Lazy Objects
categories:
- blog
---
Lazy objects are one in which the attributes are evaluated only when needed and againg if the refrence is made for that attribute, only the cached copy of that attribute is used.

{% highlight python %}

class Person():
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.relatives = self.get_all_relatives()

    def get_all_relatives(self):
        # some computation heavy function
{% endhighlight %}

This might incur some extra cost for initialization.
{% highlight python %}

class Person():
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self._relatives = [] 

    @property
    def relatives(self):
        if not self._relatives:
            self._relatives  = ## get relatives...
        return self._relatives
{% endhighlight %}

In second case initializing Person is fast as relatives are not computed until explicitly called.and once initiated its ot computed again and again.