---
layout: post
title: Dependency Injection - a thorough analysis.
categories:
- blog
---

Conside the  code below.There is something clearly wrong with it.

{% highlight java %}

public class A {
  private B b;

  public A() {
    this.b = new B(); 
  }

  public void DoSomeStuff() {
    // Do something with B here
  }
}

public static void Main(string[] args) {
  A a = new A();
  a.DoSomeStuff();
}

{% endhighlight %}

The scenario is , There is some class A, which depends upon another class B being its instance variable.




