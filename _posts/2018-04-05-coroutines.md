---
layout: post
title: Coroutines in python
categories:
- blog
---


If you have ever tried wrting a scalable application , you would have come across term ***Asynchronous Programming***.

One of the  ways in which we can achieve asynchronous programming is by use of coroutines in  python.

The idea of coroutines is simply that two or more routines( methods) work cooperatively to complete some task. 
ONe way to use coroutines is by using ***yield from*** expression in generators.

As of python 3.5 and upwards, coroutines were made an proper standalone concept seperate from generators( even though both share same code)

The takeaway is that since python 3.5 , coroutines a native Python language feature, and clearly different from generators.This helps in removing generator/coroutine ambiguity and have a clear mental model of coroutines while writing asynchronous code in python.



Native coroutines have new syntax associated with them.

{% highlight python %}

async def read_data(db):
    data = await db.fetch('SELECT ...')
{% endhighlight %}

Two important points to note are:
Inside an async function its not allowed to use ***yield*** or ***yield from***.
async function are coroutines even if thery do not contain an ***await*** statement.





