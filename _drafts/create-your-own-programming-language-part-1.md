---
layout: post
title: "Create Your Own Programming Language: Part 1"
---

# How to Create Your Own Programming Language: Part I

This is **Part I** of a series of blog posts where I will show you how I created
my own statically-typed, interpreted programming language, Camille. I'm not
claiming that this is the best way or the most efficient, but it worked for me!

## The Setup

I used my favorite programming language, [Haskell][haskell], because it is
**(a)** really awesome and **(b)** very well suited to programming language
creation. This tutorial will not focus on teaching Haskell itself, so if you
have never seen a line of Haskell code in your life, I would recommend checking
out [Learn You a Haskell][lyah]. *Learn You a Haskell* (LYAH as it is often
called in the Haskell community) is an excellent textbook chock-full of awesome
Haskell-y things and funny drawings that can take you from a Haskell newbie to
basic proficiency and literacy.

This tutorial assumes no previous knowledge of very fancy Haskell concepts or
any understanding of programming language theory or compiler design. If you
don't know what software transactional memory or an abstract syntax tree are,
that's completely fine! I definitely didn't either, until I worked on this
project---and I'm certainly not an expert even now.

Lastly, won't be focusing too much on efficiency or anything fancy like that, but we will have a fully-fledged
language that will be able to solve the first three problems of [Project
Euler][project-euler]. Let's get started!

## Designing the Language

This part is very fun! We get to decide what niche are language fits in and how
it will look. One thing to note, however, is that this is very much a continual
process when making a language. Whatever you think up here is absolutely *not*
set in stone. In fact, I got really excited when I first started to make
Camille and wrote up a huge list of example cases and test scenarios, only to
later change the syntax and semantics of my langauge, rendering the document
completely useless.

Because this process is so dynamic, I highly recommend that you not spend *too*
long of a time making your syntax perfect here, but rather just something that
will get you started.

I found it helpful to create a specification document using a format very
similar to the [Backus–Naur Form][bnf]. Don't worry about the exact format of
your specification document, I just wrote whatever felt natural. This document
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
helpful. We will start with a much more basic version, and as we go along, I'll
introduce more complexity until we get to this full version.

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

Can you tell which Project Euler problem it is? It's [Problem 2][pe2]{:
.spoiler}!

It's certainly not the most readable language (infix operators would really
help), but it's definitely a start. Despite its shortcomings, you can definitely
discern some standard computer science paradigms, with return statements,
recursion, and variable types.

Speaking of variable types, I mentioned early that Camille would be a
statically-typed, interpreted language, *not* compiled.

Why??? Who does that???

Well, for starters, it is much easier to implement an interpreter than a
compiler. If you are intereted in a compiled language, I highly recommend
reading about LLVM, a colllection of tools that makes compiler writing
significantly easier. Stephen Diehl has written an [excellent
tutorial][haskell-llvm] for LLVM in Haskell, so go check that out if you're into
that kind of thing.

More importantly, however, is that I really saw a Void (category theory pun
intended) that could be filled with a language like this. Everyone loves the
ease-of-use of languages like Python or Ruby, but none of them (that I know of)
have a really solid type system. I won't make this paragraph a debate about
static vs. dynamic typing, but I personally am a fan of static
typing---especially when it's done right, like in Haskell. By now I'm sure you
can tell that I love Haskell.

Did I mention that I loved Haskell? I do. You will too, soon enough (if you
don't already).

[project-euler]: http://projecteuler.net
[haskell]: http://haskell.org/
[lyah]: http://learnyouahaskell.com/
[bnf]: https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_Form
[pe2]: https://projecteuler.net/problem=2
[haskell-llvm]: http://www.stephendiehl.com/llvm/
