
# Design of a new programming language and development environment: why, what, and how

# My Backstory

tl;dr - I've been tinkering with computers and writing software for a while. I put in my 10,000 hours several times over, and have developed some thoughts and opinions over time.

<strong><em>You should probably skip this entire section and get on to the good stuff in [First I must sprinkle you with fairy dust](#first-i-must-sprinkle-you-with-fairy-dust
) or [Desired Properties](#desired-properties) below.</em></strong>

In elementary school I found a book on programming in BASIC in the school library and wrote programs on paper but didn't have a computer to run them on. When I did get my hands on a PC running MS-DOS 5 I got access to QBasic, and learned by playing with modifying the included Gorilla and Nibbles games.

My middle school required everyone to take a semester in computer literacy which included typing practice and using a spreadsheet. This was on an Apple II and may have been VisiCalc. In the optional second semester advanced computer literacy course I picked up Hypercard.

In high school I took the computer science course, which used Turbo Pascal 7 for DOS. This was the summit of what the high school offered but the school district was happy to pay for any courses I wished to take at the community college, so I took courses in C, C++, and Visual Basic. I also later took the basic programming course at the high school due to strange circumstances - normally a prerequisite to the Pascal course I had already taken. I ignored the curriculum and instead pulled in a couple of assembly routines and made a mouse driven paint program. As a senior I took computer applications independent study and get Linux running on Power Macintosh G3 - this was non-trivial at the time.

At university, I briefly touched MATLAB and Mathematica, and took courses in data structures and assembly programming. I also founded a Linux Users Group and made my own Linux distribution. The Malcom Linux Distribution was a full rebuild of Mandrake Linux with different compiler optimizations, and additional graphical configuration tools. The name was based on the Jurassic Park quote about shoulders of giants but the login screen featured a picture of Malcolm X, since it was the Malcolm Linux X Window System, or Malcolm X Window System.

When college didn't work out for me, I worked in Linux and Unix system administration at four or five companies, and then transitioned into freelance programing. I learned PHP, JavaScript, MySQL, C#, Ruby, and Python. When I returned to academia and worked in the Eagleman Neuroscience Lab I picked up Java to work on a mobile app and learned R for data analysis, including particularly ggplot2. I taught a for-credit summer course in R programming for some Rice University students.

Eventually, when looking for more clients, I made it through a very lengthy and rigorous process to become identified as a Top 3% developer and member of the Toptal marketplace. I clocked about 4000 billable hours of work through Toptal in addition to my work with my direct clients.

In 2022, dissatisfied with the state of my tools, I jumped into a very deep dive exploring the landscape, history, and status of programming languages, programming language design, and software development environments, methods, and tools.

In this document I am assembling some notes about this research and collecting the results of that deep dive into something more of a coherent vision.

Coming out of this, the hope and expectation is to finally make some good software.

![good-software.jpg](good-software.jpg)

# First I must sprinkle you with fairy dust

Your current programming language and development tools are much worse than you think.

These languages are obsolete: JavaScript, C#, Go, Java, Objective-C, MATLAB, and C++02.

These are better for now, but are still oh so very far from what programming should be: Kotlin, Swift, Rust, Scala, ReScript, TypeScript, Elixir, Clojure, F#, Elm, Julia, and C++14. Python almost makes the obsolete list but there's no suitable alternative for it yet.

If you have any doubt at all about the above statements, here's a pile of YouTube videos to get you started down the path. I've included them very roughly descending in order of importance and relevance for our efforts. Minimally you should watch at least the first four or five of them.

["Stop Writing Dead Programs" by Jack Rusher at Strange Loop 2022](https://youtu.be/8Ab3ArE8W3s)

["Inventing on Principle" by Bret Victor](https://youtu.be/PUv66718DII)

["Building a Toolkit for Design" by Matthew Griffith](https://youtu.be/Ie-gqwSHQr0)

["Making Impossible States Impossible" by Richard Feldman](https://youtu.be/IcgmSRJHu_8)

["Functional Programming for Pragmatists" by Richard Feldman at GOTO 2021](https://youtu.be/3n17wHe5wEw)

["The computer revolution hasn't happened yet" by Alan Kay at OOPSLA 1997](https://youtu.be/oKg1hTOQXoY)

[Yesterday's Computer of Tomorrow: The Xerox Alto Smalltalk-76 Demo](https://youtu.be/NqKyHEJe9_w)

["Simple Made Easy" by Rich Hickey (2011)](https://youtu.be/SxdOUGdseq4)

["The Mess We're In" by Joe Armstrong at Strange Loop 2014](https://youtu.be/lKXe3HUG2l4)

["Systems that run forever self-heal and scale" by Joe Armstrong (2013)](https://youtu.be/cNICGEwmXLU)

["We Really Don't Know How to Compute!" - Gerald Sussman (2011)](https://youtu.be/HB5TrK7A4pI)

["Programming’s Greatest Mistakes - Mark Rendle - NDC Copenhagen 2022"](https://youtu.be/qC_ioJQpv4E)

["Object-Oriented Programming is Bad" by Brian Will](https://youtu.be/QM1iUe6IofM)

["Object-Oriented Programming is Embarrassing: 4 Short Examples" by Brian Will](https://youtu.be/IRTfhkiAqPw)

["Object-Oriented Programming is Good*" by Brian Will](https://youtu.be/0iyB0_qPvWk)

["Clean Coders Hate What Happens to Your Code When You Use These Enterprise Programming Tricks" by Kevlin Henney](https://youtu.be/FyCYva9DhsI)

# Desired properties

A good software development system should be <strong>moldable, transparent, live, distributed, versatile, and rich</strong>.

## Moldable

For some examples of moldable environments, see Smalltalk, TiddlyWiki, and Lisp machines. Within Smalltalk, the Glamorous Toolkit and TruffleSqueak are of particular note, but also old demo videos.

- Integrated

## Transparent

- High-level
- Literate
- Introspectable
- Multi-Representational
- Reproducible

## Live

## Distributed

## Versatile

## Rich

# Second Level Principles

## Small

## Opinionated

## Batteries Included

## Greedy

# Design Decisions

## Not IN the ocean, INSIDE the ocean

The compiler, debugger, and editor are not separate programs. They are facilities inside the program.

## Cached compilation

## Message passing

## Syntax

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

Every character in the syntax must be visually distinct. Tabs are not source code. Elm got this right. Instead of allowing tabs, make sure the tools are set up to present the code with different amounts of spaces.

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

## State Charts

https://statecharts.dev/

## Iced GUI

https://github.com/iced-rs/iced

https://book.iced.rs/

https://docs.rs/iced/latest/iced/widget/index.html

## Excel

We can implement most of the Excel functions in a package. See [PhpSpreadsheet](https://github.com/PHPOffice/PhpSpreadsheet), for example. This will be a hit.

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

Some more thought may be needed as far as special support for things like trees.

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

# Images

Use GraphicsMagick


# Inspiration

### Stop Writing Dead Programs

https://youtu.be/8Ab3ArE8W3s?si=YPX7LP5TJfoNLdbl

### Some great quotes

"Any sufficiently complicated C or Fortran program contains an ad hoc, informally-specified, bug-ridden, slow implementation of half of Common Lisp." - Greenspun's tenth rule

https://levelup.gitconnected.com/top-45-programming-quotes-24ebad417241

"You cannot manage that which you cannot measure" - James Barksdale, CEO of Netscape 

"Measuring programming progress by lines of code is like measuring aircraft building progress by weight" - Bill Gates 

"Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it" - Brian Kernighan 

"If debugging is the process of removing software bugs, then programming must be the process of putting them in" - Edsger W. Dijkstra 

"Hiring bad developers is like drinking seawater. It seems to satisfy a need while actually increasing it" - Michael T. Nygard 

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

### Cranelift

https://cranelift.dev/

rustc_codegen_cranelift https://github.com/bjorn3/rustc_codegen_cranelift

### Linker

https://github.com/rui314/mold

## Parser generator

### nom

https://github.com/rust-bakery/nom

https://tfpk.github.io/nominomicon/

# Educational Resources

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


# Gui


## Window management

https://github.com/jordansissel/xdotool

## Browser

### Tauri

https://tauri.app/

# Licensing Strategy

MIT / Apache dual for some components

Maybe some triple AGPL/BSL/Proprietary that time bombs to MIT / Apache dual

