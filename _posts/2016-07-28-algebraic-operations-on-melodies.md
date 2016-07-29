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

- \\( a(x, d(x, y)) = y \\)
- \\( a(a(x, d), -d) = x \\)
- \\( a(x, 0) = x \\)

Can you think of any others?

The properties that I have listed should actually make a lot of sense. In
fact... they actually look really, really similar to some other operations that
I am sure you are very familiar with!

We run into a little bit of trouble with our functions if we explore around the
edges of our group of pitch classes. For example, what is \\(a(G♯, 1)\\)? There
doesn't seem to be anything "above" a G♯. However, remember that our pitch
classes repeat themselves; one note above G♯ is A! Our pitch class "wraps
around" and goes back to the first element. In fact, this is the same behavior
as the hands on a clock! If you add two hours to eleven o'clock, you don't end
up with thirteen o'clock---the numbers wrap around and you are left with one
o'clock. In mathematics, we call this *modular* arithmetic. We will return to
this idea later.

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
apparent function over the integers that satisfies the laws of \\( a \\) is just
ordinary addition:

- \\( 5 + (8 - 5) = 8 \\)
- \\( 6 + 2 - 2 = 6 \\)
- \\( 9 + 0 = 0 \\)

And so, it seems as if, for the integers, \\( d(x, y) = y - x \\) and \\( a(x,
n) = x + n \\).

Let us see if we can formalize this, and perhaps prove that
ordinary subtraction and addition really do fit our laws for \\( d \\) and \\( a
\\).

### Formalizing Our Operations

Let us first focus on our \\( a \\) function. We are now ready to formally
define exactly the laws that a function must follow if it wishes to serve as an
\\( a \\) function. This will allow us to generalize our \\( a \\) function to
pretty much anything we can think of, including music (which is how we got into
this whole formalization business in the first place).

- Firstly, we want \\( a \\) to be *associative*. All this means is that \\(
  a(a(x, y), z) = a(x, a(y, z)) \\). Ordinary addition follows this law for
  integers: (x + y) + z = x + (y + z).
- Secondly, we want there to be an *identity element* for the set over which \\(
  a \\) operates. Again, this is a really fancy word for a very simple concept;
  simply put, we want an object \\( e \\) to exist such that \\( a(x, e) = a(e,
  x) = x \\). And once again, ordinary addition follows this law for integers.
  In this case, \\( e = 0 \\), and so we have: \\(x + 0 = 0 + x = x \\).
- Thirdly, we want there to be an *inverse element* for every object in the set
  over which \\( a \\) operates. This means that for every object in the set,
  there should be a corresponding object that "undoes" \\( a \\) when combined
  with the original object. If our original element is notated \\( x \\), then
  the inverse element is typically notated \\( x^{-1} \\). For integers under
  ordinary addition, \\(x^{-1} = -x\\) (this is not to be confused with the
  multiplicative inverse; we are talking about addition here). As a concrete
  example, the inverse element under addition for the integer \\(5\\) is
  \\(-5\\), for \\(3\\) it is \\(-3\\), and for \\(0\\) it is \\(0\\).
- Lastly, we have a property that is a little bit weird. However, it is very
  nice to have, and cannot be proven from the three laws above. It should be so
  intuitive as to be redundant, but it is necessary to list. We want \\(a\\) to
  exhibit a property known as *closure*: that \\( a(x, y) \\) is always in the
  same set as \\(x\\) and \\(y\\). In the case of integers, this means that
  adding two integers will always result in another integer. An example of an
  operation that is **not** closed over the integers would be division: \\(3
  \div 2 \\) is \\(1.5\\), which is clearly not an integer.

And so, because addition over the integers passes the above four tests, we can
hereby decree it to be an official representative of the society of \\(a\\)!
Woohoo!

In other words, addition follows all of the laws that we expect \\(a\\) to
follow, and so if we were to define \\(a\\) over the integers, letting \\(a(x,
y) = x + y\\) would be a perfectly valid thing to do.

This process will also work with real numbers and addition. It will also work
with the positive real numbers and multiplication. Try working out \\(a\\) for
different sets of numbers, or even seemingly non-mathematical things like food
or crochet! The applications are limited only by your imagination. Sometimes you
will find something that works (as in the case of addition over the integers),
and other times you will find that it will not work (such as in the case of
multiplication over the integers). Each time you try it out, the operation,
identity element, and inverse elements will all be different. It's very fun to
try to stretch your brain to come up with ways to fit this mathematical
abstraction to the real world, and in many cases, you will find the operation to
be very intuitive in the end!

Speaking of real world applications... let's get back to the music!

...But first, let's talk about our original function, \\( d \\). It turns out
that our definition of \\(a\\) is powerful enough to be able to define \\(d\\)
in terms of \\(a\\). This means that we don't need to come up with a new set of
laws! We can rely on the fact that we proved \\(a\\) to be correct, and so we
know that \\(d\\) has solid mathematical grounding. It's quite a simple
definition, but it uses the important fact that every element must have an
inverse. We let \\( d(x, y) = a(y, x^{-1})\\), and we're done. For integers,
this means that \\( d(x, y) = a(y, -x) = y - x\\), which is exactly what we said
before. We can now prove our properties about \\(d\\), too. For the case of
integers, we have:

- \\( -d(y, x) = -(x - y) = -x + y = y - x = d(x, y) \\)
- \\( d(x, x) = x - x = 0 \\)

As you can see, \\(d\\) is redundant when we have the awesome power of \\(a\\).

Oh, by the way, you just did some of that [abstract algebra][algebra] stuff.
Specifically, you just learned the basics of [group theory][group-theory]. An
operation that follows our \\(a\\) laws, combined with a set of objects (like
the integers), forms a [group][group]. And that's really, really cool. Integers
under addition form a group, real numbers under multiplication form a
group[^reals], rotations of polygons form a group, Rubik's cube permutations
form a group, "clock" (modular) arithmetic forms a group, and finally, we can of
course, apply group theory to music. All of these can be described in terms of
our all-powerful \\(a\\) laws (the \\(a\\) laws are formally called the *group
laws*, by the way, but I hadn't wanted to throw out the term group before now).

[^reals]:
    If you want to be truly mind blown, look at [this][mse] Math StackExchange
    post once you are comfortable with the notion of a group (which you should
    be after the next few sections). My mind figuratively exploded when I read
    this for the first time.

*Now* let's get back to the music!

### Operations on Pitch Classes (Revisited)

We run into some issues if we try to formulate \\(a\\) as we did before: most
egregiously, our definition of \\(a\\) for pitch classes takes in a pitch class
and an integer---that's not how groups work! Groups utilize one set, and one set
only. We can't deal with both pitch classes and integers at the same time; they
are incompatible! Or are they? What if pitch classes *were* integers? Then we
would only be dealing with one set: the integers (or the pitch classes, because
saying "pitch classes" would be synonymous with saying "integers").

But no, that's ridiculous. There are an infinite number of integers, and only
twelve pitch classes. But you know what else there is twelve of? Hours in a day.
Coincidence? Yeah, pretty much... or is it?

Yeah, it is. However, we *can* borrow the idea of modular arithmetic from clocks
and apply it here beautifully. Remember that our pitch classes behave just like
the hands on a clock: one semitone above G♯ is A. In a sense, twelve (G♯)
"equals" zero (A). In the case of the integers, we would say that twelve is
equal to zero (modulo twelve). This is written out as \\(12 \equiv 0\
(\text{mod}\ 12)\\). We could also say that, for example, \\(14 \equiv 2\
(\text{mod}\ 12)\\) and \\(5 \equiv 2\ (\text{mod}\ 3)\\).

And so, we can create a group for integers from zero to twelve (or any
upper bound) with the operation of "clock" (modular) arithmetic. You can verify
that all the laws hold, if you wish. One interesting thing to take note of is
the inverse element in this group; because there are no longer negative numbers,
the inverse element must be something else. If you think about it, winding the
clock back one hour is the same as moving it ahead by eleven. I'm sure you have
had the experience of trying to set an old clock to a particular time, only to
accidentally pass a certain number, which results in you having to increase all
the way past twelve hours and go back to the number you wanted (it's even worse
for minutes, because you're working modulo sixty). This is the concept of an
inverse element working in real life---you've been working with inverse elements
for longer than you've thought!

Now that we have a group for our clock numbers (zero through twelve), we are but
one step away from creating a group for our pitch classes. In fact, we pretty
much already did so!  We mapped every pitch class to a clock number, so wherever
we see a clock number, we can replace it with the corresponding pitch class, and
vice versa.  This one-to-one mapping is called an [isomorphism][iso] in group
theory. Look at you, learning all these fancy words for simple concepts!

And so, it is time to define our first proper operation on pitch classes. How
exciting! Well, in reality, we'll just be using our old clock arithmetic with a
fancy new name. In pitchclassland, clock arithmetic becomes the new and shiny
"pitch shift" operator! It is exactly our original definition of \\(a\\), but
now with all the kinks worked out. For example:

- \\( a(A, 2) = B \\)
- \\( a(G, 3) = A♯ \\)

Although it seems like we haven't accomplished much, we actually have a lot of
power in our hands now. We've rigorously created a mathematical formulation of
music at the most basic level, the pitch class. The bulk of the work that we
have done so far wasn't necessarily in creating \\ a \\), but rather the
mathematical foundation of *any* operation that we could apply.

What other operations can you define on the set of pitch classes? The sky is the
limit!

Next, we shall explore another mathematical structure that appears within music:
the *monoid*.

## Monoids

Monoids are actually really simple, especially compared to groups. Monoids are
simply groups without inverse elements; that is, there doesn't need to be an
undo operation. So, every group is also a monoid, but not every monoid is a
group. You can think of monoids as things that can "add," but not "subtract."

The canonical example of a monoid is string concatenation (addition): "abc" +
"def" = "abcdef". We can't really "subtract" a string from this, so string
concatenation forms a monoid, rather than a fully-fledged group.

That's all! A monoid is a group without inverse elements; a group is a monoid
with inverse elements.

Let us now explore what we can apply monoids to within music. Now that we know
the theory behind some basic abstract algebra, we don't have to go through the
back-breaking effort of trying to understand the abstract concepts! We can just
apply what we already know.

## The Chord Monoid

## Parallel Composition Monoid (Chords)

We can define another operator, \\( p \\), that takes in two notes and returns
the chord created when stacking them (for now, we will ignore note duration, but
this approach will work with different durations too). I call this operator \\(
p \\) because I like to think of it as a **p**arallel composition operator (as
opposed to sequential, which we will see later). We shall see that \\( p \\)
forms a monoid over the set of all chords.

As an example usage of \\(p\\), if we wanted to play the notes C4 and E4 at the
same time, we could construct this as \\( p(C4, E4) \\). This will return a
chord. Note that \\( p \\) is *not* an operator over the set of all pitch
classes (as \\(a\\) was), but rather an operator over the set of all *musical
objects* (all notes, all chords, and as we will see later, all sequential
compositions). Now, \\(p\\) will take in two musical objects and return a new
musical object This allows us to construct chords with many more notes by
repeatedly applying \\(p\\); for example, if we wanted a C major chord, we could
write \\( p(p(C4, E4), G4)\\).

Let us quickly verify that \\(p\\) forms a monoid over the set of all chords:

- **Associatve Property**: \\(p(p(x, y), z) = p(x, p(y, z)) \\). \\(x\\) and
    \\(y\\) stacked with \\(z\\) is the same as \\(x\\) stacked with \\(y\\) and
    \\(z\\).
- **Identity Element**: The identity element is the chord with zero notes (the
    "empty chord").
- **Closure Property**: Any chord composed with any other chord is a chord.

Let's see one more monoid.

## Sequential Composition Monoid

We will define another operation, \\(s\\) to be the operation that composes two
musical objects *sequentially*, one after another. You can verify the three
monoid laws for yourself, if you'd like. What do you think the identity element
would be?  Hint: <span>don't forget that notes have a duration, too</span>{:
.spoiler}.

## Algebraic Operations on Melodies

And now, we can apply all of what we have learned to actual music. We have
already defined two very important operators, parallel and sequential
composition, that operate on sets of notes and chords (musical objects).
Parallel composition will take two musical objects and layer them; sequential
composition will take two musical objects and concatenate them. We can define
many more such operators, like the reverse operator, \\(r\\). It takes in a
single musical object and reverses it; a note reversed is just the note itself,
a parallel composition reversed is all its components reversed, and a sequential
composition reversed is the composition backward.

We can also generalize \\(a\\) to work on any musical object; chords can be
shifted up and down, and so can melodies.[^tonality] You can think of many
different algebraic operations on melodies, like changing note durations,
inverting notes, repeating sequences, etc., but I will leave that up to you!

[^tonality]:
    In my project specifically, I allow for shifting up and down by either major
    intervals, minor intervals, or any (atonal) interval---this adjusts the
    "flavor" (tonality) of the randomly generated songs.

The key application of this theory in my project is that I define a set of a few
of these operations, and randomly (and recursively) apply them to some starter
melodies. This is a very flexible approach, as incredibly complex songs can be
generated from simple melodies and operations.

In other words, the notes themselves of the song are not randomly generated;
instead, the operations *applied* to these notes are randomly selected.

And that's it! I believe that this approach provides an elegant and simple
framework for manipulating melodies and musical objects that is easily
extensible in many different ways. I encourage you to think about how you would
do things differently, or how you could extend what I have done. Most
importantly, have fun with it! It is music, after all: the mathematics of sense.

[msc]: https://education.wolfram.com/summer/camp/programs/mathematica/
[aoon]: http://demonstrations.wolfram.com/AlgebraicOperationsOnMelodies/
[algebra]: https://en.wikipedia.org/wiki/Abstract_algebra
[haskell]: https://www.haskell.org/
[category]: https://en.wikipedia.org/wiki/Category_theory
[overtones]: https://en.wikipedia.org/wiki/Overtone
[mse]: http://math.stackexchange.com/a/1151115/161819
[group-theory]: https://en.wikipedia.org/wiki/Group_theory
[group]: https://en.wikipedia.org/wiki/Group_(mathematics)
[iso]: https://en.wikipedia.org/wiki/Isomorphism
