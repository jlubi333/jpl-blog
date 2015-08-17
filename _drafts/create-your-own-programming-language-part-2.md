---
layout: post
title: "Create Your Own Programming Language: Part II"
---

{% include math %}

# Create Your Own Programming Language: Part II

Welcome to **Part II** of my tutorial where I show you how to create your own
programming language! If you haven't already, please read [Part I][part1] to get
started. As before, I created a language called Camille, but you can name it
whatever you want!

## Project Layout

We will be organizing our back-end code into the following structure:

- Type Information
- Parser
- Evaluator
- Environment
- Type Checker

This is roughly the order that we will be implementing the files, but we will go
back and forth between these files quite often.

Our front-end will feature both a [REPL][repl] and a file interpreter.

## Getting Started

The first file that we will create will be our type information file, which I
named `Type.hs`.

Every language---statically-typed or not---has types. In a language like Python,
the types are not checked until runtime, but it still has types (that is why you
get an error in Python when you try to do something like `1 + "a"`).

Our language will have 5 primitive types. There are, of course the usual
suspects:

- `Integer`: an arbitrary precision integer
- `String`: a list of characters (including the empty string or a single
    character)
- `Boolean`: a true or false value

Floating-point numbers could easily be added as an extension, but I chose not to
include them in the first iteration of the language. You can, of course, choose
to do so.

This list leaves 2 vacancies. These empty spots are filled with:

- `Void`
- `Callable`

This is where the *real* fun begins!

## The `Void` Type

The `Void` type is *similar* to the concept of a `null` value, however it is
fundamentally *very* different. In a language like Java, every single type
(barring the primitives) can take on the value `null`, from your basic `String`
to your `AbstractFactoryBuilderBeanSingleton`. This is widely regarded as a [Bad
Thing&trade;][null] because it introduces heaps of potential errors.

`Void`, on the other hand, is completely safe! You will never encounter a
`VoidPointerException` or any silly nonsense like that (not that Camille even
supports pointers). `Void` is simply a type with a single value: `Nothing`.
Let's compare this with some other types.

- `Integer`: can take on infinitely many values
- `String`: can take on infinitely many values
- `Boolean`: can take on two values: `True` or `False`
- `Void`: can take on one value: `Nothing`

Note that `null` is not even on this list because it is not a type at all, but a
value! For the rest of these tutorials, we can just forget about `null`
altogether as we know it, because neither Camille nor Haskell has it (for our
purposes).

So what use does `Void` have? Well, there are two main ways that `Void` will be
used in Camille. The simplest purpose of `Void` is to allow for functions and
things like type declarations to have a return type, because we will craft
Camille so that *everything* is an expression.

For example, `a + 1` is an expression that returns exactly what you think it
would, but what does an expression like `a :: Integer` (type declaration that
`a` is an `Integer`) return? It can't return the value of `a` because `a` won't
have a value yet if we are just now declaring a type to it! In cases like these,
or in functions like `print`, the only sensible return value is `Nothing`. Note
that this is different than *not* having a return value, because you can very
easily assign something to nothing in Camille <span>(as long as you can handle
the philosophical implications of such a statement!)</span>{: .mutter}.

The second purpose of `Void` is a bit more complicated, and I will explain it
after the next section.

## The `Callable` Type (Backstory)

In order to best explain the `Callable` type, I'd like to share a little story
that led me to the creation of it.

The final project in my computer science class was to develop a substantial
programming project about anything that was of interest. I chose to create
Camille, because I had always wanted to create a language. One of the
requirements for the project was that we had to check in at the 25%, 50%, and
75% mark and give a presentation about what we had so far.

At the 50% mark presentation, I had enough of the language done that I could
show off its REPL and a few of its feature. At the time, however, I expressed
that I was struggling with exactly how I was going to implement a type system in
an interpreted language, and particularly what the type of a function should be.
I had decided that a function would be solely typed by its return value, but I
was very unsatisfied with that because that would mean that the type of its
arguments would remain unchecked.

I was explaining to the class that everything was going to have be an expression
with a return type, one of my friends said something along the lines of:

> Wait, so numbers are actually functions?

We looked at each other for a few seconds as a few other members of the class
laughed. He had thought that I meant everything would literally be a function.

"Wait, so everything would be a function...?" I wondered aloud.

"Yeah, so you would be able to call numbers like functions," he said.

"OMG wait you mean like with parenthesis? You could call it like `1()` or `2()`
and the numbers would actually be functions!"

"Yeah, yeah!"

"Then you could call the numbers that you could return!!! You could be like
`1()()` or `1()()()` or an infinite number of parenthesis!!!"

It was an idea so preposterous that I couldn't help but fall in love. Little did
I know that my friend had actually come up with a legitimate way of describing
numbers. Many months later, I found out that, in category theory, numbers can be
described as functions taking in the "Unit" value (i.e. nothing) and returning a
number.

By the way, this would be a good time to mention that `Void` is actually the
unit type in category theory, and `Nothing` is the unit value. These are
classically represented as something like `()` for both the type and the value,
but I prefer the explicit name for a language like Camille. Feel free to
completely ignore this paragraph, but I just thought that it was cool to draw
some parallels between theory and practice.

And so, what started as a crazy idea halfway through my project became one of
the defining features of the language.

## The `Callable` Type (For Real This Time)

The `Callable` type is very interesting. I would go so far to say that it is my
*favorite* type, because it is---in some ways---the only type that a Camille
programmer can actually create.

Let me explain. `Callable` represents something that is callable
<span>(duh!)</span>{: .mutter}.  The most obvious callable value is a function.

Just a bit of a warning, the rest of the section is very theory-oriented. If you
don't really care about *why* I made the design decisions that I did, you can
just skim this section.

In Camille, functions are nameless, so if we want to reuse them, we must assign
them to a variable.  Nameless (also known as "anonymous") functions like these
are typically called lambda functions, or lambdas for short. As such, functions
in Camille are internally referred to as lambda expressions. Because functions
are values in Camille, we can easily assign them to variables, so we don't have
to worry about the fact that functions themselves don't have names.

Let's say we want a function *f* that takes in two integers and returns two
times the first integer plus the second integer In math, this may be written as:

\\[ f(x, y) = 2x + y \\]

However, we are dealing with anonymous functions now, so a better way to express
what we want is that we want a variable *f* that has a value of a function that
takes in two parameters and does the operations that we want on them. This would
be written something like this:

\\[ f = (x, y) \mapsto 2x + y \\]

Note that the right-hand side of the equation could stand completely on its own
and be a valid anonymous function. The same is not true for the first form
because *x* and *y* are not defined. If you are interested in this kind of
stuff, I recommend that you look into [lambda calculus][lambda], which is the
branch of math that this falls under.

Now, what would the type of *f* be? Well, that question is very easy to answer
if you look at second equation! Using the Camille notation that `a :: T` means
`a` is of type `T` (blatantly stolen from Haskell), we would say that:

`f :: (Integer, Integer -> Integer)`{: .center}

Now, let's get to the meat of this section! Internally, this would be
represented as:

{% highlight haskell linenos=table %}
CallableType [Integer, Integer] Integer
{% endhighlight %}

The interesting thing to note here is that `CallableType` actually requires two
parameters in order to actually be a type. So, in Camille, it's not actually
completely true to say that something is of type `Callable`, because that would
be like saying something in Haskell is of type `->`. The `Callable` is like `->`
in Haskell, it requires a left-hand side and a right-hand side. The main
difference is that functions are not curried in Camille, and so the left hand
side can be a *list* of types as opposed to just a single type like in Haskell.

So, that's the type of *f*! The type follows very naturally from the lambda
calculus definition of *f*.

Now let's look at something a bit more peculiar. What is the type of `1` in
Camile? Well, obviously its just `Integer`, right?

**Wrong!** <span>(sorry, I really set you up for failure with that
question...)</span>{: .mutter}

It is actually of type `Void -> Integer`, i.e. `CallableType [Void] Integer`.
Remember, numbers are actually functions that return their numeric value (which
itself is technically a function).

The way I like to think of this is that we have a function that gives us what we
know to be the value `1`. We can't actually define what `1` is though, other
than the fact that it is simply `1`. In other words, `1` is just a concept that
exists solely within the human mind that we typically represent with the numeral
"1".  Just as there is no physical manifestation of `1`, there is no way to
directly construct something of type `Integer` within Camille. The best that we
can do is define a function *returns* what humanity has defined to be `1`, i.e.
the number that follows `0` and precedes `2`.

I'd be lying if this absurdity  wasn't one of my favorite parts of Camille.

This brings us to the second purpose of `Void`!

## The Second Purpose of Void

I secretly slipped in the second usage of `Void` into the last section. In
addition to being the return type of an expression, it can also be used as the
type of a parameter to a `Callable` expression. This allows us to do things
like:

- `1 :: (Void -> Integer)`
- `"abc" :: (Void -> String)`
- `printHello :: (Void -> Void)` (imagine we have a function named `printHello`
that takes no arguments and just prints "Hello" to the terminal)

In our lambda calculus way of expressing things, the our functions would look
like this:

\\[ 1 = () \mapsto 1 \\]
\\[ \text{"abc"} = () \mapsto \text{"abc"} \\]
\\[ \text{printHello} = () \mapsto () \\]

The first two of these functions are clearly infinitely recursive, so instead of
being implemented in our conceptual way, they are just implemented how integers
and strings are usually implemented (but they do *behave* like the above
definitions).

# Implementing `Type.hs`



[part1]: {% post_url 2015-08-16-create-your-own-programming-language-part-1 %}
[repl]: https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
[null]: http://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare
[lambda]: https://en.wikipedia.org/wiki/Lambda_calculus
