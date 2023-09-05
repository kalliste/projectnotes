
This post, reproduced in its entirety..

https://erlang.org/pipermail/erlang-questions/2006-October/023289.html


```
[erlang-questions] how are erlang process lightweight?
Richard A. O'Keefe ok@REDACTED
Wed Oct 4 02:18:51 CEST 2006

    Previous message (by thread): [erlang-questions] how are erlang process lightweight?
    Next message (by thread): [erlang-questions] how are erlang process lightweight?
    Messages sorted by: [ date ] [ thread ] [ subject ] [ author ]

Ben Dougall <bend@REDACTED> is still confused.

And THIS is confusing him:
	This was quite interesting from "Erlang Style Concurrency": "[Erlang 
	processes are] very cheap to start up and destroy and are very fast to 
	switch between because under the hood they're simply functions."
	
Erlang processes are *NOT* functions.

Erlang process : Erlang function
    ::
Posix or Windows thread : C function

The principles are exactly the same.
However,

    (1) Posix and Windows thread implementations are SEVERELY limited by
        the fact that they are designed to support statically typed
        languages with no garbage collector information which can create
	pointers to objects in the stacks and save those pointers anywhere.

	That means that Posix and Windows thread implementations
	CANNOT MOVE OR RESIZE STACKS, because they don't know where the
	pointers are and could not fix them.  (Note that static typing
	isn't the main problem; the Burroughs B6700 operating system
	could and did dynamically move and resize stacks with no worries
	at all, because the hardware required pointers to be tagged, so
	the operating system could be absolutely sure of finding and
	fixing all of them.  I was stunned back in 1979 to discover how
	primitive other computers were.)

	Because Posix and Windows thread implementations cannot move or
	resize stacks, 
	(A) you the programmer have to say how big they will be
	(B) to get this right it is absolutely essential that the C
	    compiler tell you how big stack frames are (but it never does)
	    and how deep the stack can get (but it never does that either).
	    So you have to over-estimate with a rather large safety factor.
	(C) heaven help you if you guess wrong, because the hardware,
	    operating system, and run-time library will cheerfully see
	    your program crash and burn.
	    So you REALLY have to over-estimate with a very large safety
	    factor.

	Erlang does keep around enough information (for garbage collection)
	so that it CAN find and fix all the necessary pointers whenever a
	stack has to be moved or resized.  So
	(D) you never have to make guesses, and
	(E) stacks can start very small => lightweight in memory use.

    (2) Posix and Windows thread implementations need the help of the
	operating system for several key operations.  Context switches
	are known to be very very bad for caches; a paper from DEC WRL
	showed that in that system a context switch could foul up your
	cache to the point where it took many thousands of instructions
	to recover, and of course by that time you have another context
	switch.

	The Erlang process implementation is user-level, sometimes
	called "green threads".  No kernel memory is needed for an
	Erlang process.  Erlang process switching does mean that the
	stack data for the process switched out of is gradually lost
	from the cache, BUT what goes into the instruction cache is the
	emulator, and that stays cached.  There are no traps out to the
	operating system and back again (which can be quite slow; deep
	recursion on SPARCs used to be very painful because of that).
	So Erlang process switching is cheap => lightweight in time.

    (3) You might imagine that Java solves problems (1) and (2), and
	indeed it does.  But Java is an imperative programming language;
	computation proceeds by smashing the state of mutable objects.
	If two or more processes try to access the same mutable object
	they can get terribly confused.  So every access to an object
	that *might* be shared with another process has to be 'synchronised'
	(or is it 'synchronized'? I wish they had chosen a word that
	everyone agreed how to spell).  This is called "locking".  Much of
	the locking is done implicitly when you call synchronised methods,
	but that doesn't affect the point that there has to be a heck of a
	lot of it.

	Java implementors have put unbelievable amounts of work into trying
	to make locking fast.  They still can't make it as fast as not locking.
	Although Java had Vector, it acquired ArrayList.  What's the
	difference?  Vector expects to be shared, and does oodles of locking;
	ArrayList expects not to be shared, and does no locking.  It's faster.
	Although Java had StringBuffer, it acquired StringBuilder?  What's
	the difference?  Just that StringBuffer expects to be shared, and
	does oodles of locking; StringBuilder has exactly the same interface
	but expects not to be shared, and does no locking.  It's faster.

	In Erlang, there are no mutable data structures[*], and in any case,
	no process can access another process's data in any way, so there is
	no need for locking in routine calculations.  Obviously, loading and
	unloading modules requires locking, and data base access had better
	involve some kind of locking, but routine calculations marching over
	data structures and building results DON'T need any locking.
	[*] I lied:  there is something called "the process dictionary".
	But that is not and cannot be shared with any other process, so you
	STILL don't need locking.

	Result: Erlang needs little or no locking for routine calculations
	=> lightweight in time.

	Even message sending and receiving could, I believe, be done
	without locking using lock-free queues.  I have no idea whether it
	would be worth while.


    Previous message (by thread): [erlang-questions] how are erlang process lightweight?
    Next message (by thread): [erlang-questions] how are erlang process lightweight?
    Messages sorted by: [ date ] [ thread ] [ subject ] [ author ]

More information about the erlang-questions mailing list

```
