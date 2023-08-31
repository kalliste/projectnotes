
# Introduction

tl;dr - I've been tinkering with computers and writing software for a while. I put in my 10,000 hours several times over, and have developed some thoughts and opinions over time.

<strong><em>You should probably skip this entire section and get on to the good stuff in [Desired Properties](#desired-properties) below.</em></strong>

In elementary school I found a book on programming in BASIC in the school library and wrote programs on paper but didn't have a computer to run them on. When I did get my hands on a PC running MS-DOS 5 I got access to QBasic, and learned by playing with modifying the included Gorilla and Nibbles games.

My middle school required everyone to take a semester in computer literacy which included typing practice and using a spreadsheet. This was on an Apple II and may have been VisiCalc. In the optional second semester advanced computer literacy course I picked up Hypercard.

In high school I took the computer science course, which used Turbo Pascal 7 for DOS. This was the summit of what the high school offered but the school district was happy to pay for any courses I wished to take at the community college, so I took courses in C, C++, and Visual Basic. I also later took the basic programming course at the high school due to strange circumstances - normally a prerequisite to the Pascal course I had already taken. I ignored the curriculum and instead pulled in a couple of assembly routines and made a mouse driven paint program. As a senior I took computer applications independent study and get Linux running on Power Macintosh G3 - this was non-trivial at the time.

In college I briefly touched MATLAB and Mathemtica, and took courses in data structures and assembly programming. I also founded a Linux Users Group and made my own Linux distribution. The Malcom Linux Distribution was a full rebuild of Mandrake Linux with different compiler optimizations, and additional graphical configuration tools. The name was based on the Jurassic Park quote about shoulders of giants but the login screen featured a picture of Malcolm X, since it was the Malcolm Linux X Window System, or Malcolm X Window System.

When college didn't work out for me, I worked in Linux and Unix system administration at four or five companies, and then transitioned into freelance programing. I learned PHP, JavaScript, MySQL, C#, Ruby, and Python. When I returned to academia and worked in the Eagleman Neuroscience Lab I picked up Java to work on a mobile app and learned R for data analysis, including particularly ggplot2. I taught a for-credit summer course in R programming for some Rice University students.

Eventually, when looking for more clients, I made it through a very lengthy and rigorous process to become identified as a Top 3% developer and member of the Toptal marketplace. I clocked about 4000 billable hours of work through Toptal in addition to my work with my direct clients.

In 2022, dissatisfied with the state of my tools, I jumped into a very deep dive exploring the landscape, history, and status of programming languages and software development environments.

In this document I am assembling some notes about this research and collecting the results of that research deep dive into something more of a coherent vision.

Coming out of this, the hope and expectation is to finally make some good software.

![good-software.jpg](good-software.jpg)

# Desired properties

A good software system should be <strong>moldable, transparent, live, distributed, versatile, and rich</strong>.

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

# Inspiration

### Stop Writing Dead Programs

https://youtu.be/8Ab3ArE8W3s?si=YPX7LP5TJfoNLdbl

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

### Gleam

https://gleam.run/

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

## Data structures

https://docs.rs/imbl/latest/imbl/


# Educational Resources

https://nnethercote.github.io/

https://en.wikipedia.org/wiki/Meta-circular_evaluator

https://en.wikipedia.org/wiki/Type_system#Existential_types


### Rust compile time

https://blog.kodewerx.org/2020/06/the-rust-compiler-isnt-slow-we-are.html

https://endler.dev/2020/rust-compile-times/


# Data formats

### Arrow

https://duckdb.org/2021/12/03/duck-arrow.html

https://docs.rs/crate/arrow/latest

https://github.com/pola-rs/polars

### Cap'N Proto

https://capnproto.org/

## Actor model 

https://www.bastion-rs.com/


# Gui

## iced

https://github.com/iced-rs/iced

https://book.iced.rs/

https://docs.rs/iced/latest/iced/widget/index.html

## Video

https://github.com/jazzfool/iced_video_player

https://www.reddit.com/r/rust/comments/yyoy31/rust_gui_library_for_video_playback/

https://github.com/yuv418/mpv-egui

https://github.com/mpv-player/mpv-examples/tree/master/libmpv

https://github.com/mpv-player/mpv-examples/blob/master/libmpv/sdl/main.c

https://docs.rs/libmpv/2.0.1/libmpv/

## Window management

https://github.com/jordansissel/xdotool

## Browser

### Tauri

https://tauri.app/

