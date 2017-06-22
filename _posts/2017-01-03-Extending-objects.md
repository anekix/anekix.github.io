---
layout: post
title: Extending objects? inheritance or composition?
categories:
- blog
---


Sart this post with a direcrt question. 
What do you do When you need to extend the functionalities of an existing object?

There are two different ways 

1) Using Inheritance


baisc syntax is:


{% highlight python %}
class BaseClassName:
	#some attributes (methods are also attributes)

class DerivedClassName( BaseClassName ):
	#extra attributes of derived class

{% endhighlight %}

Why to use inheritance?
Because it allows code resuability and also provides a common interfacefor similar obejects which display is-a relationship. Like Car is-a Veichle and Truck is-a Veichle. so we can define Veichle as the base class and other classes like Truck and Car can inherit attributes from it.

Why would you want that? let me show with a realistic example.


create a Polygon class. a Polygon is just a closed figure which has 3 or more sides. simplest of them is a rectangle.

{% highlight python %}
class Polygon:
	def __init__(self, sides):
		self.sides = sides
	def setSidesLength(self):
		self.sidesLength =[float(input('enter side {}'.format(i) for i in self.sides))]

	def printSides(self):
		for index,value in enumerate(self.sidesLength):
			print "side {} is".format(index),value
{% endhighlight %}

now any closed geometric figure with some defined sides is-a Polygon . for ex. Triangle, here we can clearly see an IS-A relationship

so we sublclass Polygon in Triangle class

{% highlight python %}
class  Triangle(Polygon):
	def __init__self():
		Polygon.__init__(self,3)
	def findArea():
		a, b, c = self.sides
		# calculate the semi-perimeter
		s = (a + b + c) / 2
		area = (s*(s-a)*(s-b)*(s-c)) ** 0.5
		print('The area of the triangle is %0.2f' %area)

{% endhighlight %}
In Triangle class we inherited some attributes common to all Polygons like setting side lenghts and printing all sides,so When we
want to set sides or print sides of any Triangle Object we did not have to define those methods again in Triangle class.

