---
layout: post
title: inner workings of python imports and module system! 
categories:
- blog
---

Lets start with an inbuilf module ex. random or math

`sys.modules` is a dictionary holding the refrence to the actual module code.
we can verify that by using the code below:

{% highlight python %}

import sys
print sys.modules

{% endhighlight %}

How does python knows where to look for modules when performing an import?

it maintains a search path in `sys.path`. to check you current search path. try the code below:

{% highlight python %}

import sys

for path in sys.path:
    print path

{% endhighlight %}

it would output something like:
{% highlight python %}

/usr/local/bin
/usr/lib/python2.7
/usr/lib/python2.7/plat-x86_64-linux-gnu
/usr/lib/python2.7/lib-tk
/usr/lib/python2.7/lib-old
/usr/lib/python2.7/lib-dynload
/usr/local/lib/python2.7/dist-packages
/usr/local/lib/python2.7/dist-packages/Tenjin-1.1.1-py2.7.egg
/usr/lib/python2.7/dist-packages
/usr/lib/python2.7/dist-packages/PILcompat
/usr/lib/python2.7/dist-packages/gtk-2.0
/usr/lib/python2.7/dist-packages/ubuntu-sso-client
/usr/local/lib/python2.7/dist-packages/IPython/extensions
/home/kshitij/.ipython
{% endhighlight %}

