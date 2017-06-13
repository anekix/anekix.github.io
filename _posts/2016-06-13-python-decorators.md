So you have had enough of python goodness but again here comes one of my faviourite things that i like in python.
ability to decorate a function or in simple words create a wrapper around an existing function.

This i show we define a basic function in python.

{% highlight python %}

def my_function():
	print 'Attack on The Titans !'

{% endhighlight %}

also you need to understand that in  python functions are first class objects.i.e we can assign them to a variable , pass around other functions or even define a function inside another function and functions can return anotheer functions.

Now consider this dummy example of creating a function wrapper that always outputs uppercase of a string returned by some function.

{% highlight python %}

def some_string_returning_function(some_string):
	return some_string


def uppercase_converter(func):
	def wrapper_function(string_value):
		new_string = func(string_value).upper()
		return new_string
	return wrapper_function

my_new_text = uppercase_converter(some_string_returning_function)
print my_new_text("i like tokyo ghoul")

{% endhighlight %}
