---
layout: post
title: Python classes in detail
categories:
- blog
---





simple class defination - its just a logical grouping of objects and methods

suppose we want a define a person object and with some attributes and methods on the person objects.
we do it like this.
{% highlight python %}

class User:
    
    def __init__(self, age, gender, location, contact):
        self.age = age
        self.gender = gender
        self.location = location
        self.contact = contact

    def setContact(new_contact):
        self.contact = new_contact

    def setLocation(new_location):
        self.location = new_location

{% endhighlight %}


notice ``` def __init__(self, age, gender, location, contact):` This is called the constructor of the class.
as name suggests it is used to  `construct` a `class instance` from the `class defination` of `User`.

A class can be considered as a blueprint or instruction manula of how the class should be constructed.
until now memory is not allocated for the class above its just a blueprint/instruction manual.


What if we want to use above class? for that we need tp create a `class instance` or an `object` of class `User`