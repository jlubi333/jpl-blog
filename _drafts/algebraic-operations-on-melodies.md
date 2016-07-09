---
layout: post
title: "Algebraic Operations on Melodies"
---

{% include math %}

>May not music be described as the mathematics of the sense, mathematics as
>music of the reason?
><cite>James Joseph Sylvester</cite>

I have always been very drawn to the intersection of math and music. During the
summer of 2015, I attended the [Mathematica Summer Camp][msc] (which was very
fun, by the way) and had to complete a programming project in Mathematica over
the course of two weeks. Naturally, I wanted to do something with math and
music.

I eventually settled on a random song generator, although with a little bit of a
twist. Instead of generating a random melody, the generator will take two input
melodies and apply a random set of operations to them, resulting in a unique
song. In other words, the source of randomness is not in the melodies itself,
but in how they are transformed, repeated, and layered over themselves.

{% include image url="http://demonstrations.wolfram.com/AlgebraicOperationsOnMelodies/HTMLImages/index.en/popup_3.jpg"
                 caption="Algebraic Operations on Melodies Wolfram Demonstration"
                 source-url="http://demonstrations.wolfram.com/AlgebraicOperationsOnMelodies/"
                 source-name="Wolfram Demonstrations Project" %}

I am pretty happy with the result, but due to time constraints I couldn't make
it as grand as I would have liked (and I haven't worked on it since then). For
instance, one thing that the program currently lacks is the ability to input a
custom melody---instead, it relies on a few built-in ones. To be truly viable as
a composition tool, I would most certainly need to include custom melodies,
among other things.

And so, without further ado, I present to you my [algebraic music
generator][aoon].

Regardless of the true flexibility and utility of the program, I find the
mathematics behind it really interesting, so in this post I will focus on the
math side of this project rather than the programming side.

## Abstract Algebra

The core mathematical tool behind this project is [abstract algebra][algebra].
Although I'm but a lowly incoming college freshman who hasn't actually *taken*
abstract algebra, I've learned a little bit about it on my own from the
Internet, mainly due to my interest in [Haskell][haskell] and [category
theory][category].

As usual, Wikipedia summarizes this concept very nicely, in a completely
non-self-referential and totally useful way:

>**Abstract algebra** (occasionally called **modern algebra**) is the study of
>algebraic structures.

Wasn't that helpful? To be fair, they aren't *wrong*.

I view abstract algebra as a way to *abstract* concepts that appear in many
different forms. For example, it lets us treat, say, the symmetries of regular
polygons in the same way that we might treat the permutations of a Rubik's cube.
It allows us to describe certain behaviors and properties of these objects using
a unified and powerful language.

So what does this have to do with music? Well, it turns out that polygons and
Rubik's cubes aren't the only things that abstract algebra can describe---in
fact, that's far from the truth! A huge variety of concepts from a wide variety
of disciplines lend themselves well to being studied under the lens of abstract
algebra.

Music is no exception.

## The Algebra of Music

### Some Background

Let's think musically now. Anyone who has ever played an instrument knows that
music is made up of a series of notes, each having a particular frequency (or
*pitch*) and *duration*. For now, I will focus primarily on pitch, but duration
(and rhythm in general) is a very important topic that we will cover later!

Pitches are assigned letter names: A, B, C, D, E, F, or G. These are the white
notes on a piano (see below). However, there are also pitches *in between* some
of these letters. These correspond to the black notes on a piano. We represent
them with either a "sharp" (symbolized ♯) or "flat" (symbolized ♭). A sharp
raises a pitch by a half-step, and a flat lowers a pitch by a half-step (a
half-step is simply the distance from one note on the piano to the next,
regardless of color). So, for example, A♯ ("A sharp") is the black note just to
the right of A on the piano. This could also be symbolized B♭ ("B flat"). We say
that A♯ and B♭ are *enharmonic* because they represent the same note.

{% include image url="http://www.piano-keyboard-guide.com/wp-content/uploads/2015/05/32-key-keyboard-1.jpg"
                 caption="A piano keyboard with note names"
                 source-url="http://www.piano-keyboard-guide.com/piano-keyboard-layout.html"
                 source-name="Piano-Keyboard-Guide.com" %}

One thing to watch out for is that E♯ is enharmonic to F, because E is right
next to F on the piano (no black keys in between). Similarly, B♯ is enharmonic
to C, because B is right next to C. This gives us a total of twelve possible
pitches:

1. A
1. A♯/B♭
1. B
1. C
1. C♯/D♭
1. D
1. D♯/E♭
1. E
1. F
1. F♯/G♭
1. G
1. G♯/A♭.

You'll notice that I've listed twelve pitches, but you know as well as I that
there are many more than just twelve keys on a piano. Even in the picture above
you can see more than twelve! The reason for this is that this twelve-note
group of notes repeats itself all the way up and down the piano. So, in
reality, there actually multiple notes that we would call "C," but all their
frequencies are so closely related to each other that they all sound like a C
to us.

Each C resides in a different *octave*, but they are all the same note.
To get from a note that is in one octave to the same note in the octave above,
all you need to do is multiply its frequency by two. So, if we know that one C
has a frequency of 440 Hz, we also know that 880 Hz and 220 Hz represent C as
well.

## Operations on Notes

So, what can we do with these notes?

[msc]: https://education.wolfram.com/summer/camp/programs/mathematica/
[aoon]: http://demonstrations.wolfram.com/AlgebraicOperationsOnMelodies/
[algebra]: https://en.wikipedia.org/wiki/Abstract_algebra
[haskell]: https://www.haskell.org/
[category]: https://en.wikipedia.org/wiki/Category_theory
