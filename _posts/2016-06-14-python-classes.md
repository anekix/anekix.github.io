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


notice `def __init__(self, age, gender, location, contact):` This is called the constructor of the class.
as name suggests it is used to  `construct` a `class instance` from the `class defination` of `User`.

A class can be considered as a blueprint or instruction manula of how the class should be constructed.
until now memory is not allocated for the class above its just a blueprint/instruction manual.


What if we want to use above class? for that we need to create a `class instance` or an `object` of class `User`

This is how we do it:
{% highlight python %}
John = User(20,'male','tokyo','98924676488')

{% endhighlight %}
The above is like asking python " if you happen to know how to create a `User` ,then create it with the values that i provide and remember to follow the instruction manual for creating a <b>User</b> `John` "

and then to use its attributes

{% highlight python %}
john.setContact(889983388933)
john.setLocation('UK')
{% endhighlight %}

here `John` is just one instance of User class yo ucan create any number of instances of a class you want.

Now whats going on with that `self` in function definations inside class?

`self`
 refers to instance itself that we created. calling `John..setLocation('UK')` puts those instruction to John instance. simply put `self` is the instance of the John that `setLocation` is being called upon.you could have written `User.setLocation(John,'UK')`

 # Instance Level v/s Class Lvel

 until now all we saw was creating **attributes** and **methods** at instamce level i.e each class has its own copy of its attributes.What is we wanted some attribute to be present at class level?

 its actually quite simple. This is how its done
{% highlight python %}

 class User:
    defaultAge = 25

    def __ini__(age,location,gender):
        self.location = location
        self.gneder = gender
        if gender:
            self.age = age
        else:
            self.age = defaultAge
{% endhighlight %}


now *every object* of `User` will have a common value `defaultAge`





