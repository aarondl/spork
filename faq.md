# FAQ

<!-- TOC -->

- [FAQ](#faq)
- [Purpose](#purpose)
    - [The void](#the-void)
    - [Functional vs Imperative](#functional-vs-imperative)
    - [Philosophy](#philosophy)
        - [Correctness](#correctness)
        - [Performance](#performance)
        - [Simplicity](#simplicity)
        - [Consistency](#consistency)
        - [Completeness](#completeness)
    - [Principles](#principles)
- [Types](#types)
    - [Combined Declaration and Definition](#combined-declaration-and-definition)

<!-- /TOC -->

# Purpose

## The void

Spork is made to be a general purpose programming language. When new languages
enter the scene these days they are typically filling a void left between two
or more other languages.

In the case of Go, it nicely filled a void between C/C++ and Python. It recovers
much of the lost performance of Python, while not sacrificing too much
expressiveness. It produces a single binary which cuts down on Docker abuse
prevalent in the ecosystem of Python/Ruby. Its type system is very clean and
even simpler than Python's which lends itself to readability and easy
understanding of code. On the flip side it abandons much of the inherent dangers
of coding it C/C++, it simplifies much and makes it much less tedious and feels
a lot closer to an interpreted language from that era than its compiled
brothers.

In the case of Rust, it nicely filled a void between C/C++ and Go. Here you have
much more modern constructs, many functional and borrowed from Haskell, as well
as solid performance while not giving up safety. Rust is a safer language that
Go in many ways, also an infinitely safer language than C/C++.

But as there always will be, there exists yet another void between languages.
Now it is between Go, Rust, and Haskell.

Go's error handling is tedious, Rust's isn't that much better, Haskell's is
much worse because it relies on Monad transformers which don't compose. Go and
Haskell rely on a GC where Rust proves that we can move to compiled memory
management and there's performance benefits to be had there. Haskell is as a
language quite unusable because of its evolution over time: between the many
different string types, the import system, the prelude arguments, the horrendous
documentation and the cobbled together dependency management and build systems
it is only for serious enthusiasts. Rust's borrow checker puts up the fight of a
lifetime (pun intended) if you're to accomplish any code at all in it, simply
passing a lambda into a function is an exercise not for the faint of heart. Go's
guarantees in terms of safety are pretty weak with no algebraic data types, no
result/either or option/maybe types to get rid of nil.

Despite these complaints, they all remain titans in the realm of programming
languages. They are languages that have defined the shape of things to come with
their innovations and they should be lauded for it. Go's concurrency and utter
simplicity, Haskell's composability and type system, Rusts memory management and
safety.

Spork exists to attempt to fill the gap by borrowing concepts from each and
making its own slight innovations where necessary.

## Functional vs Imperative

There has been a line drawn in the sand as to whether languages are functional
or imperative, but as time goes on most languages are blurring that line. Rust
as an example takes many of its paradigms from Haskell yet would be classified
as an imperative language. Some hardcore functional fans would say it's because
it is not a functionally pure language.

As these lines blur more and more it seems prudent to start thinking of
languages in terms of "how much support" they lend to one style of programming.

Spork endeavors to support both styles very well. Credit goes to Gary Bernhardt
for the inspiration that this is necessary. His talks "Boundaries" and
"Functional Core, Imperative Shell" are very powerful and it is this sort of
programming Spork wishes to make easy.

Pure functions are amazing. Easy to test, easy to use, easy to compose; it is
true beauty. A person's first baby steps into Haskell open a world filled with
wonder and amazement. But as soon as you end up putting your entire program into
a monad (or a transformer for error handling), or having to deal with efficient
arrays the rose colored glasses are shattered and you realize that impurity is
extremely practical (as is strictness). The answer would seem to be Gary's
concept of mixing the styles where appropriate - to be trite: Use the best tool
for the job.

With this goal in mind its syntax base is that of Haskell because it is harder
to add currying, partial function application, function composition etc. to a
C-like language. It is envisioned that most code should be written in a pure
functional style.

## Philosophy

Along the lines of the "worse-is-better" we lay out an unnamed philosophy here:

### Correctness

Correctness is paramount. It's the year 2020 as of writing, we don't have flying
cars but we should at least have programming languages that are not shaped like
monstrous foot guns.

There's no point in doing anything at all unless it is correct.

### Performance

Rust's performance is amazing given the featureset it boasts. It does love to
talk about zero cost abstractions however you will note that the compile times
and the programmer time (borrow checker fights) indicate that there truly is no
zero cost abstraction. This shows the tradeoff between simplicity and
performance as Rust has chosen performance in an extreme way as it is far from a
simple language.

Comparing Go to Haskell in terms of performance is also dramatic. In Haskell it
is impossible to reason about performance and just as hard to actually measure
and improve. It is largely guess work. In Go there are excellent tools to
measure performance as well as many ways to improve performance where necessary.

We should be able to easily reason about, measure and improve performance in a
correct program. It is also okay to make things less simple in order for a
programmer to opt-in to more performance.

### Simplicity

Go's productivity despite being strongly typed and having `if err != nil`
everywhere is legendary. You can truly get things done but also have a
performant single binary at the end of the day. It shatters the illusion that
interpreted languages are more productive because they lack types or have many
dynamic patterns and shows that simplicity in the language and simplicity in the
toolchain is what really matters.

The faster we can produce correct, performant programs the better.

### Consistency

Consistency can be sacrified for correctness, simplicity, or performance. But
in general the consistency also is mirrored in simplicity. The more consistent
a language is the simpler it will be. Much like a good story internal
consistency is what makes it a pleasure to read.

### Completeness

The design should strive to accomplish as much as possible but when any of the
facets above are threatened it is better to omit a feature until we can find
a design that creates a consistent, performant, simple and correct feature.

## Principles

* Simple grammar: Allow code rewriting/inspection tools
* Crash-free: A program that compiles should never crash
* Type-safe: There is no "trust me" coercion
* Memory-safe: There is no pointer arithmetic or casting of pointers etc
* Good error messages: C++ and Haskell error soup should be avoided
* Inherent build system: Goodbye Make & Autoconf
* Remove classes of errors: Use syntax as a tool to restrict bad actions
* Build time: Extremely fast builds are very important
* Explicit: Implicit language features exist, but you can always choose to be
  explicit over implicit.
* No ambiguity in the grammar: Compiler and programmer know what code will do

# Types

## Combined Declaration and Definition

Creating methods in C++ is very annoying. To modify any object, changes are
required in at least 3 files which are /mostly/ copy pasta, but have subtle
differences that require each to be thought of separately. Example:

```c++

// base.h
class Base {
  virtual Foo<Blah> doSomething(Baz thing) = 0;
//                                  ^^^^^ unused var name adds confusion
}

// derived.h
class Derived : public Base {
    Foo<Blah> doSomething(Baz stuff) override;
//  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ copy pasta
//                            ^^^^^ unused var name that may not match the base
//                                  name, causing confusion and extra work
//                                  refactoring
}

// derived.cpp
Foo<Blah> Derived::doSomething(Baz target) { .. }
//^^^^^^^          ^^^^^^^^^^^^^^^ copy pasta
//        ^^^^^^^^^ but signature has a different structural format than above
//                                 ^^^^^^ variable name can again be different
```

Spork removes header files and separate declaration/definition to improve this:

```spork
type Hello struct {}

fn hello(h:Hello -> age:i32 -> range:i32 -> i32) {
         ^^^^^^^- putting this argument in this position
                  without opting out means this belongs to
                  this struct
}
```
