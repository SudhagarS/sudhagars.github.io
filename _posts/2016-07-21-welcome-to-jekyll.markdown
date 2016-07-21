---
layout: post
title:  "Decorators in Python"
date:   2016-07-21 06:21:42 +0530
categories: python
tags: python decorators
---

Decorators are one of my favorite pieces in Python. As the name suggests, a decorator is something that decorates a function by adding extra behaviours. '**@**' is used in Python for decorators.

Let say you want to calculate the time a function takes to execute. One thing you could do is, inside the function body have the below statements.


{% highlight python %}
t=time.clock
func body
print time.clock-t
{% endhighlight %}

This is ok if you want to time only one function. But if you want to calculate execution time for more than 2 functions, you can't just go and alter all the functions. That violates the "DRY" principle. What you could do is write a function that takes as input a function and returns a new function with additional support for timing the execution.

{% highlight python %}
def benchmark(oldfunc):
        import time
        def wrapper(*args, **kwargs):
                t = time.clock()
                res = func(*args, **kwargs)
                print func.__name__, time.clock()-t
                return res
        return wrapper
{% endhighlight %}

Now if u want to add this timing feature for func_a, you would write,

{% highlight python %}
func_a= benchmark(func_a)
{% endhighlight %}

Since this pattern of altering a function is so common, Python has a special syntax for that.

{% highlight python %}
@benchmark
def func_a():
        body
{% endhighlight %}

For more on decorators, 
[http://wiki.python.org/moin/PythonDecorators#What_is_a_Decorator]().
