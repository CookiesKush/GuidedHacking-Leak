In this article we'll have a look at the differences between Roblox Luau
 and vanilla Lua. You'll learn about Lua language features like syntax 
and available data types. Finally we'll cover metatables, a potentially 
confusing but essential part of Roblox exploit scripting.


<p align="middle">
<br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060659226080464936/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060659226080464936/image.png" alt="">
  </a>
</p>


## Roblox Luau Introduction

Hello everyone, in this article we're going to discuss the scripting 
language used to power Roblox games; Lua. Lua is a language nearly 
exclusively used as an 'extension language', or a language embedded in 
an application to allow the end user to extend its functionality. I'm 
willing to bet that most of you will recognize its inclusion in Cheat 
Engine, but it's also commonly used for game development. High profile 
examples include GMod, Warcraft, Warframe, and of course Roblox.



Roblox, however, uses their own modified version of Lua 5.1 called Luau.
 Luau came about because Roblox needed to improve the performance, 
security, and functionality of Lua while at the same time preserving 
backward compatibility. Eventually the modifications they'd made to the 
original source were plenty enough for them to justify a better in-house
 solution, thus: Luau scripting.
 
 
## Roblox Luau Scripts vs. Lua 5.1

Roblox Luau scripts and Lua 5.1 are very similar to say the 
least. In fact, they are so similar that Roblox themselves tell you to 
learn Lua 5.1 before reading their introduction material about Luau. The
 end-user doesn't really need to know about all of the performance and 
linter enhancements, but let's talk about what's actually different from
 a programmer's perspective.
 

### Roblox Luau Sandboxing
 Yep.. that's the biggest difference! Features have been removed for the sake of security. The io, os, package and debug libraries
 have been removed. The biggest hit here is the debug library which is 
why almost all executors re-introduce this. Functions which could expose
 bytecode have been removed such as loadstring and string.dump. Finally functions which grant access to the file-system have been removed.
 
 
### Upgrading Lua Versions
Roblox has cherry-picked newer Lua version features to implement, and 
you can find the status of all features on their compatibility page [here](https://luau-lang.org/compatibility). Below is a list of the newly implemented features as of this thread's creation.



```

yieldable pcall/xpcall
hex and \z escapes in strings
arguments for function called through xpcall
optional base in math.log
frontier patterns
%g in patterns
\0 in patterns
bit32 library
string.gsub is stricter about using % on special characters only
\u escapes in strings
basic utf-8 support
functions for packing and unpacking values (string.pack/unpack/packsize)
new function table.move
collectgarbage("count") now returns only one result
coroutine.isyieldable
new implementation for math.random
functions lua_resetthread and coroutine.close
The function print calls __tostring instead of tostring to format its arguments
```


## Lua Types, Data, & Flow

I'm not going to attempt to teach you the Lua language myself, or how to
 program in general. I'm going to do my best to demonstrate Lua's syntax
 in this article and I will be providing resources which you can use to 
understand Lua. Honestly if you have any experience with any programming
 language you'll pick it up in no time. Before we begin, I'll warn you 
that Lua's syntax is notoriously disgusting.

- Tables begin at index 1
- {} --> do/then end
- != --> ~=
- i++ --> i = i +1

Now that you've been thoroughly warned let's proceed!


### Lua Types
Lua has an extremely small selection of types, boasting a whopping one 
whole data structure. Lua is dynamically typed, so you don't need to 
remember stuff like an integer being called the 'number' type. You just 
need to get a basic gist of what's available.


- nil --> Lack of any value / null.
- boolean --> true or false
- number --> The number type. Represents floats, ints, etc.
- string --> "A string"
- function --> A function. A chunk of callable Lua code.
- table --> The data structure. Used to store arbitrary data in memory.
- userdata --> C data.
- thread --> A thread of execution.


### Roblox Luau Syntax

I'm going to be showing you some code snippets here, starting with initializing variables of different types.

```

local myLocal = "This is a local variable."
myGlobal = "This is a global variable."

local myString = ('Wow' .. " appended bits!")
local myNumber = 1.5
local myBoolean = true
local myTable = {key = 'value'}

local function myFunction()
    print(myString)
    print(myNumber)
    print(myBoolean)
    print(myTable)
end

myFunction()
print(myFunction)
```
Notice the global variable towards the top. You can make a variable of 
any type, including a function, a global variable. I find it to be good 
practice to keep any variable local that doesn't need to be global. 
However, if you don't want to type the keyword everything will still 
function fine. The only data type I feel is necessary to explain further
 are tables. They work very differently from other languages, and we're 
going to get into it further down in the more 'advanced' section.



For now lets look at control flow. Conditional statements in Lua are 
very generic: if, elseif, else, end. You get the gist. You also have 
your basic for and while loops, but oddly there's another loop type 
'repeat until'. It repeats a chunk of code until a condition is true. 
Kind of the opposite of a while loop. I've never had to use it because I
 mean, `while (not condition)` accomplishes the same thing and feels so much more familiar. Anyway, let's see the code snippet.



```

local condition = true
local secondConditon = false

-- Demonstrate conditional statements.
if (condition) then
    print("First was true.")
elseif (secondCondition) then
    print("Second was true.")
else
    print("Neither were true")
end

-- Demonstrate while and repeat until loops.
local counter = 5
print("Counter: " .. tostring(counter))
while (counter > 0) do
    counter = counter - 1
end
print("Counter: " .. tostring(counter))
repeat
    counter = counter + 1
until (counter == 5)
print("Counter: " .. tostring(counter))

-- Demonstrate for loops.
for i = 1, 5 do
    print(i)
end
```

We're going to cover for loops for iteration of tables down below, but 
other than that we've covered Lua's syntax. Pretty simple eh? But wait! 
There are some things that may genuinely confuse you beyond the language
 basics. Here's the section you need to pay attention to, my dear 
skimmers.

## 'Advanced' Roblox Scripting Section


First lets talk some more about tables. Being the only data structure, 
they can do a lot of stuff that we don't normally associate with tables.
 They can take the place of both dictionaries and arrays. Because of 
this, there are two iterator functions we need to know about: `ipairs()` and `pairs()`.


```
local greetingTable = {'Hello', 'how', 'are', 'you?'}
local phoneTable = {John = '000-0000', Michelle = '111-1111'}

local greetingString = ''
for index, value in ipairs(greetingTable) do
    greetingString = (greetingString .. value .. ' ')
end
print(greetingString)

for key, value in pairs(phoneTable) do
    print(key .. "'s phone number is " .. value)
end
```

ables are also used to implement a form of OOP, assigning functions to keys and calling them as `table.doSomething()`.
 However the most confusing aspect of tables that we need to understand 
for Roblox exploit scripting are metatables and metamethods. I'm going 
to put them as simply as I can.


## Metatables & Metamethods


LUA Metatables are ordinary tables (in dictionary format) which contain 
methods used behind the scenes when we perform operations on a table. 
These are able to be accessed and modified. For example when we write `print(table.x)` it is possible to overload this operation using the `__index` metamethod. We could make it return a constant 5 rather than whatever value was assigned to x.


## Environments

Environments are tables which store global variables, put simply. In 
Roblox, there is an extra layer of environment. There is the top level 
environment which contains the entire global environment for every 
LocalScript referred to as the global environment. On the second layer, 
each script gets its own environment called the script environment. 
Within each script (the third layer), every function gets its own 
environment referred to as the function environment. This is much 
simpler than it sounds, it's probably better to visualize than read from
 a block of text.
 
 

<p align="middle">
<br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060660505896820817/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060660505896820817/image.png" alt="">
  </a>
</p>



## Upvalues, Constants, Closures

We don't need to know all of the details here, but in essence a closure 
is both a function and everything it needs to access non-local values. 
In our case we're interested in changing these values within existing 
scripts. So we need to know about how objects are accessed within a 
function.



Consider this example. We know that a LUA function can access a global 
variable, provided that it was declared within the same environment. 
This makes perfect sense, but why can we access local variables in 
nested scopes? Have a look at this code.

```

local five = 5

local function plusFive(num)
    return(num + five)
end

print(plusFive(10))
```

This is because a function stores 'upvalues' in its closure. You can 
think of the term 'upvalue' as meaning 'external local variable'. 
Constants are far simpler to understand. In the example above, from the 
point of view of Lua, the entire script is enclosed within an anonymous 
function. Meaning that the variable 'five' is a constant within the 
top-level closure. We'll go into more detail about how to actually use 
this knowledge in the upcoming reversing article.


## Lua & Roblox Luau Script Conclusion

Now it's time for you to evaluate your own understanding. While I 
couldn't teach the language in one article, I've aimed to explain some 
syntactic quirks, language features, and higher level concepts that I 
wish I had known before diving into Roblox exploit scripting. I have two
 directions point anyone who's made it this far. If you're a programmer 
already, skim through the reference manual. If you've never written a 
line of code, you have a bit of a larger task to undergo. You should 
read Programming in Lua, and then re-read this article. After doing so, 
you're free to move on to the next article. Until next time!
