---
layout: post
title: "Create Your Own Programming Language: Part I"
---

This is **Part I** of a series of blog posts where I will show you how I created
my own statically-typed, interpreted programming language, Camille. I'm not
claiming that this is the best way or the most efficient, but it worked for me!

## The Setup

In this tutorial, I will use favorite programming language, [Haskell][haskell],
because it is **(a)** really awesome and **(b)** very well suited to programming
language creation. This tutorial will not focus on teaching Haskell itself, so
if you have never seen a line of Haskell code in your life, I would recommend
checking out [Learn You a Haskell][lyah] (LYAH as it is often called in the
Haskell community). It is an excellent textbook chock-full of awesome Haskell-y
things and funny drawings that can take you from a Haskell newbie to basic
proficiency and literacy.

This tutorial assumes no previous knowledge of very fancy Haskell concepts or
any understanding of programming language theory or compiler design. If you
don't know what software transactional memory or an abstract syntax tree are,
that's completely fine! Until I worked on this project, I definitely didn't
either---and I'm certainly not an expert even now.

Lastly, we won't be focusing too much on efficiency or anything fancy like that,
but we *will* have a fully-fledged language that will be able to solve the first
three problems of [Project Euler][project-euler]. Let's get started!

## Designing the Language

This part is very fun! We get to decide what niche are language fits in and how
it will look. One thing to note, however, is that this is definitely a continual
process when making a language. Whatever you think up here is absolutely *not*
set in stone. In fact, I got really excited when I first started to make Camille
and wrote up a huge list of example cases and test scenarios, only to later
change the syntax and semantics of my language, rendering the entire document
completely useless.

Because this process is so dynamic, I highly recommend that you not spend *too*
long of a time making your syntax perfect here, but rather just create something
that will get you started. Alternatively, you can just follow along with me for
the first time and I'll show you exactly how I implemented the syntax of
Camille.

I found it helpful to create a specification document using a format very
similar to the [Backus–Naur Form][bnf]. I didn't worry about the exact format of
my specification document, I just wrote whatever felt natural. This document
will likely be updated many times, so don't worry about getting it perfect the
first time!

For an idea of what I did, here is my specification document in its full glory:

    identifier-first ::= <letter> | _
    identifier-rest  ::= <alpha-num> | _
    identifier       ::= <identifier-first> <identifier-rest>*
    nothing          ::= Nothing
    block            ::= <type> { <expression>* }
    integer          ::= <digit>
    string           ::= " <ascii-char>* "
    boolean          ::= False | True
    if               ::= if <expression> <expression> [else <expression>]
    type             ::= Void | Integer | String | Boolean
    type-declaration ::= <type> <space> <identifier>
    fparams          ::= <type-declaration> [, <type-declaration>]*
    fargs            ::= <expression> [, <expression>]*
    lambda           ::= \ ( <fparams> ) -> <expression>
    fcall            ::= <identifier> ( [<fargs>] )
    assignment       ::= <identifier> = <expression>
    ret              ::= ret <expression>
    variable         ::= <identifier>
    expression       ::= <nothing>
                       | <block>
                       | <integer>
                       | <string>
                       | <boolean>
                       | <if>
                       | <lambda>
                       | <ret>
                       | <type-declaration>
                       | <fcall>
                       | <assignment>
                       | <variable>

I know it looks kind of scary, but like I said before, it's actually really
helpful. We will start with a much more basic version, and I'll introduce more
complexity as we go along until we get to this full version.

So what does Camille look like in practice? Here's a sneak peak at our final
goal, the solution to a Project Euler problem written in our very own language!

    f :: (Integer, Integer, Integer, Integer -> Integer)
    f = \(max :: Integer, acc :: Integer, prev :: Integer, cur :: Integer) -> Integer {
        if gt(cur, max) ret acc
        else ret Integer {
            next :: Integer
            next = add(prev, cur)
            if eq(mod(next, 2), 0) ret f(max, add(next, acc), cur, next)
            else ret f(max, acc, cur, next)
        }
    }
    print(f(4000000, 0, 0, 1))

Can you tell which Project Euler problem it is?

{: .spoiler}
It's [Problem 2][pe2]! Problem 1 was too simple, and Problem 3 was too
complicated. Problem 2 was just right.

It's certainly not the most readable language (infix operators would really
help), but it's definitely a start. Despite its shortcomings, you can definitely
discern some standard computer science paradigms. There are, for example, return
statements, recursive calls, and variable types.

Speaking of variable types, I mentioned earlier that Camille would be a
statically-typed, interpreted language, *not* compiled.

*Why??? Who does that???*

Well, for starters, it is much easier to implement an interpreter than a
compiler. If you are interested in creating a compiled language, I highly
recommend reading about LLVM, a collection of tools that makes compiler writing
significantly easier. Stephen Diehl has written an [excellent
tutorial][haskell-llvm] for LLVM in Haskell, so go check that out if you're into
that kind of thing.

More importantly, however, is that I saw a Void <span>(category theory pun
intended)</span>{: .mutter} that could be filled with a language like this.
Everyone loves the ease-of-use of an interpreted language like Python or Ruby,
but none of these languages (that I know of) have a really solid type system. I
won't make this paragraph a debate about static vs. dynamic typing, but I
personally am a big fan of static typing---especially when it's done right, like
in Haskell. By now I'm sure you can tell that I love Haskell.

Did I ever mention that I love Haskell? I do. A lot. You will too, soon enough
(if you don't already).

## The Tools

To follow along with this tutorial, you will need [Haskell][haskell] (obviously)
and [Cabal][cabal].  Once you have these installed (either via a package manager
or the [Haskell Platform][haskell-platform]), we will need to download a
package: [Parsec][parsec].

### What is Parsec?

According to the [Haskell Wiki page for Parsec][haskell-wiki-parsec]:

> Parsec is an industrial strength, monadic parser combinator library for
Haskell. It can parse context-sensitive, infinite look-ahead grammars but it
performs best on predictive (LL[1]) grammars.

{: .mutter}
Erm... okay... what's a combinator again?

Don't worry if you don't know what any of this means. Like I said before, I had
absolutely no idea what any of this was when I started. For our purposes, we
will just be using Parsec as a nice library that makes parsing text very easy.

To install Parsec, just type the following into the command line:

    cabal install parsec

Parsec should work right out of the box.

Congratulations, you are now ready to write your very own programming language!

## A Final Note

Before we dive in, I'd like to point out a few resources that you may find
valuable (I certainly did).

- [Write Yourself a Scheme in 48 Hours][write-a-scheme]: Exactly what it says on
    the tin. Of all the resources I've mentioned, I probably learned the most
    from this one.
- [DIY: Make Your Own Programming Language, by Mattias Appelgren][diy]: Also
    exactly what it says on the tin. The author uses Rust, which is a cool new
    language that looks very promising. The language that he creates is
    unfortunately not
    fully featured, however the parts that he does cover are very nicely done.
- [Stephen Diehl's LLVM Tutorial][haskell-llvm]: So nice I mentioned it twice.
- [Write You a Haskell][wyah]: Also by Stephen Diehl. It is a very cool
    foray into the world of creating a functional programming language, but it
    is sadly incomplete.
- [Parsing a Simple Imperative Language, Haskell Wiki][while]: This short wiki
    article discusses parsing a simple imperative language called "While", but
    it uses more advanced features of Parsec that I feel are unnecessary.
- Lastly, if you'd like to see the full implementation of what we'll be making,
    you should check out my [repository on Github][camille] for Camille.

Without further ado, [let's-a-go][part2]!

[project-euler]: http://projecteuler.net
[haskell]: http://haskell.org/
[lyah]: http://learnyouahaskell.com/
[bnf]: https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_Form
[pe2]: https://projecteuler.net/problem=2
[haskell-llvm]: http://www.stephendiehl.com/llvm/
[cabal]: https://www.haskell.org/cabal/
[haskell-platform]: https://www.haskell.org/platform/
[parsec]: https://hackage.haskell.org/package/parsec
[haskell-wiki-parsec]: https://wiki.haskell.org/Parsec
[write-a-scheme]: https://en.wikibooks.org/wiki/Write_Yourself_a_Scheme_in_48_Hours
[diy]: http://blog.ppelgren.se/2015-01-03/DIY-Make-Your-Own-Programming-language/
[wyah]: http://dev.stephendiehl.com/fun/
[while]: https://wiki.haskell.org/Parsing_a_simple_imperative_language
[camille]: https://github.com/jlubi333/Camille
[part2]: {% post_url 2015-08-18-create-your-own-programming-language-part-2 %}
