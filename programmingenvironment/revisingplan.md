
## A new software system

I am in the process of rethinking and redesigning the realm of software systems and how they are built. This started out as an effort to look into the features of modern programming languages to know how to choose among them better and use them well. It then turned into an exploration into designing a new programming language when I found many important amazing innovations, but no single place where they were embodied together as much as they could or should be. From there I found that this plan too was narrower than would be appropriate to contain enough of what I had found and learned. So now I find myself designing not just a new piece of software, but a new software system, within which a new programming language would be only one part of the picture.

Most of all this is a picture about an embodiment, in the form of a software system, of a principle articulated by Bret Victor as "immediacy", and the various corollaries I have found that follow from it. This has broad implications deep into every part of the system, but there are some other important principles and concerns that play in as well. Together these things can make building and changing software faster, more accessible, and more fun, and can help us make more reliable systems and tackle new problems.

A first taste of where I am coming from with all of this can be had from a few key videos:

["Stop Writing Dead Programs" by Jack Rusher at Strange Loop 2022](https://youtu.be/8Ab3ArE8W3s)

["Inventing on Principle" by Bret Victor](https://youtu.be/PUv66718DII)

["Ted Nelson Demonstrates XanaduSpace"](https://youtu.be/1yLNGUeHapA)

["We Really Don't Know How to Compute!" - Gerald Sussman (2011)](https://youtu.be/HB5TrK7A4pI)

If you watch those and still want more, you might just watch a random seven hours of Alan Kay talking - I don't know which specific video of him to curate for this list yet.

This is all quite an ambitious and broad endeavor, but one which I believe may hold great value. Currently I am looking into ways in which it might be approached incrementally. Through pursuing it incrementally, we can aim to make it such that each step in the process brings value to other efforts and goals as well as the future tasks of the project itself. Also, it will likely be best that no step calls for any unduly large investment of time energy before being connected to real world use where the underlying hypotheses can be tested and validated, and where some of the resulting value can be captured to return energy back to this work.

I'd like to dig in some today on my thoughts and plans for this as they stand now, but first we have to talk about a video game from 1988.

## A classic video game

Mega Man 2, or ロックマン2 Dr.ワイリーの謎 as the Japanese called it, was an action platformer game for the Nintendo NES/Famicom system, and was the most successful game in the franchise until Mega Man 11 overtook it about 30 years later.

In the first game we learned that Mega Man (a humanoid robot initially named Rock) was to be an assistant to Doctor Thomas Light in his workshop. However Dr. Light's former University colleauge, Doctor Albert W. Wily had grown mad and turned his talents and Light's famed "Robot Masters" to the aim of world domination. Moved by a strong sense of justice, Rock volunteered to take up the fight, and adoped the moniker Mega Man. Mega Man's own capabilities at the start were rather modest, but his special ability to acquire the powers of the adversaries he defeats wins out in the end. Though successful, new challanges were to follow. Through it all Mega Man maintains his hope of a future world in which man and robot can live in peaceful harmony.

So it is that in the second game, Dr. Wily has returned to threaten the world again with his own new "Robot Masters": Metal Man, Air Man, Bubble Man, Quick Man, Crash Man, Flash Man, Heat Man, and Wood Man. As the player, you may choose to pursue them in any order. Each of them has their own particular challenges and obstacles which must be traversed to reach them, and wields a unique weapon that carries a special power to be won. For the best chance of success in reaching Dr. Wily, the player must choose wisely the order in which to pursue his adveraries, knowing that at each point he will carry with him only those powers which he has acquired already by then, and considering the treacherous nature of going up against a Robot Master and his guards poorly armed, but also knowing the advantages for future battles he will gain on defeating them.

In the case of the software system that is to be built, we shouldn't try to do it all together at once, and we may not be able to pick the optimal path, but we can at least aim to go after Wood Man only after defeating Metal Man first. There are a number of insights, advancements, principles, and technologies that we will expect to incorporate. We can begin the process of charting our course through them with looking first at self-sustainability.

## Self-Sustainability

In "Ascending the Ladder to Self-Sustainability" (Jakubovic 2022) the authors identify one of the major key design principles of our system, which they call self-sustainability: "Programming is usually based on an inconvenient separation between an implementation level and a user level. Self-sustaining systems expose their implementation at their user level so that they can be modified and improved from within." This will have impacts even through to users of software made in the system, including users that never look under the hood, but we can first talk about how for our task of building the system, it is essential.

> In the ordinary lifecycle of software, there is a hard separation between the product and its source. The product may be any end-user application such as a game, and is created from the source by a producer, which is a compiler or other similar tool built for programmers. In this arrangement, the product’s user has little ability to re-program it, beyond setting configuration parameters anticipated ahead of time. The user’s only option is to modify the source (if it is available) and use the producer to create a new version of the product. Curiously, this arrangement isn’t limited to end-user products but also applies to most programmer-oriented products! In the ordinary programming experience, tools like the compiler or editor are themselves products with a separate source and producer. If the user of a language wants to re-program it beyond a customisation anticipated by its designer, they have to go and modify the compiler source code. If they are lucky, the compiler is also written in the same language. In this case, the user is already familiar with the language’s notation and capabilities, making the task easier than learning an entirely new language. Even so, their changes still occur at a separate level from their ordinary use of the system. In this context of programming, the separation between the product and the source is particularly lamentable, as it makes it very hard for programmers to improve their tools.

There are a number of other valuable observations and insights in the paper, but in particular the description of their key takeway on domain specific notations and projectional editors is worth sharing here.

> In the non-self-sustainable world, a projectional editor is implemented in some traditional programming language and interface; say, Java. The domain-specific notations can benefit a wide variety of programs created using the editor. Yet, this range of beneficiaries nevertheless forms a “light cone” emanating out from the editor, never including the editor itself. For example, any vector formulae used to render the interface of the editor will remain as verbose Java expressions, along with any code for new additions to the editor. The tragedy of non-self-sustainable programming is that it can never benefit from its own innovations.

In the particular experiment in self-sustainable software systems described in the paper, they aimed to start with an extremely small and minimal basis, and then be very consevative about any other external things they use or bring in such that it would be tractable to rewrite those components within the vocabulary and style of the system itself. OK good luck guys, see you in 50 years! For our purposes, we can relax the requirement about the existing components we bring in being extremely small.

We can factor in a few additional considerations in our decisions about libraries and other components we bring in. We can use things for which there are multiple existing implementations conforming to the same specification. We can use a large library, even if there is only one implementation, where that will not where to cascading effects outward in the design and details of the rest of the software. We can reduce the impact of such effects by containing our usage of a component through incorporating it via a wrapper that we write. We can bring in a component temporarily to experiment with it and learn its merits and deficiencies before choosing another or making our own.

## Combined Object-Lambda Abstractions, with and without graphics and UI

The Self-Sustainability paper built on some work by Ian Piumarta from his time with Viewpoints Research Institute, and much of the other VPRI work is quite relevant to us. In his CV, Piamurta describes this as "Searching for computing's equivalent of the Bose-Einstein Condensate" and goes on to say "Two questions occupy most of my thoughts: how can we eliminate the accidental complexity that is stifling computing (and put some of the beauty back into the discipline), and how small (physically and conceptually) can a self-describing, self-bootstrapping, self-hosting, self-sustaining computing system be? The two questions are intimately related."

At least one of Piamurta's documents on this was circulated unofficially and against a "not for publication or distribution" warning shown in red. He dubbed it combined object-lambda abstractions - the underground paper and the talk were titled "Making COLAs with Pepsi and Coke" and he described making a minimal Lisp, using it to make a minimal Smalltalk, then making that minimal Smalltalk able to build that flavor of Lisp, and again building his Smalltalk with the new build of his Lisp, thus closing the loop, and producing a minimal reflective self-sustainable software system.

The "Ascending the Ladder" paper notes that Piamurta's work did not yet in itself incorporate any provision for graphics. There are good reasons to consider them on track here, which we will see later, but their chosen starting point was very minimal, and at least from their initial publication, they did not appear to take it very far in the first push. If we are looking more to build real tools solutions than do good research, then a reduced-order model may not be the best fit. We will often work more in-vivo and less in-vitro. For us, starting close to the machine and building up seems backwards.

I have long been fond of this quote from Yukihiro Matsumoto ("Matz") who created the Ruby programming language:

> Often people, especially computer engineers, focus on the machines. They think, "By doing this, the machine will run faster. By doing this, the machine will run more effectively. By doing this, the machine will something something something." They are focusing on machines. But in fact we need to focus on humans, on how humans care about doing programming or operating the application of the machines.

Briefly I thought that maybe focusing first on working with those parts of the system most closely relating to user interface could make sense. However there may be some low hanging fruit that would be helpful for that work, and specifically we may instead consider one of the less glamorous areas.

## Glue Code

Glue code refers to those portions of a program that serve solely to connect otherwise incompatible components together. It relates neither to the business logic and problem domain nor the data model, data representation, or data storage, nor the user interface, nor considerations such as efficiency, security, or performance, nor the underlying machine and environment. It contributes nothing directly to the goals and requirements of the software, but instead serves to adapt and connect components together that were not designed and built in a way that would sufficiently facilitate combining them directly.

I've considered some before how best to describe what I do and what sorts of projects best fit my skillset. I have some good diverse technical and other skills, and it wouldn't always make sense to describe myself in terms of a particular technology or set of technologies or role. Some of my major successes have called for leveraging multiple areas of tech expertise, and sometimes in particular working on projects involving multiple programming languages. On reflection, many of the most notable cases have involved great uses of glue code.

With the orignal Meditation Deathmatch software, my collaborator and I were very successful in rapidly producing a working prototype through connecting a Python backend with a JavaScript frontend. At this time this was a novel approach for building a desktop application, but our architecture paralells what became rather popular in the following years with platforms like Electron and Cordova. We successfully delivered a working program under a very constrained timeline and were able to debut it for our friends and community at the festival where we were scheduled to do so. It was quite a hit. A later version of this software retained a continuation of the same frontend code while changing out the backend for an Android Java implementation integrated through Cordova. Now, due to WebBluetooth and other developments, a purely client-side in-browser JavaScript version is feasible, and the Cordova glue can be dropped.

For Smart Farm Systems I was brought in to make a custom Cordova plugin so that existing C++ library code shared by a Rasperry Pi Agriculture IoT hub and controller and Windows desktop client could be used in a new iOS and iPadOS app. Cordova was the secret weapon that allowed us leverage JavaScript and web technologies to rapidly deliver a sophisticated and aesthetic portable user interface, and not be forced to use Swift or C++, which was not considered ideal. Neither was it necessary to re-implement the C++ code in JavaScript, nor attempt to package it in WebAssembly form, which was not clearly viable at the time. This was a major boon in cost, flexibility, and time to market. Along the way I signed some paperwork and became an Apache Software Foundation contributor, upstreaming some changes to Cordova that added an important capability to Android Cordova, bringing it line with Cordova on iOS. Open Source cred of x where x is greater than zero.

More recently I had a client that approached me about working on some of their server-side software to meet a new need. Due to some significant world events you may recall, they suddenly found themselves with essential business software that had always been deployed on a local network and used TCP socket communications. Faced with an immanent need to instead deploy the client interface over the web, they were hoping I could modify the server software and add an entirely new HTTP-based API, and preferably one that used JSON instead of rather inscrutable compact binary messages. The server software was large, complex, and somewhat brittle, and its structure and existing API did not map cleanly to HTTP and stateless request and response communications such as with a REST API. Further, a recent previous at attempt at this same work by another developer working against the same code and specifications had failed entirely. After some not-unstressful thought and study, I threw out all of their plans and design documents entirely, and formulated a new design to meet their goals.

The core of my solution was an independent TCP Socket / WebSocket bridge service to sit between the existing server software and the new client software. Under this plan, only small surgical changes to the server software were required, and I was able to use the most suitable language and libraries for this piece instead of being constrained by the implementation language of either the server or the client. A system for chained filter plugins allowed for this component to further readily and cleanly support translating between binary messages and JSON, as well as other needs such as logging, debugging, and security and authentication concerns. This same tool can also be used in the future for similar challenges elsewhere where web clients may need to communicate with legacy network services.

So after these and other experiences, I now may consider describing myself as a glue code specialist. Glue code is not generally considered exciting, interesting, valuable, or important, so this may come off like telling someone you are a bank that just makes change.

https://youtu.be/CXDxNCzUspM?si=rb3woTUpwWRxerfT

https://youtu.be/KodqIPMbyUg?si=wdtQs4qZZ7ywONvi

This perception means that it could be an area that is understudied in academia and underutilized in industry. Good use of glue code brings flexibility in the choice of software components, allowing more opportunities to leverage a broader set of existing libraries, including existing internal libraries within an organization and also both open and proprietary components in other language ecosystems and platforms. Glue code facilitates rapid prototyping and development, and flexibility in the face of changing requirements and developing technologies.

## Advanced Glue Code Solutions For the 21st Century and Beyond

> Setting: Somewhere on earth, a University, in the not so distant future. A graduate computer science student somehow convinces their advisor to let them do their thesis on glue code. At the thesis defense, the committee is shocked to find themselves neither disappointed nor bored. The new graduate accepts an invitation to lead an initiative at 3M doing glue code R&D. Officially this is aimed at bringing new products to market, but management sees it purely as a marketing scheme to remind the world of their role as experts in the glue business. A new CEO comes in and inevitaby the department is spun off. Disney picks it up. This new generation of Imagineers is doing incredible things with glue code.

The software system project will call for researching, testing, and incorporating and integrating many different components and technologies across different problems and areas of software. Also, flexibility and rapid protyping will be key concerns. A well-considered and excellent tookit for glue code could provide a major positive impact throughout the effort.

Often glue code is very specific to the components being integrated together, and is given no more attention than is required to return to work more directly relating to actual stated business requirements and goals. There are some more general purpose solutions and technologies, such as Cordova which has been mentioned, and also the entire space of web standards can be considered as directed at the need to connect different software systems together.

To examine what future good general-purpose solutions for glue challenges might look like, we can try dividing those challenges into categories. First, challenges which apply to connecting modules together where the source code is available, is written in the same language, will run within the same process, and has no other significant confounding factors. I will skip over further analysis of this category for now, as well as its nearby variations. Second, challenges which apply to connecting modules together between different languages. Third, challenges that arise when integrating components that communicates between processes or over a network.

Current solutions for integrating between languages include using C language bindings as a lingua franca, interface generator tools and domain specific languages for interfaces such as SWIG and CORBA IDL, languages designed to integrate with each other such as .NET languages with each other, Objective-C and Swift, and JVM languages, frontend/backend integration toolkits for desktop and mobile such as Electon and Cordova, and interchange formats such as XML, JSON, and Protocol Buffers.

There are some specific notable tools worth mentioning here. Haxe is a language and compiler specifically designed to transpile to other languages and target and integrate with a variety of platforms. Some computational notebook packages such as Jupyter are built to support pluggable language "kernels" - in this case the name relates to suport for Julia, Python, and R. The Oracle GraalVM project is built to support multiple language frontends in a JVM environment and also native compilation targets, and their Truffle Language Implementation Framework facilitates implementation on GraalVM of interpreted languages, including automated transformation of interpreters to compilers via the Third Futuamura Projection, and inter-language interopration for polygot programming. Less relevant but worth mentioning - the somewhat less ambitious RPython project supports implementation of performant JIT compilers for dynamic languages using a specially designed Python subset, and forms the basis for the PyPy implementation of Python.

These each have some limitations. Though GraalVM can produce lightweight executables and target different platforms, it is itself somewhat hefty and won't squeeze onto your microcontroller or the asset bundle for your web application to support development in-situ in those places. Also its perhaps somewhat viral and a may fit best where an entire executable would be delivered via GraalVM rather anything resembling little bit of glue code. GraalVM also does not yet target WASM, pending broader adoption of a garbage collection extension to the WASM standard. Haxe is suitable for writing libraries, but the compiler itself is neither particularly hackable nor particularly small. Jupyter is mostly specific to the area of computational notebooks.

We can imagine an ideal language for the purpose of writing glue code that integrates components in different languages and would be suitable for use in bootstrapping a self-sustainable software system. It should be extremely portable, very small, include good tools for compiler construction, be able to self-host and transpile itself to many other languages, facilitate compilation of other languages to those same targets, operate either interpreted or compiled, support dynamic and static typing in a powerful type system that could model the type system of other languages, maintain typecheck correctness guarantees when compiled to these various platforms, and be a good fit for the task of making language tools such as parsers, interpreters and compilers. A Prolog system or similar would also be nice. I have cheated a bit by starting with the answer first before writing out this list of requirements, but Shen is all these things.

Shen looks like a great fit here both for glue code and glue code tools as well as the task of prototyping and bootstrapping a new portable multi-target language. In addition to Shen, a library or tool specifically aimed at modeling in-memory representations and calling conventions would be very useful - there do not seem to be really great examples of this but there are a few lesser known ones to examine and study. Some time spent looking further into type system questions would also be valuable, including specifically linear types, affine types, and dependent types. Also a deep examination of algebraic effects will be essential - algebraic effects can model various other effectful computation primitives, including, for example, exceptions and async/await. Also, there are some recent in-memory and networked data representation specifications and associated tools worth noting. These include Cap'n Proto and Apache Arrow Flight.

When it comes to integrating software components across processes and across networks, statecharts and affine types will be key subjects of interest, but the biggest important technology to deploy will be a Distributed Actor framework. The most important of these so far are Erlang OTP on BEAM and Akka on the JVM. There do not seem to be particularly great solutions so far with broader language support. Several libraries within the Rust ecosystem look promising and could possibly made to go wherever they are needed. Many of the other important components we'll be looking to try are in the Rust ecosystem as well, so Rust integration will be an important early focus.

## Candidate for the very first steps

At the very start, before anything else, we may do well to jump into Rust via the Rhai language, and specifically Rhai, rather than only using Rust directly. Rhai is an embedded scripting language engine for Rust. According to their website, Rhai is "A small, fast, easy-to-use scripting language and evaluation engine that integrates tightly with Rust. Builds for most common targets including no-std and WASM."

We can use Rhai to explore Rust GUI toolkits, Rust Actor frameworks, other Rust libraries, challenges of using them together, any and hurdles and solutions for binding them from dynamic languages and deploying them on different platforms.

At any point then we could take these new powers along and return to the paths of glue code tools and solutions and embark on language and compiler design and construction by coding a Shen port in Rhai or Rust. Porting Shen requires only porting its extremely minimal and simple underlying language, K-Lambda, and there are many example K-Lambda implementations. Nevertheless, even here, a side excursion through the mal (Make a Lisp) process would make sense. Mal is a staged tutorial with unit tests that guides the process of a small Lisp languages. At time of writing, the mal repository includes 93 implementations and 115 runtime modes in 87 languages. An adaptation of this to the similar process of constructing a Shen K-Lambda port should clearly include some excellent creative commons musical selection bangers that play when a new test first passes. We can call the basic version "Make a Shen" (MaSh) and the multimedia version "Bangers and Mash".

Multimedia Supplement: Firefly, River says "Mal. Bad. In the Latin."

https://github.com/kanaka/mal

## Fundamental GUI Toolkit Plans

Armed with a bit of our planned glue code magic, we can turn our attention to the front-end next before venturing deep into the realms of the underworld below and all of its arcane mysteries. The things we see and do not yet see in the front-end will inform and inspire all of those other adventures, and we will return to the GUI again and again after each.

I will summarize the current plans for GUI aspects briefly without trying to give all the details and justifications quite yet. Any good plan for the GUI portions will very likely adopt the Elm Model-View-Update architecture. At some point, attention to recent theory and developments in Functional Reactive Programming will be key, including a dive into Affine types, and starting with Evan Czaplicki's thesis, but also including more recent work in Elm, as well as other papers outside Elm, some of which have already been collected.

This architecture along with some other bits in the design will enable critical features of the system including liveness and time travel.

There will also be some work to do in implementing a hybrid push-pull system for fine-grained reactivity in the style of SolidJS, and that could start with examining the code for "voby" and "reactively"

https://github.com/vobyjs/voby

https://github.com/modderme123/reactively

We don't need to build our own push-pull FRP system right away, but it will likely end up integrated down into the language design process. Currently SolidJS for example is a compiler that compiles a FRP JavaScript dialect to plain JavaScript such that mapping signal propogation and fusing update operations happens at compile time rather than at run time. It will make sense to take this seriously as a language design and compiler and tooling construction effort, and behoove us to have a single framework for compilers and language tools. There is also the option here to dive into the topic of deforestation and particularly, examine the decisions for collections data structures and libraries that can facilitate this and other optimizations.

The GUI system should also take the approach of Elm-UI. See: ["Building a Toolkit for Design" by Matthew Griffith](https://youtu.be/Ie-gqwSHQr0) Briefly, Elm-Ui is a declarative high level design system for describing interfaces and layout that draws more from the principles of typography and design than looking to build abstractions upward from what happens to be provided by HTML and CSS.

The GUI system should also follow the design principles of the Glamorous Toolkit, https://gtoolkit.com/docs/principles/

At the start, instead of building from scratch, we should look into adopting the Rust Iced toolkit, which already fits many of our goals and requirements, and aim to stick with it until something about it holds us back. https://github.com/iced-rs/iced

Support for constraints based programming via SAT solvers will also be an important area of exploration. This is a recommended direction from Alan Kay himself, and we have seen the need for it in the Meditation Deathmatch frontend work specifically.

Glamorous Toolkit also includes a focus we will share in the realm of multi-representational displays and editors. Simply using the Elm architecture will help with impmlementation efforts in the space of multi-representational interfaces and projectional editors, along with many of the other ideas in other places of the system. Later, a check into VPRI OMeta Parsing Expression Grammars, reversible computing research, and some rather obscure work from a former University of Texas professor, GitHub user W7Cook who made a language called Enso, which is not the same as the recent but interesting other programming language by the same name. This other Enso is a space to explore as well.

## Immediacy, Liveness, Visual Representation, ...

Taken together, the majority of the design choices, decisions, and components of the system are all in support of the single overaching principle of immediacy.

Liveness, Transparancy, Moldability, Self-Sustainability, Visual Representation, and other things follow from this one.

For a look at liveness, see:

A Perspective on the Evolution of Live Programming (Tanimoto 2023)

Live, Rich, and Composable: Qualities for Programming Beyond Static Text (Horowitz 2023)

## Some specific languages with important powers to reap

Mega Man must collect his weapons and abilities from all of the Robot Masters: 
Metal Man, Air Man, Bubble Man, Quick Man, Crash Man, Flash Man, Heat Man, and Wood Man

Without going into how and why for now, here are some of the languages we will be reaping powers from:
Roc, Gleam, Unison, Shen, Erlang, Elm, Smalltalk (including specifically Glamorous Toolkit), Enso (and maybe that other less known Enso), Austral, Cyclone, Bosque, and Frank


