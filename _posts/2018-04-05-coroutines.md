---
layout: post
title: Native coroutines , asyncio and async programming in python.
categories:
- blog
---


If you have ever tried writing a scalable application , you would have come across term ***Asynchronous Programming***.

One of the  ways in which we can achieve asynchronous programming is by use of coroutines in  python.

The idea of coroutines is simply that two or more routines( methods) work cooperatively to complete some task. 

ONe way to use coroutines is by using ***yield from*** expression in generators.

As of python 3.5 and upwards, coroutines were made an proper standalone concept seperate from generators(even though both share same code)

The takeaway is that since python 3.5 ,a coroutine is a native Python language feature, and clearly different from generators.This helps in removing generator/coroutine ambiguity and have a clear mental model of coroutines while writing asynchronous code in python.

Native coroutines have new syntax associated with them.

{% highlight python %}

async def read_data(db):
    data = await db.fetch('SELECT ...')

{% endhighlight %}

Two important points to note are:
Inside an async function its not allowed to use ***yield*** or ***yield from***.
async function are coroutines even if thery do not contain an ***await*** statement.

in the above example ***await*** keyword suspend execution of ***read_data()*** until result from 
***db.fetch()*** is available and the control is passed so that another coroutine can be executed in the meantime.

Did you just noticed what happened just above? In case of threads we dont have manual control over when threads will be switched but its made explicit when we decide to use coroutines for concurrent task execution(by using ***await*** keyword).

so its safe to claim that coroutines provie a mechanism of explicit concurrency.

Now consider this situation.A coroutine is being executed and at some point it decides to wait for some result asynchronously , and we have some more coroutines defined , how do we then decide which coroutine is executed next?.

The answer for above is ***event loop*** .any framework that exposes ***async/await*** api for asynchronous programming also has some sort of event loop which decides which coroutine to execute next.

***asyn/await*** is basically an API exposed by python to perfrom asychronous programming.***asyncio*** is one framework that happens to use ***async/await***.



