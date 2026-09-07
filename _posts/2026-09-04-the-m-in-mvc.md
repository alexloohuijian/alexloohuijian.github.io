---
layout: post
title: "The M in MVC: It's Issues in High Frequency Applications"
date: 2026-09-04
categories: [architecture, gamedev]
tags: [csharp, data-oriented-design, performance, systems-programming, memory-architecture]
---

# The M in MVC: It's Issues in High Frequency Applications

## Numbers Going Up

Incremental games like [Cookie Clicker](https://orteil.dashnet.org/cookieclicker/) and [(the) Gnorpe Apologue](https://store.steampowered.com/app/1473350/the_Gnorp_Apologue/) bring to mind the idea of a simplistic UI and game logic. However, beneath that thin veneer of UI lies the fact that most of these games are actually high-frequency, high-intensity spreadsheet simulators, and that in the late game can bring even the most powerful computers down to seconds per frame.

At the core of any incremental game is the idea of numbers going up. In programming and systems developments, there are all sorts of ways to increment a number in an incremental game, but these games tend to have much more than one number, and to handle them at the nonsensically large scales, all while operating under a 16.66 ms (60 FPS)  frame budget.

There has been plenty of advice for trying to build an incremental game as your first project, as they are pretty simple to create. Unless you wanted to start from first principles like I did, as well as having a real super fast forward for the offline catch up instead of a simple and naive time skip (offline time x current rate).

The true super fast-forward mechanism I envisioned can be found in several legendary games, such as [Antimatter Dimensions](https://ivark.github.io/AntimatterDimensions) and [Trimps](https://store.steampowered.com/app/1877960/Trimps/). I knew that crunching millions of tick updates within a reasonable amount of time  would most likely require some serious engineering to do right, so I started brainstorming a bunch. 

Mine, and likely many others, initially thought it was going to be a big compute bottleneck, but it turned out that I was pretty wrong. Modern CPUs have ALUs that can compute billions of floating point operations in the time it takes to say the word “cookie”. The problem was getting all that data into the CPU at that same speed, in a famous phenomenon known as the [Memory Wall](https://dl.acm.org/doi/10.1145/216585.216588).

I had initially planned to to use OOP based MVC architecture, written in C\#, but some quick issues would cause issues, due to nebulous concepts like “cache misses” and that the best solution to all this was “Data Oriented Design”. 

As a wet behind the ears university student, these were all new concepts to me, but then again so were C\#, OOP and MVC, so I decided to dig in further into them. Where does the classic Model break down.

## What is a Model?

Under MVC, a model represents both static persistent game data, such as the cookie production rate of a grandma, as well as mutable runtime data, such as the number of cookies a grandma has made, and coupling both of these distinct datasets together under a single class can cause issues.

To find out how this happens, we must first remember what a C\# object actually is: an 8 byte reference pointer. An object will typically also contain other objects as well, which are more reference pointers to the actual data somewhere else, so trying to find out what the data in a C\# object can frequently end up in a “your princess(data) is in another castle(address)” type of situation.

Is there any problem with that? After all, we have Random Access Memory, able to randomly access memory anywhere in memory at O(1) speeds. While that might be true, we have to remember that programs tend to do more than just retrieve data from memory, it still needs to be operated on. We have [von Neummann](https://en.wikipedia.org/wiki/Von_Neumann_architecture#Von_Neumann_bottleneck) to thank for that.

## Pointer Chasing

Let us start with a simple example. For an incremental game one of the most fundamental operations is iterating over every generator and running that generator. In C\#, a List\<Generator\> stores a contiguous array of 8 byte reference pointers, each one pointing to the data of the generator across the heap.

In order to operate on them, the CPU must engage in something known as pointer chasing. Let us assume a classical CPU that retrieves data from memory one address at a time. Each hop to the system DRAM will take roughly [60 to 80 nanoseconds](https://gist.github.com/jboner/2841832). One might rightfully chuckle at the idea of having to wait nanoseconds, but we have to remember that a modern 4 Ghz processor operates once every 0.25 nanoseconds. Combined with the fact that modern CPUs can process up to 4 instructions per cycle, an 80 nanosecond stall can end up with the CPU wasting up to 1,200 potential operations doing absolutely nothing.

To make things worse, while the CPU is tapping its proverbial feet waiting for literal ages for the data it needs to process the operation requested of it by the program, it is not “idle”, and therefore many have been bamboozled looking at their task managers and the high CPU utilisation and simply assume the program is compute bounded when it is spending most of its time idle, waiting on data.

## The Cache Line

So what happens when the CPU finally gets to where the data is stored. In classical operating systems studies, CPUs are often illustrated as grabbing data from one address at a time. However, the times have changed, and as Ulrich Drepper wrote in his famous [What Every Programmer Should Know About Memory](https://lwn.net/Articles/250967/) article, modern CPUs work with cache lines, pulling 64 bytes at a type from its L1, L2 and L3 caches.

Let us look at a classical generator model and what each generator model might have. 

* Object Header(fat pointer) \- 16 bytes  
* Name(pointer) \- 8 bytes  
* Description(pointer) \- 8 bytes  
* Sprite(pointer) \- 8 bytes  
* Base Output(double) \- 8 bytes  
* Output Multiplier(double) \- 8 bytes  
* Amount generated(double) \- 8 bytes

Total Size \- 64 bytes

Notice of the 64 bytes in the object, we only really cared about 24 of them, the 3 double primitives. The other 40 bytes are irrelevant to our current operation, and this causes “cache line pollution”. To make things worse, the next generator we need to operate on is unlikely to be in the same batch of memory as the first, and the CPU prefetcher needs to do another 80 nanosecond, and this is the true face of the legendary “cache miss”.

## Fast Forward Math Check

If that all sounds complex, it is. With some basic napkin math, assuming our game has 50 generators, and we want to fast forward 8 hours, an average time when a player goes to sleep and comes back on, we will need to process approximately 

8 hours x 3,600 seconds x 60 FPS x 50 generators \= 86,400,000 generator updates. 

With OOP based models, that's around 6.9 seconds of pure CPU stalling. That’s just waiting for memory, we haven't included the actual math, vtable lookups (very popular in C\#) or GC pressure. And that's just for simple generators, operating on primitive numbers. If we want to go beyond 1e308 we will need to start using larger more complex number structures. Add in auto-buyers, synergy upgrades, auto-battlers, auto-prestiging, auto-challanging and many other features, we could end up taking minutes just to load into the game.

## Convergent Engineering

Looking back, there are several structural changes we could do to help improve this time. So firstly, let us remove pointer chasing by structuring our objects as structs instead of objects, so all our data is now contiguous. When we start operating on our generators, there is less need to run around chasing the pointers.

Another improvement we can make is that we could move all to isolate data and state, so that we can read more useful data at once rather than having to deal with data that is irrelevant to the current operation. Stripping the object down to the only 3 numbers we care about, shrinks the object down to 24 bytes, allowing us to fit 2 objects into our cache line at once. Not only that, our array of generators is now 50 x 24 bytes \= 1,200 bytes, which comfortably fits into an L1 cache, which normally has around 32 to 48 kilobytes of space. 

Since the entire array can fit in the L1 cache, we don't need to spend 70 nanoseconds retrieving data from DRAM, and a cache line can feed the CPU registers in around 1 nanosecond, almost 70 times faster, and reducing our previous 8 hour simulation from 6.9 seconds to 0.08 seconds.

Wait a minute. Turns out that just by following how computers actually work, you end up with a set of fairly strict invariants, and following them, I had unconsciously reinvented [Data-Oriented Design](https://neil3d.github.io/assets/img/ecs/DOD-Cpp.pdf). 

## What’s Next?

And so Models can be improved with several simple changes, such as arranging them continuously and splitting static data out from mutable data. However, we must remember all this, and we only arranged the data, we actually haven't operated on them yet.

If the State is now just a flat and dumb array of primitives, with no methods, how do we actually update it, handle user input and render it to the screen? A basic controller would have to now be in charge of a considerable amount of stuff, which might lead into the god-object.

For that, we will have to tackle the other parts of MVC, the View and Controller. Next time.  

