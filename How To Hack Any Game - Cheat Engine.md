This Cheat Engine tutorial and the next video, will bridge the gap between our previous teachings on Cheat Engine and real game hacking.
 It will cover the basics of finding addresses, pointers & offsets, 
and introduce you to key terms such as base address, entity, local 
player, entity object, static pointer etc...


## What will you learn?

- How to find the local entity object
- How to find pointers
- How to find offsets
- How to use Structure Dissector

This is a remake of an old video made by Solaire which should be much 
more clear and is a good example of the quality of videos you will find 
later in the course. This is the first video introducing you to Assault 
Cube, which is an open source, free, native, x86 game coded in C++ that 
does not use a game engine. This makes it ideal for game hacking 
tutorials and we will use it for the majority of the GHB.


The next video will teach you how to write a simple C++ trainer using the pointers we obtain in this video.

### Considerations

Even if you did CS420 or Game Hacking Shenanigans first, do NOT skip this video, this is the foundation of all future tutorials.
Do not try this on other games yet, they will be much more difficult and only confuse you.
You must be in a real bot match for all tutorials, the demo map will not work.
You must use the correct version of Assault Cube: 1.2.0.2

### Requirements

- Cheat Engine
- Assault Cube 1.2.0.2

## How To Find Offsets, Entity Addresses & Pointers

When you're done watching the [video](https://youtu.be/YaFlh2pIKAg), continue reading down below where 
we will clarify some things from the video that may be confusing. Then 
practice this on Assault Cube, keep repeating the steps from the video 
until you can do it without referencing the video.


### What is an Entity?

An entity is the programming term to describe an object which interacts with other objects. In Unreal Engine they call it an Actor.
 Entities or Actors require the most code because they must be able to 
interact with each other dynamically. Entities typically include the 
players, bots and things like grenades. Indeed in the game's source code
 player's are defined as playerents.

You will hear this term used when hacking all games, essentially an entity is a player object.


### What is an Entity Object?

playerent is the class used for players. A class defines what a class of
 objects contains and what they can do. An object is an instance of a 
class.

So if we have a class named playerent, we create an object of this class
 named Rake. Now Rake is a dynamic entity which contains all the 
required variables and can execute all of it's class functions. You will
 learn more about this later in the Learn C++ part of the GHB.

Essentially, an Entity Object is a player's structure in memory.

You will hear these things being used interchangeably: player object, 
entity object, player, entity. They all refer to the same thing.


### What is an Entity Object's Base Address?

This is simply the first address where the structure begins, with no offsets added. Sometimes we refer to it as offset 0x0.

### What is the Local Player?

This is the player object controlled by your mouse and keyboard. Other 
players and bots are typically referred to as players or entities in 
contrast.


### What is a green address in Cheat Engine?

A static address.

### What is a static address?
An address which is "always" the same, even when you restart the game. 
In reality it means an address which is always relative to the base 
address of a module. You will learn more about that later here: GetModuleBase Function. What matters is that you don't need to follow a pointer path to access a static address, meaning FindDMAAddy() is not required, making hacking these variables much easier. You will learn all about this in the next few videos.

### What is a pointer?
A pointer is a variable, which contains an address. This seems simple 
but it will be the most confusing part of the next 3 months of your 
life. It's something that will "click" in your head one day and make 
perfect sense, do not kill yourself trying to understand it right now.


### What's the difference between "what writes to this address" and "what accesses this address"?

What Writes only returns instructions which overwrite the value at that address.
What Accesses returns both writing and reading instructions, meaning any instruction which touches the address.
We typically just use "What Accesses" because doing it once is better than doing it twice.


### What is the structure dissector used for?
This tool lets you easily reverse engineer structures, with the ability 
to rename and change the type of each variable. It also makes it easy to
 find variables which are also in the same structure. If you found the 
health address, then finding the player position coordinates will 
typically be as easy as looking inside the same class. While Cheat 
Engine's implementation is quite good, with it's auto-guessing feature, ReClass.NET is much better, you will be taught how to use it later.


### What is a Logical Pointer?
In this video, we find a pointer that is not logically defined but it is
 still a valid pointer in memory. Within a large game process, there can
 be hundreds of thousands of pointers which point to the correct 
address. You cannot depend on most of them because they just temporarily
 hold the correct address. A logical pointer will always point to the 
correct address, and they're easy to find when you follow the game logic
 using find what accesses. You want to
 use pointers which are used by game logic you have identified, these 
are most likely to work forever because they utilize a logical pointer 
chain. You can discover the logical pointer path by finding out what 
writes and what accesses.



### Having trouble?

Having trouble understanding offsets, entity addresses, and pointers is 
normal when you first start. It will take months to have a solid 
understanding of it. Watch the video again, evaluate all the details 
slowly and practice each step. If you're still stuck, sometimes skipping
 it and going through the next few tutorials, will solve your 
frustration. But if you get 2-3 videos ahead and are still off-track, 
come back to this tutorial.

If your scans find more addresses than the video, just continue to 
filter them down by hurting yourself and doing additional scans for the 
new health value.

## Homework
Repeat the steps from the video 2-3 times until you can complete them without referencing the video.
