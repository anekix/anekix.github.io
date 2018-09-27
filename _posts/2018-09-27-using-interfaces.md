---
layout: post
title: Defining Contracts with Interfaces
categories:
- blog
---

One of the greatest additions to the javascrcipt ecosystem has been the addition of `typescript`. one important part that was introduced in typescript
was the ability to define `interface`. simply put an interface is a way to define a structural contract to the consumer of a particular 
code or more generally interface is a group of related properties and methods that `describe an object`.

One thing to remember is that interfaces are just used by the transpiler to do type checking.nothing more nothing less! once transpiled to javascript there is no meaning of interfaces as javascript is not a typed language.


{% highlight javascript %}

interface Point{
    x: number,
    y: number
 }

{% endhighlight %}
Now to create an object that confirms with  the defination of Point inteface , we can write.

{% highlight javascript %}

let point: Point;

point.x = 20;
point.y = 30;

{% endhighlight %}

Now assume there is a consumer of this Point object which always expects the object passed to it always has a `x` & `y` property defined.

{% highlight javascript %}

function draw(poit: Point){
  
  // some implementation to use `point` object
  console.log(point.x, point.y)

}

{% endhighlight %}

If we now by mistake make a call to `draw` method like this
{% highlight javascript %}
draw( { x:90} ) // gives a compile time error
draw( {x:45, y:67, z:33} ) // give a compile time error that Object literal may only specify known properties, and 'z' does not exist in type 'Point'.
{% endhighlight %}
