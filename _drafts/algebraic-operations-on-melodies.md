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

Regardless of the fact that my program is of dubious flexibility and utility, I
find the mathematics behind it really interesting, so in this post I will focus
on the math side of this project rather than the programming side.

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

Wasn't that helpful? To be fair, Wikipedia isn't wrong, per se.

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

<span>(If you are familiar with the notion of pitch classes, please feel free to
skip ahead to the [next section](#operations-on-pitch-classes).)</span>{:
.mutter}

Let's think musically now. Music is made up of a series of notes, each having a
particular frequency (or *pitch*) and *duration*. For now, I will focus
primarily on pitch, but duration (and rhythm in general) is a very important
topic that we will cover later!

Pitch is divided into two parts: a *pitch class* and an *octave*. Pitch classes
are assigned letter names: A, B, C, D, E, F, or G. These are the white notes on
a piano[^piano] (see picture below). However, there are also pitch classes in
between some of these letters! These correspond to the black notes on a piano.
We represent them with either a "sharp" (symbolized ♯) or "flat" (symbolized ♭).
A sharp raises a pitch by a half-step, and a flat lowers a pitch by a half-step
(a half-step, or *semitone*, is simply the distance from one note on the piano
to the next, regardless of color). So, for example, A♯ ("A sharp") is the black
note just to the right of A on the piano. This could also be notated B♭ ("B
flat"). We say that A♯ and B♭ are *enharmonic* because they represent the same
note.

[^piano]:
    I will use the piano as a pedagogical tool because of its easily-visualized
    keys, but these concepts apply to every pitched instrument in standard
    Western music.

{% include image url="http://www.piano-keyboard-guide.com/wp-content/uploads/2015/05/32-key-keyboard-1.jpg"
                 caption="A piano keyboard with note names"
                 source-url="http://www.piano-keyboard-guide.com/piano-keyboard-layout.html"
                 source-name="Piano-Keyboard-Guide.com" %}

One thing to watch out for is that E♯ is enharmonic to F, because E is right
next to F on the piano (no black keys in between). Similarly, B♯ is enharmonic
to C, because B is right next to C. The same logic applies to F♭ and C♭. This
gives us a total of twelve possible pitches:

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
1. G♯/A♭

You'll notice that I've listed twelve pitch classes, but you know as well as I
do that there are many more than just twelve keys on a piano. Even in the
picture above you can see way more than twelve keys! The reason for this is that
this group of twelve pitches repeats itself all the way up and down the piano.
So, in reality, there are actually multiple notes that we would call "C," but
each one is in a different *octave*. An octave is just grouping of the twelve
pitch classes above. In the above picture, there are about two and a half
octaves visible (C to C is one, to C again is two, and to the last G is about a
half).

There is a very simple relationship between the frequencies of notes that differ
by one octave: a note that is one octave higher than another note will have
twice the frequency of that note. So, if we know that one C has a frequency of
440 Hz, we also know that 880 Hz and 220 Hz represent C as well. So while 220
Hz, 440 Hz, and 880 Hz are different pitches (they reside in different octaves),
they are in the same pitch *class*, C. This simple relationship is why the human
ear perceives all C's to sound alike.[^overtones] This also explains why they
are all grouped together in one pitch class!

[^overtones]:
    The more technical reason that all C's sound so similar is that they share
    many of the same [overtones][overtones]. This is a direct consequence of the
    fact that they are all related in a simple 2:1 ratio.

Put another way, the set of all C's forms the pitch class, whereas one
individual C in a particular octave (with a particular frequency) would be what
we call a pitch. There is no frequency for the abstract notion of a pitch class
(like C), but there is a frequency assigned to a note with a pitch class in an
octave (like C4, the C in the fourth octave).

### Operations on Pitch Classes

So, what can we do with these pitch classes? Well, one thing we could talk about
would be some sort of a notion of distance between them. Looking at the picture
of the piano (or our listing of pitch classes), it would be reasonable to say
that, for example, the distance from A to A♯ is one semitone, the distance from
A to B is two, and the distance from C to G is seven. Additionally, the distance
from any note to itself should be zero.

Let's define a function \\( d \\) (for distance) that determines how far you
have to travel to get from one pitch class to another (in semitones). Using the
above examples, we have:

- \\( d(A, A♯) = 1 \\)
- \\( d(A, B) = 2 \\)
- \\( d(C, G) = 7 \\)
- \\( d(E, E) = 0 \\)

We could extend this definition to account for *signed* distance, meaning that
while the distance from C to G may be *positive* seven, the distance from G to C
would be *negative* seven. We arrive at the following examples:

- \\( d(A♯, A) = -1 \\)
- \\( d(B, A) = -2 \\)
- \\( d(G, C) = -7 \\)

In general, we have the property that, for any notes \\( x \\) and \\( y \\),
\\( d(x, y) = -d(y, x) \\). We also know that \\( d(x, x) = 0 \\). We're off to
a great start!

Another function that we might want to have is one that will give us a new pitch
class if we add a particular signed distance (in semitones) to another pitch
class.  Let's call this function \\( a \\) (for add). We have:

- \\( a(A, 1) = A♯ \\)
- \\( a(A, 2) = B \\)
- \\( a(C, 7) = G \\)
- \\( a(A♯, -1) = A \\)
- \\( a(B, -2) = A \\)
- \\( a(G, -7) = C \\)
- \\( a(F, 0) = F \\)

What properties do we have with this function? Glad you asked! Here are some
that I spot:

- \\( a(x, y) = a(y, x) \\)
- \\( a(x, d(x, y)) = y \\)
- \\( a(a(x, d), -d) = x \\)
- \\( a(x, 0) = x \\)

Can you think of any others?

The properties that I have listed should actually make a lot of sense. In
fact... they actually look really, really similar to some other operations that
I am sure you are very familiar with!

To explore these operations and their properties more, let us first go back to
our old friend, the set of integers, and see if we can make sense of these
operations there.

### Operations on Integers

Let's first try to define \\( d \\) for the integers. It shouldn't take much
convincing to realize that the signed distance between two integers \\( x \\)
and \\( y \\) is simply their difference, \\( y - x \\). Let's try it out:

- \\( d(1, 3) = 3 - 1 = 2 \\)
- \\( d(3, 1) = 1 - 3 = -2 \\)
- \\( d(5, 5) = 5 - 5 = 0 \\)

Our properties of \\( d \\) that we outlined before still hold! These properties
are, in fact, more like *laws* in the sense that we *define* \\( d \\) by these
properties, rather than discovering these properties about \\( d \\) after the
fact. They are intuitive truths upon which we build our function.

This same notion of laws applies to our other function, \\( a \\). One readily
apparent function that satisfies our \\( a \\) laws for the integers is just
ordinary addition:

- \\( 7 + 3 = 10 \\)
- \\( 3 + 7 = 10 \\)
- \\( 5 + (8 - 5) = 8 \\)
- \\( 6 + 2 - 2 = 6 \\)
- \\( 9 + 0 = 0 \\)






---

It appears as if we have found another general property of our function, \\( a
\\)! \\( a(x, y) = a(x, y) \\)

Hmm... there seems to be a pattern here. Our two functions, \\(d\\) and \\(a\\)
seem to be very closely related. But let's continue on.

We run into a little bit of trouble with our functions if we explore around the
edges of our group of pitch classes. For example, what is \\(a(G♯, 1)\\)? There
doesn't seem to be anything "above" a G♯. However, remember that our pitch
classes repeat themselves! One note above G♯ is A! Our pitch class "wraps
around" and goes back to the first element. In fact, this is the same behavior
as the hands on a clock! If you add two hours to eleven o'clock, you don't end
up with thirteen o'clock---the numbers wrap around and you are left with one
o'clock. In mathematics, we call this *modular* arithmetic.

[msc]: https://education.wolfram.com/summer/camp/programs/mathematica/
[aoon]: http://demonstrations.wolfram.com/AlgebraicOperationsOnMelodies/
[algebra]: https://en.wikipedia.org/wiki/Abstract_algebra
[haskell]: https://www.haskell.org/
[category]: https://en.wikipedia.org/wiki/Category_theory
[overtones]: https://en.wikipedia.org/wiki/Overtone
