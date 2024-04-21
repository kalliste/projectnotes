
### JHVH-4x4

> So I had thought for some time on the programming language to end all programming languages. 
> 
> Right now most (good) programming languages are fairly low level. An analogy might be describing all the intricate steps to laying a brick wall (amount of mortar, mix of the mortar, spacing of the bricks, prep work to start laying, clean up process after laying, etc.) if you are lucky there is a library function to handle some of this but you will need to understand and implement the syntax. At this point you can easily screw things up and will have to look for something stupid like using a colon instead of a semi-colon or any of number of other problems. All of this can be avoided if you can describe what you want to do and then allow an AI interpreter to do it. The syntax is irrelevant if the interpreter understands what you want. Essentially, this would require a real-time, interpreter/compiler. As you write the software the results are presented. 
> 
> Example: User: Add two valuesAI: Describe Value one, Value twoUser: Blah & BlahAI: Function may produce unusable results, suggest adding additional bounding, see here.

> The result is an unbounded language that can have a virtually limitless number of functions or macros. If you wanted a language that was a combination of lisp, small talk and C with your own commands thrown in that would be fine. As long as the interpreter knows what's going on it's fine. Like when twins create their own language. 
> 
> Of course the question/concern is won't thins make a tower of Babel? No, not necessarily. The AI/Interpreter understands the source and the resulting code. The AI/I could easily take the Human readable source or rather meta-code and convert it to machine readable meta-code. The MRMC is stamped with a version representing a machine generated glossary. MRMC is a description of the function of the code. The function map can then be translated into any language the user desires along with any comments (either human or machine generated) included.

### Upgrade

> Yes, the hubristic user interface for programming looks viable now or soon:
> https://www.unqualified-reservations.org/2009/07/wolfram-alpha-and-hubristic-user/
> 
> But this is not actually the problem really -- it comes from a misconception that non-programmers have about what programmers do.
> 
> The ability to read and write Spanish proficiently will not make you Cervantes.
> 
> The real problem is that non-programmers think in ways that are too vague, imprecise, and unsystematic for building complex systems, and natural language is unsuitable for describing anything sufficiently unambiguously.
> 
> 
> "take the Human readable source or rather meta-code and convert it to machine readable meta-code"
> 
> Yes, we call that a compiler. The main point of high level programming languages is that they are the human readable version of a computer program.

> Taking it from another angle -- I suspect a big part of our trouble now is that we should be expressing much much more about the structure and behavior of our software visually with graphs and diagrams instead of purely with language only. Related to this we need to be able to organize things in ways that let us see the big picture of the thing we are working on, but also drill into details without getting bogged down because the details we need at that moment are tangled up with the details we don't.
> 
> Language models can help us offload a lot of work to the machines that we couldn't before, but we still need to be able to structure software in a way that it can be sliced and subdivided into right size chunks humans can talk about and think about without breaking stuff in adjacent (or distant!) parts of the system
> 
> Ideally coming out of this much of the system would be very much like you are imagining. Humans would be working against basically English language documentation of the system, maybe even using a UI that resembles Ted Nelson's stuff in some ways, but also through speech instead of screens when we like. The machines can help us with the task of keeping English overview synchronized (both ways) with the actual code, but until we have full Strong AI we'll still need humans to be able to work with more precise tools sometimes than English. Again though, that doesn't necessarily have to be programming languages as we know them. It could look more like Scratch, or Touch Designer.
> 
> But yes I think you are generally on track with this. We may even be able to get the language model chatbots trained up to ask the right questions much of the time, and in natural language, to guide a person to consider the right information to make the right decisions, and to resolve the inconsistencies and imprecision in what they are trying to ask for


> One of the funny things with Ted Nelson is that he really sees so well how all of this went off track at Xerox PARC. All of our systems are built on a user interface design that was all about paper metaphor thinking and paper compatibility. It's great for selling printers and copy machines. But being so anti-PARC I think means that he misses a lot of the things that were really cool about what they did at PARC. Much of the really important really valuable stuff there got lost as we went from the Alto to the Macintosh and Windows and all the variations we see in the Linux desktop.
> 
> If he had been building his system the way that they built Smalltalk, I think he might have had more success with it by now.

### JHVH-4x4

> "But this is not actually the problem really -- it comes from a misconception that non-programmers have about what programmers do. The ability to read and write Spanish proficiently will not make you Cervantes."
> 
> This is of course quite true. Thus far the solution has been rigid syntax. However, iteration would be IMHO a better solution. The interpreter could operate a bit like a grammar checker prompting the user to think about how things will work. Sort of a building inspector and instructor all in one. e.g. "This stack will likely overflow when configured like this, Here are ways you can prevent that." In essence the interpreter will try and prevent you from writing bad code.

### Upgrade
> 
> Yah. Building a system like that will itself have to be pretty iterative

> Having it go all the way there requires human level general intelligence in the AI, but we can design the system in ways that let the chatbot help with more and more things as we go

> This talk from Jack Rusher just dropped today. His talk from the same conference last year is the #1 recommended video in the overall plan doc I had sent above.
>
> One of the key ideas here is about how really good notation in math is a powerful tool for amplifying our thinking with math things
> https://youtu.be/1cRFfYQYGxE
>
> A math or physics textbook or an engineering design document is going to have mathematical notation in it, both because having stuff in this form is good for being able to manipulate the symbols to answer new questions, but also because the notation is a often a more precise, clear, fast, and effective communication tool for humans that can read it. Programming languages are not a barrier to entry so much as they are a tool for doing the kind of thinking that is called for in building a large complex system.
>
> But it is also true that there are a lot of tedious details in all of our code that are not helpful for this thinking and iterating process. We've made some progress here in different ways. Kotlin and Swift and Go got rid of the semicolons. (JavaScript swept them under the rug but wasn't able to do it right, so now the semicolons are still there and still matter sometimes, but they are invisible.)
>
> Scripting languages like Python and JavaScript save us from having a lot of low level concerns mixed in with our code, but they don't isolate us from those details nearly as well as a good language could.

> My expectation is that a good system will let you do a lot via just English conversation with a chat interface. But also power users should be able to open up the hood and see charts and graphs and tables and do a lot more there before having to go down further into a place where it's more often about looking at text and symbols. Making things be really good in that middle space of visual representations should overlap a fair bit with the work needed in setting things up for the AI to be more helpful at that top surface level of interaction.

> Right now that middle space is mostly all stuff that programmers do in their heads, most often without realizing it.
>

> There's a key quote here seen in Steve Job's NextSTEP 3 demo video.
> 
> https://youtu.be/rf5o5liZxnA
> 
> "Now think of the NeXT's - think of NeXTSTEP, which is NeXT's object oriented development environment, as an object-oriented cake. I'm about to show you just the frosting on the cake. Many people have tried to copy this frosting, but what they found out is without the object-oriented cake underneath, it just doesn't work."
> 
> A language model can help us translate between code and English for comments, end-user documentation, and our requirements and specifications documents. We can probably get the LLM to do a huge amount of work for us in keeping those types of things in sync with the code.
> 
> But the big thing we want is moldability. We want to be able to make a change to the specification and have that be reflected in the actual system. We want to be able to take the system and see and understand rapidly what it is doing now, and compare that to what we want it to do. The Glamorous Toolkit is an example of a modern system designed for moldable development. Some of what goes into that is in the user interface, but it's a design concern that carries into the entire system.
> 

> I think the way we get there does look a lot like a Smalltalk system such as Glamorous Toolkit, but also using a lot of things we've learned and developed since 1980 that weren't part of the design for Smalltalk. Things like provenance, time-travel debugging, type checking and type inference, the actor model of concurrency, namespacing, model-view-update, state charts, and grammar of graphics.

### JHVH-4x4

> I am picturing the interface being something like a command line where you type things like "create a program" and you get feedback maybe "okay" or maybe it opens a window with instructions. Maybe you say "print Hello World" and you see "Hello World" gets printed and "print 'Hello World'" in the instructions/Meta-code window. You might then say "make it bigger" and "print 'Hello World' large" appears in the instructions window.
> 
> The meta-code is not code it is a description of what the code is doing. The interpreter/compiler is building the code in the background and compiling it on the fly and showing you the result.
> 
> I get the term meta-code from interpreter software where a language is first translated into a meta-language that describes the meaning of the content and then takes that descriptive meta-language and uses it to construct sentences in another language.

### Upgrade

> Ok yah I see what you mean here. There actually would be a layer at which we wouldn't call it code because it's still pretty much natural language.
> 
> The "meta-code" might look like a collection of either direct quotes or paraphrasings of what the bot and the humans have agreed on as being the description of the program. At the next level down there would be a version of things that actually is code, but that code would mostly be declarative rather than procedural. That declarative code then gets interpreted or compiled into machine instructions just like with a spreadsheet or a Haskell program now.
> 
> We can totally get GPT-4 to do this now with a little prompt-kiddery and the right plugins. It just kind of sucks at it. Stuff like langchain and AutoGPT can make it better, and we can take it further by spending time crafting prompts and fine tuning models.
> 
> A big improvement should come with just engineering language models with bigger token limits. Not saying that is easy but it is being actively worked on by people with a lot more money than we have.
> 
> The stuff I am focused on is constraining and designing the layer right below the meta-code. None of this is going to go particularly well if at that layer we have this huge anything-goes design space using an existing popular programming language and whatever random code libraries we pick as we go. Instead we have a language specifically designed for this system and a specific set of libraries, and a specific application template that we start with as scaffolding. We may extend the language and we may add libraries, but we need to be starting with an overall coherent plan concerning how things are organized and the main basic building blocks we are using.

> "The interpreter/compiler is building the code in the background and compiling it on the fly and showing you the result."
> 
> Yup, that's important. It needs to be live, and I've been looking at a lot of the specific engineering decisions and work that go into making that go well. One of the biggest pieces is pretty similar to what lets spreadsheets be live. There's no visible compilation step with spreadsheets - you just edit the formulas live and it knows what depends on what.

### JHVH-4x4

> I foresee moving away from structure oriented programing to results oriented programming.
> 
> The language (whatever form it may take) should define success and or failure. e.g. Draw a ball that is not blue. As long as an image of a ball is drawn (and the ball is not blue) the conditions for success have been met.  As long as you can describe all the conditions defining success/failure your language is fine.
> 
> I imagine there will be a very rapid evolution of language and visual tools to do this. Certainly this will favor the guy who finds the best most succinct way to create this.
> 
> There is a thing, salt is not a problem unless it is a problem. If you are cutting salt out of your diet and you do not have elevated sodium levels you are in a struggle with not benefit. Likewise if you tell your machine to write a hello world program, there thousands of way to do it.  The structure of the hello world program is irrelevant unless that particular structure is a problem. Again, constraints are the important part. Create a hello world program that is as small as possible;  create a hello world program that is as fast as possible, etc.

### Upgrade

> "As long as you can describe all the conditions defining success/failure your language is fine."
> 
> Putting together a complete list that fully defines the behavior we want is at least 10x more work, if not impossible. See: "formal verification" and "halting problem"
> 
> But in the right system, probably we can get pretty far by amassing a pile of sentences describing what we want and what we don't want
> 
> Also, in the right system we may be able to crowd-source the work a lot more instead of assigning big components to specific people

> My expectation is that if you tried to go after this goal directly now without doing all this other stuff to set the AI up for success, and without a human taking a turn frequently, the bot would run a long long time and fail to produce a result that meets the specification, and when it did succeed in producing such a result, it would be very wrong in all sorts of ways you didn't think of to say yet. Basically it would be slower and worse than having the human drive and getting the bot to help with pieces here and there. But people are trying it! So we can just let them do that and see if they can prove me wrong.
