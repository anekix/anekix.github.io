
---
layout: post
title: Learn about Factory Method Design Pattern!
categories:
- blog
---


I would like to start with a somewhat formal description of what a `factory method` design pattern is.

the factory method pattern is a creational pattern that uses factory methods to deal with the problem of `creating objects` without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—`rather than by calling a constructor`.

Read the above paragraph twice or thrice slowly until it starts to make some sense.


Example
{% highlight python %}
from abc import ABCMeta, abstractmethod
from enum import Enum


class Person(metaclass=ABCMeta):

    @abstractmethod
    def get_name(self):
        raise NotImplementedError("You should implement this!")


class Villager(Person):
    def get_name(self):
        return "Village Person"


class CityPerson(Person):
    def get_name(self):
        return "City Person"


class PersonType(Enum):
    RURAL = 1
    URBAN = 2


class Factory:
    def get_person(self, person_type):
        if person_type == PersonType.RURAL:
            return Villager()
        elif person_type == PersonType.URBAN:
            return CityPerson()
        else:
            raise NotImplementedError("Unknown person type.")


factory = Factory()
person = factory.get_person(PersonType.URBAN)
print(person.get_name())
{% endhighlight %}
