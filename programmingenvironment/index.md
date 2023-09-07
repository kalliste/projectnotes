
# Design of a new programming language and development environment: why, what, and how

# Background

> There is in Melbourne a man who probably knows more about poisonous snakes than anyone else on earth. his name is Dr Struan Sutherland, and he has devoted his entire life to a study of venom.
> 
> ‘And I’m bored with it,’ he said when we went along to see him the next morning. ‘Can’t stand all these poisonous creatures, all these snakes and insects and fish and things. Stupid things, biting everybody. And then people expect me to tell them what to do about it. I’ll tell them what to do. Don’t get bitten in the first place. That’s the answer. I’ve had enough of it. Hydroponics, now, that’s interesting. Talk to you all you like about hydroponics. Fascinating stuff, growing plants artificially in water, very interesting technique. We’ll need to know all about it if we’re go to Mars and places. Where did you say you were going?’
> 
> ‘Komodo’
> 
> ‘Well, don’t get bitten, that’s all I can say. And don’t come running to me if you do because you won’t get here in time and anyway I’ll probably be out. Hate this office, look at it. Full of poisonous animals all over the place. Look at this tank, it’s full of fire ants. Poisonous. Bored silly with them. Anyway, I got some little cakes in in case you were hungry. Would you like some little cakes? I can’t remember where I put them. There’s some tea but it’s not very good. Sit down for heaven’s sake.
> 
> ‘So, you’re going to Komodo. Well, I don’t know why you want to do that, but I suppose you have your reasons. There are fifteen different types of snakes on Komodo, and half of them are poisonous. The only potentially deadly ones are the Russell’s viper, the bamboo viper, and the Indian cobra.
> 
> ‘The Indian cobra is the fifteenth deadliest snake in the world, and all the other fourteen are here in Australia. That’s why it’s so hard for me to find time to get on with my hydroponics, with all these snakes all over the place.
> 
> - Douglas Adams, "Last Chance To See"

There are so very many things we could do with good software tools, and the lack of them is holding us back.

Let us fix this and get back to doing those more exciting things. Like hydroponics.

Starting in 2022, dissatisfied with the state of my tools, I jumped into a very deep dive exploring the landscape, history, and status of programming languages, programming language design, and software development environments, methods, and tools.

In this document I am assembling some notes about this research and collecting the results of that deep dive into something more of a coherent vision.

Coming out of this, the hope and expectation is to finally make some good software.

![good-software.jpg](good-software.jpg)

# First I must sprinkle you with fairy dust

Your current programming language and development tools aren't as good as you think, and are very very far from what they could be.

If you don't already have a deep appreciation for this, 10 or 15 hours of videos should help get you caught up.

Start with "Stop Writing Dead Programs" and maybe watch it again after the others.

["Stop Writing Dead Programs" by Jack Rusher at Strange Loop 2022](https://youtu.be/8Ab3ArE8W3s)

Bret Victor's talks are important and inspiring. Unfortunately you can't just download the code for the demos he made and build on that.

["Inventing on Principle" by Bret Victor](https://youtu.be/PUv66718DII)

["Media for Thinking the Unthinkable" by Bret Victor](https://youtu.be/oUaOucZRlmE)

An introduction to Elm-UI will help show the value of the Elm architecture.

["Building a Toolkit for Design" by Matthew Griffith](https://youtu.be/Ie-gqwSHQr0)

["Making Impossible States Impossible" by Richard Feldman](https://youtu.be/IcgmSRJHu_8)

["The Edges of Cutting-Edge Languages" by Richard Feldman](https://youtu.be/cpQwtwVKAfU?si=iUJ0-xsQ_O3fs-qO)

["Functional Programming for Pragmatists" by Richard Feldman at GOTO 2021](https://youtu.be/3n17wHe5wEw)

Also key to have an appreciation of Smalltalk

["Moldable development" by Tudor Gîrba](https://youtu.be/Pot9GnHFOVU)

["What FP can learn from Smalltalk" by Aditya Siram at Lambda World 2018]()

["The computer revolution hasn't happened yet" by Alan Kay at OOPSLA 1997](https://youtu.be/oKg1hTOQXoY)

[Yesterday's Computer of Tomorrow: The Xerox Alto Smalltalk-76 Demo](https://youtu.be/NqKyHEJe9_w)

Rich Hickey's talks about Clojure and related ideas are very valuable

["Simple Made Easy" by Rich Hickey (2011)](https://youtu.be/SxdOUGdseq4)

Here's a start on stuff relating to Erlang

["The Mess We're In" by Joe Armstrong at Strange Loop 2014](https://youtu.be/lKXe3HUG2l4)

["Systems that run forever self-heal and scale" by Joe Armstrong (2013)](https://youtu.be/cNICGEwmXLU)

["Erlang - software for a concurrent world" by Joe Armstrong](https://www.infoq.com/presentations/erlang-software-for-a-concurrent-world/)

[Joe Armstrong's various talks](https://www.youtube.com/playlist?list=PLvL2NEhYV4ZsIjT55t-kxylCU0BRlQjpl)

This is the Sussman talk referenced in "Stop writing dead programs" where he talks about propagators, among other things.

["We Really Don't Know How to Compute!" - Gerald Sussman (2011)](https://youtu.be/HB5TrK7A4pI)

["Programming’s Greatest Mistakes - Mark Rendle - NDC Copenhagen 2022"](https://youtu.be/qC_ioJQpv4E)

Let Brian Will talk shit about the dominant paradigm to you for a minute, it's good for you.

["Object-Oriented Programming is Bad" by Brian Will](https://youtu.be/QM1iUe6IofM)

["Object-Oriented Programming is Embarrassing: 4 Short Examples" by Brian Will](https://youtu.be/IRTfhkiAqPw)

["Object-Oriented Programming is Good*" by Brian Will](https://youtu.be/0iyB0_qPvWk)

["Clean Coders Hate What Happens to Your Code When You Use These Enterprise Programming Tricks" by Kevlin Henney](https://youtu.be/FyCYva9DhsI)


# What it looks like

We build our system in a few layers.

The base layer is mostly Rust and as much as possible, it's existing libraries. At this layer we would like things like fast parsing and direct access to some native libraries. One central piece though is a good actor framework that lets us message across processes that may be on separate machines but which operates more efficiently if they happen to have access to "shared memory".

The next layer is Roc. Our Roc platform provides functional/immutable interface ports to the underlying facilities in Rust. Inside the Roc environment we are no longer thinking about a lot of the implementation details that are handled by the platform code in Rust. Concerns about things like threads, locks, memory management, and memory layout are isolated to the layer below until we find specific use cases for exposing them.

It would be nice if our Roc platform could do incremental compilation and hot reload of Roc code. If not, we build that in the layer down and expose it for the layer up. Or else, if we can efficiently call natively compiled Roc from WASM Roc, and we can hot load WASM, then we don't need to make our own bytecode.

At the next (top?) layer we have a language that isn't exactly the Gleam programming language, but it resembles Gleam. It's Gleam-ish. Gleamish is a small, easy, statically typed, functional programming language with a familiar curly brackets syntax. Gleamish is multi-representational. You don't even have to see the code when you don't want to - we represent it visually for tasks where you want to get the big picture quickly.

Gleamish is just functions. We don't write complicated state machines in Gleamish text syntax - we build state charts. We have charts for other things as well. Each domain we are working with is represented declaratively in text and has a visual representation. Where possible, either can be used. Neither the text nor the visual interface are the definitive copy. They are both rendered from a data structure that can represent them both, and changes are brokered between them in Elm Model-View-Update fashion.

### Why these layers?

It can be great to have everything in one language.

We want some things to be fast and efficient and we want access to native libraries. We need a systems programming language, and today that's Rust.

Rust wants to be a systems programming language. We can abstract details away with macros, but it would be painful to build and maintain a full system of macros that takes us where we are going.

Roc is closer. It's an applications programming language with niceties like automatic memory management. But the Roc compiler is focused on turning Roc into efficient native code artifacts. That's not our top goal with Gleamish. Gleamish is about live molding. We need it to run fairly fast sometimes, but it's not the most important concern, and sometimes we want to run our Gleamish code slow. We want to get all up in its business and watch it work. You can step through any language's execution in a debugger but that's not the primary use case for most languages. Our Gleamish tools will be focused on things like molding, debugging, tracing, and visualizing the activity and behavior of the system we are building. These concerns aren't necessarily in complete opposition to the things Roc cares about, but we can benefit from separating them. It also means our Gleamish syntax can be what we want it to be to fit our other goals and needs.

# The Big Plan

- Implement Roc platform(s)
- Web/Desktop/Mobile GUI via Iced
- Actors
- Layer on a braces syntax ala Gleam
- Unison-lang style normalization
- Hot reload / live programming

### Libraries

- Polars data frames and data access
- Datatables UI component
- New implementation of Grammar of Graphics on Iced
- Logical diagrams support
- Images and video via GraphicsMagick and libmpv
- Web communications / web scraping

### Scaffolding

- Wrattler notebooks. Maybe.

### Bonus rounds

- Target and integrate BEAM/OTP
- xdotool window management integration
- Additional syntax with Hylo style mutable value semantics support
- Shaders
- O3DE or Godot. OpenXR.
- More Crypto
- LLM comment sync maintenance
- Voice interface

# Desired properties

A good software development system should be <strong>moldable, transparent, live, distributed, versatile, and rich</strong>.

## Moldable

For some examples of moldable environments, see Smalltalk, TiddlyWiki, and Lisp machines. Within Smalltalk, the Glamorous Toolkit and TruffleSqueak are of particular note, but also old demo videos.

- Integrated

## Transparent

### High-level
### Layered
### Literate
### Introspectable
### Multi-Representational

Broke: textual, no-code
Woke: low-code
Bespoke: multi-representational

### Reproducible

## Live

## Distributed

## Versatile

## Rich

# Second Level Principles

## Provenance

## Distributed Tracing

## Interoperation

Interested in Rust ecosystem first. Erlang ecosystem for scaling. Python and R for libraries. Maybe we can link through to Python, R, and Julia via Jupyter interface

## Small

## Opinionated

## Batteries Included

## Greedy

## For Pirates

# Design Decisions

## Not IN the ocean, INSIDE the ocean

The compiler, debugger, and editor are not separate programs. They are facilities inside the program.

## Cached compilation

## Message passing

## Syntax

Worth looking at Raku for good ideas:

["Raku syntax I miss in other languages" by Leon Timmermans](https://www.youtube.com/live/elalwvfmYgk)

But pick carefully! Don't be Perl

### Do use curly brackets

There's a few main options here.

- Use an 'end' keyword, and possibly a 'begin' keyword as seen in Algol, Pascal, Ruby, Elixir, Basic, Fortran, MATLAB, Julia.

- Significant whitespace. Seen in Python and Nim.

- What they do in the ML family. TODO: describe this further.

- Round brackets for everything! Lisp family, including Common Lisp, Scheme, Clojure, etc.

- Curly brackets. C, C++, C#, Java, JavaScript, Swift, Kotlin, Go, PHP, Perl, Rust.

There is a case to be made for each of the other options, but curly brackets are the most popular and familiar, and enhance quick reading and touch editing.

### No semicolons at the end of lines

JavaScript tries and fails. JavaScript isn't semicolon-free, it has automatic semicolon insertion, which is not the same and doesn't save you from semicolon problems. Kotlin gets it right. Swift and Go also appear fine. This is the way.

### Tabs are invalid

Every character in the syntax must be visually distinct. Tabs are not source code. Elm got this right. Instead of allowing tabs, make sure the tools are set up to present the code with different amounts of spaces. We can live without 1 character is 1 byte, but should be able to 

### Comparison and assignment

No ===. Probably use := for assignment and == for comparison since = is ambiguous. Maybe not necessary if we can otherwise work out the syntax where = and == would never be valid in the same place.

### let, var, const, mut, (nothing)

Oof, this is a mess. C uses nothing where we would have let in JavaScript - the default thing for something to be is a variable. Terse but being able to regex search for assignments can be nice. Using := gets us that.

This is a difference from Gleam.

### Type name placement

After the variable, not before. This is different from C but makes more sense in a language that supports type inference and is consistent with Kotlin, Swift, Rust, Scala, Go.

### Ternary operator

Love using it, but it's too arcane. Leave it out for now. Watch closely about left-associating or right-associating if ever included.

### Increment and decrement operators

Nicely terse but we can be less arcane by not having them. They are for mutation so not valid in a pure functional language anyway.

### Regular expressions

Here comes some theory!

So.... it seems like we can
- Convert a simple string equality comparison to a regex
- Convert a string subset comparison to a regex
- Take two regexes and easily produce a third which matches the set of things that match either
- (and with a possibly heavy but known algorithm) compare two regular expressions for equality

So seems like it would be possible to prove exhaustiveness in an automated way for pattern matching with regex comparisons.

It's just crazy enough - maybe let's go for it. Then we can make a funny GAAS (Grep As A Service) API some day that does this wizardry.

So go ahead and have regex literals, =~ string-regex comparison, regex-regex == operator, pattern matches on regex.

### Operator overloading

Maybe.... maybe.

### Pipe operator

Like in the ML family and Gleam. Gotta have it. Definitely worth this judicious addition not seen in that other language you know already.

### Comments

Probably do what C++, Swift, Kotlin, C#, Java, PHP and JavaScript do here. /* */ and //

The '#' character for comments mainly makes sense for interpreted languages running on Unix. Maybe allow it - PHP does. Maybe only allowed at the beginning of the file so we can still support interpreted scripts but discourage it as a practice.

This is a difference from Gleam.

### Template literals

This might make sense as an opt-in thing. Pattern matching is better. TODO: further investigation needed.

### Attributes / Annotations

Human and machine readable metadata on functions and modules is very useful. This will affect some other design decisions in different places, so care will be needed. The Kotlin syntax looks like Python decorators which are kind of evil. The C# syntax is ok but looks like a list literal. The PHP syntax might be the way to go.

This is a difference from Gleam.

### if, elif, else if, else

None of this! Only pattern matching. Like Gleam. https://gleam.run/book/tour/case-expressions.html

### for, foreach, do, while, until

Nope. We do this the FP way.

## Types

### Type Inference

### Algebraic Data Types

### Pattern Matching

## Mutation and Side Effects

### Core language is pure functional / datastructures are immutable

Core libraries should mostly be purely functional also. State is in specific libraries focused on managing state.

Later we can look at building data structure libraries that take the approach of Hylo or Swift

### Maybe we don't have to be fascists about side effects all the time

We're not trying to make a Haskell here. Lisp, Erlang, and even OCaml let you just print things

# Some prior languages and runtimes

https://www.boringcactus.com/2020/09/16/survey-of-rust-embeddable-scripting-languages.html

https://github.com/evcxr/evcxr

https://github.com/rust-lang/miri

https://www.reddit.com/r/rust/comments/n2cmvd/there_are_a_lot_of_actor_framework_projects_on/

### LiveCode

A modern cross-platform HyperCard

https://en.wikipedia.org/wiki/LiveCode_(company)

### Light Table

http://lighttable.com/

Shows debug out with the code that made it

### Rust excvr

The evcxr rust evaluation context has a REPL and Jupyter kernel for Rust

How does it work?

Analyze your code looking for variable declarations. Put a wrapper around it and write it (to disk!) as a dll. Load the dll and call the function, marshalling data in and out via a HashMap.

https://github.com/evcxr/evcxr/blob/main/evcxr/HOW_IT_WORKS.md


### Erlang BEAM / OTP

https://news.ycombinator.com/item?id=9907310

### MiniVM / Paka

https://github.com/FastVM/paka

https://github.com/FastVM/minivm

### passerine

https://github.com/vrtbl/passerine

Q: What is vaporization memory management?

A: When I was first designing Passerine, I was big into automatic compile-time memory management. Currently, there are a few ways to do this: from Rust's borrow-checker, to µ-Mitten's Proust ASAP, to Koka's Perceus, there are a lot of new and exciting ways to approach this problem.

Vaporization is an automatic memory management system that allows for Functional but in Place style programming. For vaporization to work, three invariants must hold:

1. All functions params are passed by value via a copy-on-write reference. This means that only the lifetimes of the returned objects need to be preserved, all others will be deleted when they go out of scope.
1. A form of SSA is performed, where the last usage of any value is not a copy of that value.
1. All closure references are immutable copies of a value. These copies may be reference-counted in an acyclical manner.

With these invariants in place, vaporization ensures two things:

1. Values are only alive where they are still useful.
1. Code may be written in a functional style, but all mutations occur in-place as per rule 2.


### gluon

https://github.com/gluon-lang/gluon

### Rhai

https://github.com/rhaiscript/rhai

Rhai is an embedded scripting language and evaluation engine for Rust that gives a safe and easy way to add scripting to any application.

Tight integration with native Rust functions and types, including getters/setters, methods and indexers.

For those who actually want their own language:
1. Use as a DSL.
1. Disable certain language features such as looping.
1. Further restrict the language by surgically disabling keywords and operators.
1. Define custom operators.
1. Extend the language with custom syntax.


All CPU and O/S targets supported by Rust, including:
1. WebAssembly (WASM)
1. no-std


### ellie

https://docs.ellie-lang.org/what_is_ellie.html

### dyon

https://github.com/pistondevelopers/dyon

### rune

https://github.com/rune-rs/rune

# Traps

### Hand coding

### Going full Terry

### All the performance

### Error messages

### Backwards compatibility

# Components and Pieces

## Rust basis

## Gleam syntax

https://gleam.run/

## Iced GUI

https://github.com/iced-rs/iced

https://book.iced.rs/

https://docs.rs/iced/latest/iced/widget/index.html

## Excel

We can implement most of the Excel functions in a package. See [PhpSpreadsheet](https://github.com/PHPOffice/PhpSpreadsheet), for example. This will be a hit.

https://github.com/jiradaherbst/xlformula-engine

## Diagrams

Need to render different kind of diagrams, and it should not look primitive

Maybe follow UML data model?

https://mermaid.js.org

https://statecharts.dev/

https://kroki.io/

http://mermaid.js.org/intro/

Need to research automatic layout -- probably this is <em>hard</em>

## Data types

The most basic are strings, ints, floats, and bools. Some languages get away without a bool type but lets not.

For ints we should probably go with auto-bigint. Default to 64-bit int internally and switch to bigint on overflow. Floats probably prefer float64 and maybe don't auto-bigfloat. Support interop with the various fixed size int and float types.

We should specifically support representing +/- Inf, +/- 0, and NaN in the type system. Maybe no need to think about subnormals.

Rational numbers would be nice but maybe not an early requirement.

Important to have product types and sum types. Gleam calls product types "custom types" and calls sum types "multiple constructors" https://gleam.run/book/tour/custom-types.html

Lists and Maps are important. Probably also include Sets. Maps should be ordered maps but we also need to think about handling unordered map input for interop. Maybe just sort them.

For extra bonus points - maybe the internal representation for Cap'N Proto can be used for things - Gleam appears to do this already.

Need to look at the Gleam implementation, the imbl Rust crate, and the Roc, Swift, and Hylo implementations.

Also would be key to have a good JSON type - needs to map to 

Someone had a video, article, library about method naming conventions for collections - worth checking.

graphs https://docs.rs/petgraph/latest/petgraph/

We do need data frames.

## Polars data frames

Use Polars for data frames

https://github.com/pola-rs/polars

https://github.com/sfu-db/connector-x

https://arrow.apache.org/docs/format/ADBC.html

https://duckdb.org/2021/12/03/duck-arrow.html

https://docs.rs/crate/arrow/latest

## OS Interface

We may not need a specific thing here, but keep rktio in mind https://github.com/racket/racket/tree/master/racket/src/rktio

## Crypto and network

dryoc
https://github.com/brndnmtthws/dryoc

s2n-tls
https://github.com/aws/s2n-tls

reqwest
https://crates.io/crates/reqwest

Tokio
https://tokio.rs/

## Video

A video player widget for Iced based on libmpv

https://github.com/jazzfool/iced_video_player

https://www.reddit.com/r/rust/comments/yyoy31/rust_gui_library_for_video_playback/

https://github.com/yuv418/mpv-egui

https://github.com/mpv-player/mpv-examples/tree/master/libmpv

https://github.com/mpv-player/mpv-examples/blob/master/libmpv/sdl/main.c

https://docs.rs/libmpv/2.0.1/libmpv/

## Images

Use GraphicsMagick

# Code Katas for prep



# Inspiration

### Some great quotes

"Any sufficiently complicated C or Fortran program contains an ad hoc, informally-specified, bug-ridden, slow implementation of half of Common Lisp." - Greenspun's tenth rule

"All non-trivial abstractions, to some degree, are leaky." - Joel Sapolsky's Law of Leaky Abstractions

"Regarding the fact that I regret adding threads to the language because they are too difficult to use correctly, I don't want to add yet another variation of threads." - Yukihiro Matsumoto

"Simple things should be simple, complex things should be possible." - Alan Kay

> Perl is a language for getting your job done.
> 
> Of course, if your job is programming, you can get your job done with any "complete" computer language, theoretically speaking. But we know from experience that computer languages differ not so much in what they make possible, but in what they make easy. At one extreme, the so-called "fourth generation languages" make it easy to do some things, but nearly impossible to do other things. At the other extreme, certain well known, "industrial-strength" languages make it equally difficult to do almost everything.
> 
> Perl is different. In a nutshell, Perl is designed to make the easy jobs easy, without making the hard jobs impossible.
> 
> Programming Perl, 2nd Edition (1996), by Larry Wall, Tom Christiansen and Randal Schwartz


Below quotes found at https://levelup.gitconnected.com/top-45-programming-quotes-24ebad417241

"You cannot manage that which you cannot measure" - James Barksdale, CEO of Netscape 

"Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it" - Brian Kernighan 

"If debugging is the process of removing software bugs, then programming must be the process of putting them in" - Edsger W. Dijkstra 

"Hiring bad developers is like drinking seawater. It seems to satisfy a need while actually increasing it" - Michael T. Nygard 

"Measuring programming progress by lines of code is like measuring aircraft building progress by weight" - Bill Gates

"It should be noted that no ethically-trained software engineer would ever consent to write a DestroyBaghdad procedure. Basic professional ethics would instead require him to write a DestroyCity procedure, to which Baghdad could be given as a parameter" - Nathaniel S. Borenstein 

"Any code of your own that you haven’t looked at for six or more months might as well have been written by someone else" - Eagleson, also known as Eagleson’s Law 

"I hate code, and I want as little of it as possible in our product" - Jack Diederich

### TruffleSqeak

https://github.com/hpi-swa/trufflesqueak

### Glamorous Toolkit

https://gtoolkit.com/

### 4GL

https://en.wikipedia.org/wiki/Fourth-generation_programming_language

### Enso

https://enso.org/

https://github.com/enso-org/enso

### Hylo

https://www.hylo-lang.org/

### Lotus Improv

https://en.wikipedia.org/wiki/Lotus_Improv

### Unison Lang

https://www.unison-lang.org/

## Bootstrapping a platform

### mal

https://github.com/kanaka/mal

### Build Your Own Lisp

https://buildyourownlisp.com/

### stage0

https://github.com/oriansj/stage0

### A Simple Scheme Compiler

https://web.archive.org/web/20160325113733/http://icem-www.folkwang-hochschule.de/~finnendahl/cm_kurse/doc/schintro/schintro_142.html

### Zick Standard Lisp

https://github.com/zick/ZickStandardLisp


### NeXTSTEP Release 3

https://youtu.be/rf5o5liZxnA?si=f2Nwukqnz2nZtpyq

## AI

https://en.wikipedia.org/wiki/Frame_(artificial_intelligence)#Frame_language


# Development Tools

### IntelliJ Community

https://github.com/JetBrains/intellij-community

### VSCodium

https://vscodium.com/

### Theia

https://theia-ide.org/

# Backend

## Memory Management / Performance Techniques from related projects

A unified theory of garbage collection
https://courses.cs.washington.edu/courses/cse590p/05au/p50-bacon.pdf

https://manishearth.github.io/blog/2021/04/05/a-tour-of-safe-tracing-gc-designs-in-rust/

https://crates.io/crates/crossbeam-epoch

https://github.com/asajeffrey/josephine

### gc-arena

https://crates.io/crates/gc-arena

### bumpalo

https://crates.io/crates/bumpalo

### Morphic and Perseus

https://morphic-lang.org/

https://www.youtube.com/live/F3z39M0gdJU

https://youtu.be/ZnYa99QoznE

https://www.reddit.com/r/haskell/comments/qc4bxd/outperforming_imperative_with_pure_functional/

https://www.microsoft.com/en-us/research/publication/perceus-garbage-free-reference-counting-with-reuse/

### Cranelift

https://cranelift.dev/

rustc_codegen_cranelift https://github.com/bjorn3/rustc_codegen_cranelift

https://github.com/CraneStation/kaleidoscope-cranelift

https://github.com/In-line/calculator

"For end-user every mathematical expression is evaluated simultaneously by the interpreter and JIT compiler. JIT and Interpretator are racing to compute value first."

https://github.com/0xekez/lisp

https://web.archive.org/web/20220429160434/https://zmedley.com/lust/

https://github.com/lechatthecat/marie

https://github.com/mental32/monty

https://github.com/chc4/lineiform

https://blog.redvice.org/2022/lineiform-rust-meta-jit/

"Lineiform is a meta-JIT library for Rust interpreters. Given an interpreter that uses closure generation, it allows an author to add minimal annotations and calls into Lineiform in order to automagically get an optimizing method JIT, via runtime function inlining and constant propagation to resolve dynamic calls to closed environment members. Internally it does lifting of closure bodies from x86 assembly to Cranelift IR for dynamic recompilation."

https://github.com/billsjchw/tigerc-rs

https://github.com/JohnDowson/CraneLisp

https://github.com/swerdloj/jitter

Jitter is a just-in-time (JIT) compiled scripting language designed with Rust interoperability in mind.

Jitter evolved into a research project investigating the ideas of:
- Treating a compiler as a library
- Language extension mechanisms

All data types in Jitter align with Rust's #[repr(C)]. Because all primitive types also align with Rust's, interop is simple.

https://github.com/playXE/sierra-scheme

https://github.com/CrockAgile/wasmcrime

https://github.com/bayedieng/blox

https://github.com/birktj/dyncall-rs

https://github.com/bytecodealliance/cranelift-jit-demo

https://github.com/capy-language/capy


### Redmagic

https://github.com/matthewfl/redmagic

### Linker

https://github.com/rui314/mold

## Parser generator

### nom

https://github.com/rust-bakery/nom

https://tfpk.github.io/nominomicon/

# Educational Resources

https://book.marwood.io/

https://craftinginterpreters.com/

https://graydon2.dreamwidth.org/307291.html

https://nnethercote.github.io/

https://en.wikipedia.org/wiki/Meta-circular_evaluator

https://en.wikipedia.org/wiki/Type_system#Existential_types


### Rust compile time

https://blog.kodewerx.org/2020/06/the-rust-compiler-isnt-slow-we-are.html

https://endler.dev/2020/rust-compile-times/


# Serialization

### Cap'N Proto

https://capnproto.org/

## Actor model 

https://www.bastion-rs.com/

https://docs.rs/agnostik/latest/agnostik/


# Gui


## Window management

https://github.com/jordansissel/xdotool

## Browser

### Tauri

https://tauri.app/

# Licensing Strategy

MIT / Apache dual for some components

Maybe some triple AGPL/BSL/Proprietary that time bombs to MIT / Apache dual

