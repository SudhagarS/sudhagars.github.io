---
layout: post
title:  "An Informal Introduction To Lambda Calculus"
date:   2015-11-24 16:00:00 +0530
categories: functional lambda
tags: lambda functional
excerpt: Hence an exploration of functional programming would be incomplete without studying λ Calculus.
---

It was two or three years ago. I was reading some Python code when I came across a λ-expression for the first time. My immediate reaction was confusion on why that was out of sync with rest of the syntax.

1. Why use ‘lambda’ and not ‘function’?
2. Why are parameters not enclosed in a bracket?

If you haven’t done Python, this is how a λ expression in Python looks like,

{% highlight python %}
filter(lambda x: x % 3 == 0, [1, 2, 3, 4, 5, 6, 7, 8, 9])
{% endhighlight %}

Anyway I didn’t bother to read a lot into all those and just carried on with the code.

I am a big fan of Paul Graham’s essays. After reading this one(you should too) where he talks about how his choice of programming language,lisp, was his secret weapon, I was determined to explore functional programming which lisp epitomises. What is so special about functional programming that some really good programmers are speaking highly of it. Why is everyone betting big on functional programming.

I started exploring the functional world, started learning the functional programming concepts(immutability, statelessness, closures, TCO, etc,.) using mostly Lisp/Clojure. One thing I noticed when reading books or blog posts on FP is that there are always some mentions of λ calculus in them. After a bit of research, it became clear that functional programming has it roots in λ Calculus. Hence an exploration of functional programming would be incomplete without studying λ Calculus.

### Some History

An influential mathematician of the early 20th century, David Hilbert, published his famous list of [23 problems](https://www.wikiwand.com/en/Hilbert%27s_problems) in mathematics. The problems were unsolved at that time and were the kinds of problems whose solutions would lead to the advancement of mathematics. Later he posed another challenge which is famously known as ***Entscheidungsproblem*** (Decision problem). These problems of Hilbert had promoted vigorous investigation into the theory of computability. The two computing models that came out of that time were Alonzo Church’s **Lambda Calculus** and Alan Turing’s **Turing Machine**.

Hilbert’s Decision problem asks if is it possible to devise an algorithm to solve any problem. Alan Turing answered NO to the problem. Of course, saying no is not enough. You have to prove it. Turing created an abstract computation model called [Turing Machine](https://en.wikipedia.org/wiki/Turing_machine) as part of his proof. You can think of Turing Machine as a mini computer. Any thing a computer does, a Turing Machine can do.
Turing showed that it was impossible to compute whether a turing machine would halt for any program-input pair. To explain further, consider the below programs.

{% highlight python %}
1) a = 1 + 3
2) while (True): print 'hello'
{% endhighlight %}

While the first statement terminates, the second one doesn’t. These are trivial programs, hence deducing if they will terminate is easy. But as the programs get complex, it might prove to be a difficult task. Turing showed that it is not only difficult, but impossible.

Around the same time Turing was working on this, his advisor Alonzo Church was working on another model of computation called λ Calculus which is the subject of this post. Actually, Church’s work started long before Turing’s.

## Lambda Calculus

Lambda Calculus is conceptually the simplest programming language in the world.

All you have are functions. Oh, they are all **anonymous**.

A function should take only one **ONE argument**.

A function should **return** a value.


That’s it. Three simple rules. Two keywords `λ` and `.`. You can model any problem. Any problem a computer/turing machine can solve, you can solve them using λ Calculus, not in a straight forward way though as you will see.

Here is the grammar of the language,

{% highlight html %}
<expression>   := <name> | <function> | <application>
<function>     := λ<name>.<expression>
<application>  := (<expression> <expression>)
{% endhighlight %}

Looking at this, you will realise that everything is an expression, λ expression.

Lets see few λ expressions,

{% highlight html %}
1) λx.x
2) λfunc.λarg.(func arg)
3) ((λfunc.λarg.(func arg) λx.x) y)
{% endhighlight %}

First expression is just an identity function. It returns the value it gets.

The second expression sure doesn’t look all that great. But it gives a good idea of the things to come. If you dissect it, it is a function that takes the argument func and returns another function. This returned function in turn takes the argument arg and returns the application of function func to argument arg.

### Function application

The third expression illustrates the usage of second expression. It also gives an opportunity to discuss how function applications work. An function application expression is of form (f arg). To evaluate this expression, we have to first evaluate the expression in the left side.

In the third expression, the left side is `(λfunc.λarg.(func arg) λx.x)`.
This expression itself is a function application. It’s left side doesn’t need more evaluation. Now to apply a function to its argument, just substitute the argument in function’s body. After substitution, we have the below result.

`λarg.(λx.x arg)`

After this step, the third expression becomes,

`((λfunc.λarg.(func arg) λx.x) y) --> (λarg.(λx.x arg) y)`

Now the reduction of the resultant expression will proceed as follows,

`(λarg.(λx.x arg) y)`

`(λx.x y)`

`y`

Yes, the third expression evaluates to y.

#### What’s next…

Now that I have covered the basics of λ Calculus, later I will try to model an interesting problem in λ Calculus. I will also try to explore the ways in which λ Calculus has influenced some of the significant functional programming languages of today.

### TRIVIA

**Y-Combinator**.
 Who haven’t heard of it? Have you ever thought about what it could possibly mean. Surprise, “Y combinator” is a concept from Lambda Calculus. Paul Graham picked it up as the name for this startup incubator.

“Y combinator” is a higher-order function which takes a single argument, which is a function that isn’t recursive. It returns a version of the function which is recursive.