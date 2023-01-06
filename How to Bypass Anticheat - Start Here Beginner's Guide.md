## Introduction

This thread contains a rough overview of information and skills you will
 need to bypass anticheat. There is also a number of important links and
 references that you will need as you learn more about anticheat. You 
should not even think about attempting to bypass anticheat until you 
have at least 6 months experience.


### Do not learn game hacking on games with anticheat, this is a waste of time.

Instead, learn game hacking first on easy games. Then when you're 
adequately experienced, start learning about anti cheat using this guide
 and then work on reversing and bypassing an anticheat.
 
 
### Kicked or Crashed When Attaching Cheat Engine?

This means they are detecting the Cheat Engine string or the debugger 
attaching. These are the first things to try if the game doesn't have a 
commercial anticheat.

### The first and easiest steps to attempt to bypass anticheat are:

- Use VEH Debugger in Cheat Engine (it's in options under Debugger)
- Try Undetected Cheat Engine
- Try Cheat Engine Alternatives
- Inject Scylla Hide first ( or use x64dbg plugin )
- Try using Manual Mapping and other injection methods from the GH Injector

## What is Anticheat?

Anticheat is functionality built into the game or additional software 
that runs while the game is running, it uses various methods to detect 
cheats. You typically cannot play the game without it running. Most of 
the functionality built into anticheat is just classic antidebug with 
signature detection of cheats that the anticheat has built signatures 
for.

## Features Anticheat Uses

- File Integrity Checks
- String Detection for cheat tools
- Classic AntiDebug
- Obfuscation
- Signature Based Detection
- Hook Detection
- Memory Integrity Checks
- Virtualization
- Kernel Drivers which block process access token creation & more
- Virtualization Detection


## Home Rolled Anticheat

Game developers can easily implement the first few anticheat features, 
specifically file integrity checks, string detection for cheat tools 
& classic antidebug. These are relatively easy to bypass.


## Commercial Anticheat

If you're a developer you can purchase/subscribe to thirdparty 
commercial anticheat. These will always be more strict and more 
difficult to bypass than any anti-debug that the developer creates 
themselves. The most common commercial anticheats are Battleye, 
EasyAntiCheat & Xigncode.

Other common anticheats include Punkbuster, Fairfight & Hackshield.


## Valve Anti Cheat

This is the worst anticheat on the market, do not worry about stupid VAC
 unless you're selling paycheats. Everyone asks stupid question about 
VAC as if it was some god tier anticheat, it's trash and is bypassed 
without doing anything special.



## The most important thing you can do to understand anticheat is watch this [playlist](https://youtu.be/yJHyHU5UjTg?list=PLt9cUwGw6CYG-d7LGlLKHmLWFBJqA2XSV)

Anticheats have the capability to detect every single thing that occurs on your computer, they are extremely invasive, all kernel anticheats are essentially rootkits. Even VAC
 scans every single process that's running. The question is, do they 
have a signature or other detection vector for your specific cheat. 
Signatures are built for known cheat software, so if you write your own 
software, they can't detect it based on signature. They can still use 
heuristics, but they won't autoban for heuristics unless it's very 
obvious it's a cheat. If you're not distributing your hack to 15+ people
 they are not gonna waste their time analyzing your cheat in most cases.
 They have limited resources like every business
 
 


## How to bypass anticheat?


There is no magic trick or download we can give you to instantly bypass 
anticheat. If you have been game hacking for less than 6 months, you 
have no business asking about anticheat. You cannot even understand 
because you do not have the required knowledge to do so. If we told you 
how to bypass anticheat you wouldn't be able to implement it because 
it's not a step 1-2-3 process.


If you want to bypass an anticheat from scratch, by yourself you need 
6-24 months experience game hacking. If you want to bypass anticheat by pasting, gtfo.


To bypass anticheat you must hide from it, disable it, bypass it or 
spoof the results of it's checks. Anticheats will use multiple methods 
to detect you and multiple methods to protect itself, so it's not 
typically as easy as bypassing one feature and you're done. It's usually
 a multi-pronged approach.
 
 
### The second more difficult steps to attempt to bypass an anticheat are:

- Don't use any public source code, write it yourself
- Do not share your hack with anyone
- Use manual mapping to inject using the GH Injector
- or better yet write your own manual mapping injector


## How to learn to bypass anticheat

Here is a step by step guide on what your journey to bypassing anticheat should look like:


- Guide - START HERE Beginners Guide to Learning Game Hacking
- Guide - Beginners Guide To Reverse Engineering Tutorial
- Practice & get experience hacking games without anticheat for at least 6 months
- Make fully featured aimbots & ESPs for games without anticheat
- Be moderately experienced with all aspects of object oriented programming
- Read this guide you are currently reading
- Learn anti-debug
- Learn Important Aspects Windows Internals - you will know which parts are important
- Guide - Kernel Mode Drivers Info for Anticheat Bypass
- Reverse the anticheat of your choosing for several weeks
- Create your anticheat bypass


If you skip more than 1 of these steps you will fail.


### Steam AntiDebug / DRM

Publishers can opt in to have Steam add antidebug/DRM protection when releasing on Steam


## Kernel Mode Anticheat Bypass


The Windows Operating System has different layers which we call rings, 
your game and your hacks are usermode ring 3 processes. Drivers such as 
your video card drivers run in kernel mode or ring 0. These drivers use a
 different API and are written using the Windows DDK (Driver Development
 Kit). These usually have the .sys file extension. They run BELOW 
usermode processes, usermode processes can't touch them. If the 
anticheat has a kernel mode driver you cannot patch it from usermode, 
you must either avoid detection or make your own kernel mode driver.

If you're 1337 you can use vulnerable drivers such as CapCom to load 
your system driver which you can then use to bypass kernel mode 
anticheats.

Here's a little guide: EvanMcBroom/EoPs

You can also defeat Protected Processes Light protection using Mattiwatti/PPLKiller which can sometimes enable you to be able to access and modify previously protected processess



## How Does AntiCheat Work?

To bypass anticheat you must understand how it works. Anticheat work 
very similarly to Antivirus. These are the basic things it does to stop 
you from cheating, kinda going from simple to more advanced

- File Integrity Checks
- Detecting Debuggers
- Stops debugger from attaching
- Detect Cheat Engine & memory editors
- Signature Based Detection
- Detect DLL injection
- Detect Hooks
- Block Read/WriteProcessMemory
- Memory integrity checks
- Statistical Anomaly Detection
- Heuristics


## File Integrity Checks

Patching or hexediting the game.exe should never work. This is how 
custom minecraft clients work, you just make your own EXE or edit the 
one you get with the game. It is very simple to stop this, use a MD5 or 
SHA hash for all the important game files. If bytes in the .exe are 
changed the hash will change. when your game.exe loads it should compare
 the hashes of the important game files against a DB of file hashes, if 
hashes don't match, the game should close.

Bypass: To bypass File Integrity checks, only modify memory, not the 
files on disk. Or reverse engineer the integrity checks and patch them.

Most anti-cheats use signature based detection and file hashes. If a DLL
 gets injected with a known cheat file hash, you're cheating. Signatures
 are built for cheats in the same way that you build a pattern for a 
pattern scan or an antivirus detects viruses. To bypass signature and 
hash detection is too easy, write your own hacks and don't share them. 
Don't use public code that may match a signature that they already have.
 #1 most important is don't copy and paste, if you find some cool code 
you want to use, re-write it differently. I typically do this with 
everything because I learn it better and like my code to all have the 
same style.

Then you have detour/hooking detection. How to detour? you place a jmp 
in the instruction of a function, typically hackers are hooking the same
 core functions such as directx endscene or lets say the gun::shoot() 
function. So they compare what's loaded into memory with what's written 
on disk, if the code doesn't match then it's obvious someone is 
modifying the code at runtime in memory. they could even just scan for 
jmps at 0x0 of every function. How about vtable hooks? just as easy to 
detect.

A decent way to make undetected ESP would be to make external, only use 
readprocessmemory and do an external overlay ESP, this would be 
undetected against most basic anticheats.



## Games that use EAC ( EasyAntiCheat )

7 Days To Die, Aboslver, Audition, Battaltion 1944, Block N Load, Cabal 
Online, Combat Arms, Crossout, Cuisine Royal, DarkFall: Rise of Agon, 
Darwin Project, Days of War, Dead by Daylight, Death Field, Dirty Bomb, 
Dragon Ball Fighter Z, Xenoverse 2, Dragonica Lavalon Awakens, Empyrion,
 Far Cry 5, Fear the Wolves, For Honor, Fortnite, Friday the 13th, 
Gigantic, Hide & Hold Out, Hunt Showdown, Hurtworld, Infestation: 
Surviror Stories, Infesntation: World, Intershelter, iRacing, Ironsight,
 Lifeless, Luna, Magicka Wizard Wars, Memories of Mars, Miscreated, Next
 Day, Offensive Combat Redux, Onward VR, Paladins, Post Scriptum, 
Ragnarok, Realm Royale, Reign of Kings, RF Online, Rising Storm 2, 
Robocraft / Royale, Rockshot, Rust, Sky Noon, Smite, Squad, Sword Art 
Online, Tales Runner, The Culling 1& 2, Ghost Recon Wildlands, Total
 War Arena, War of the Roses, War of the Vikings, War Rock, Warface, 
Warhammer 40,0000, Watch Dogs 2, World Adrift, Yulgang



## Games that Use BattlEye

ARMA II, ARMA III, DAYZ, H1Z1, Ark Survival Evolved, Surivial Of the 
Fittest, PlanetSide 2, Rainbow Six Siege, Survarium, Project Argo, 
Unturned, Insurgency, Day of Infamy, The Isle, Line of Sight, Conan 
Exiles, Blacshot, Tibia, PUBG, Black Squad, Pantomers, Fortnite, 
S4League, Zula, Islands of Nyne, BlackLight Retribution, SOS, PIxark, 
Heroes & Generals, Bless Online.



## Hook Detection

anti debugging tricks follow jumps detect hooks


# Classic AntiDebug = Detecting Debuggers

All anticheats will probably use this technique. When you attach Cheat 
Engine or a debugger it uses a very specific method of interacting with 
the target process. Windows operates this way for security. When you 
attach a debugger you're actually registering the debugger with the 
Windows OS, so detection is obviously quite easy.


### These are 4 basic methods to detect a debugger

### IsDebuggerPresent()

A simple Windows API function that will return TRUE if the calling 
processor has a debugger attached. They can just call this function and 
close the program if it returns TRUE. Read more here

This code will patch IsDebuggerPresent externally so it returns false every time

```
#include <iostream>
#include <Windows.h>
#include <TlHelp32.h>

DWORD GetProcId(const char* procName)
{
    DWORD procId = 0;
    HANDLE hSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
    if (hSnap != INVALID_HANDLE_VALUE)
    {
        PROCESSENTRY32 procEntry;
        procEntry.dwSize = sizeof(procEntry);

        if (Process32First(hSnap, &procEntry))
        {
            do
            {
                if (!_stricmp(procEntry.szExeFile, procName))
                {
                    procId = procEntry.th32ProcessID;
                    break;
                }
            } while (Process32Next(hSnap, &procEntry));

        }
    }
    CloseHandle(hSnap);
    return procId;
}
uintptr_t GetModuleBaseAddress(DWORD procId, const char* modName)
{
    uintptr_t modBaseAddr = 0;
    HANDLE hSnap = CreateToolhelp32Snapshot(TH32CS_SNAPMODULE | TH32CS_SNAPMODULE32, procId);
    if (hSnap != INVALID_HANDLE_VALUE)
    {
        MODULEENTRY32 modEntry;
        modEntry.dwSize = sizeof(modEntry);
        if (Module32First(hSnap, &modEntry))
        {
            do
            {
                if (!_stricmp(modEntry.szModule, modName))
                {
                    modBaseAddr = (uintptr_t)modEntry.modBaseAddr;
                    break;
                }
            } while (Module32Next(hSnap, &modEntry));
        }
    }
    CloseHandle(hSnap);
    return modBaseAddr;
}

void PatchEx(BYTE* dst, BYTE* src, unsigned int size, HANDLE hProcess)
{
    DWORD oldprotect;
    VirtualProtectEx(hProcess, dst, size, PAGE_EXECUTE_READWRITE, &oldprotect);
    WriteProcessMemory(hProcess, dst, src, size, nullptr);
    VirtualProtectEx(hProcess, dst, size, oldprotect, &oldprotect);
}

int main()
{
    DWORD procId = GetProcId("CS2D.exe");

    HANDLE hProc = OpenProcess(PROCESS_ALL_ACCESS, NULL, procId);

    //mov eax, 0
    //ret
    PatchEx((BYTE*)IsDebuggerPresent, (BYTE*)"\xB8\x0\x0\x0\x0\xC3", 6, hProc);

    std::getchar();
    return 0;
}
```


### CheckRemoteDebuggerPresent()

Does the same thing but can work against an external process, so the 
game can run a separate process that calls this on the game process or 
it can just call it against itself. Read more here

### Manually Checking the Being Debugged Flag in the PEB

Both of these functions read the BeingDebugged flag in the PEB (Process Environment Block). If you have bypassed the 2 above functions, they can manually read it from the PEB to bypass your hooks.

```
typedef struct _PEB
{
    BOOLEAN InheritedAddressSpace;
    BOOLEAN ReadImageFileExecOptions;
    BOOLEAN BeingDebugged; //<--
    //etc... 
}
```

How to get PEB Address internally

```
__readfsdword(0x30); //x86
__readgsqword(0x60); //x64
```

You read the fs segment register offset 0x30/0x60 that gives you address
 of the PEB. Offset 0x3 of the PEB is the BeingDebugged flag.


## How to get the PEB externally using NtQueryProcessInformation


```
typedef NTSTATUS(__stdcall* tNtQueryInformationProcess)
(
    HANDLE ProcessHandle,
    PROCESSINFOCLASS ProcessInformationClass,
    PVOID ProcessInformation,
    ULONG ProcessInformationLength,
    PULONG ReturnLength
    );

tNtQueryInformationProcess NtQueryInfoProc = nullptr;

bool ImportNTQueryInfo()
{
    NtQueryInfoProc = (tNtQueryInformationProcess)GetProcAddress(GetModu  leHandle(TEXT("ntdll.dll")), "NtQueryInformationProcess");
    if (NtQueryInfoProc == nullptr)
    {
        return false;
    }
    else return true;
}

PEB GetPEB()
{
    PROCESS_BASIC_INFORMATION pbi;
    PEB peb = { 0 };
    if (!NtQueryInfoProc) ImportNTQueryInfo();

    if (NtQueryInfoProc)
    {
        NTSTATUS status = NtQueryInfoProc(handle, ProcessBasicInformation, &pbi, sizeof(pbi), 0);
        if (NT_SUCCESS(status))
        {
            ReadProcessMemory(handle, pbi.PebBaseAddress, &peb, sizeof(peb), 0);
        }
    }
    return peb;
}
```

### How to Bypass these basic debugger detection techniques

All 3 of the above detections are based on the PEB.BeingDebugged flag, 
so you can bypass them all just by overwriting the BeingDebugged flag 
with 0.

You can also hook each function individually and change the return value to a spoofed result.

NtQueryInformationProcess with ProcessDebugPort argument

"Retrieves a DWORD_PTR value that is the port number of the debugger for
 the process. A nonzero value indicates that the process is being run 
under the control of a ring 3 debugger."

Bypass: Hook NtQueryInformationProcess in the game process

### ThreadHideFromDebugger

### Force an Exception and Try to Catch It

They can also force an exception to occur and try to catch it, if there 
is a debugger attached the exception will get caught by your debugger 
instead of the program.



# How anti-cheats stop the debugger from attaching:

The best way to stop a basic windows debugger from attaching to your 
process is to spawn a child process that debugs the main client and 
another debugger from the client to the child process, thus creating a 
sort of circular protection for both of the processes. This
 is a link that explains how to write a basic windows debugger. Of 
course you don't have to write a complete debugger for this, but you 
might want to read more on this.

Bypass: This could be easily bypassed with a VEH debugger. The 
VEH debugger or exception-based debugger (VEH meaning Vectored Exception
 Handler) is being achieved by setting a PAGE_GUARD protection on 
regions and then writing an exception handler for the page guard 
exception and write your own code to step through the addresses.

Another way of bypassing this is using a basic kernel-mode debugger, but
 this could also be detected by checking the KernelDebuggerEnabled flag 
of SYSTEM_KERNEL_DEBUGGER_INFORMATION (not applicable to the one in 
Cheat Engine


## How anti-cheats detect dll injections:

1) Enumerating the Modules: The easiest way to check for modules 
is to check a module list and see if there are any extra modules 
appearing in there. This can be done with snapshots, with 
EnumProcessModules or using the PEB (this can be done without any APIs).

2) Hooking LoadLibrary: The most basic injection method is 
basically starting a thread with the LoadLibrary API as the starting 
address and the DLL path as an argument. No matter how this is done, the
 function will get called when injection is done, so a way to do this 
would be to hook LoadLibrary and each time it is called, it means that 
there was a DLL injected into the process.

3) DllMain Pattern Scanning: When a DLL is injected, of course it
 will share the same memory as the process it has been injected into, so
 you can understand why it would be pretty easy to detect a DLL entry 
point inside an exe. All you have to do is create a pattern with the 
DllMain binary and scan all the executable regions for it. 

4) TLS Callback to Capture Threads: A not-so-effective way to 
detect DLL injections is to use a TLS callback. This is basically 
something that gets called each time a thread has started/ended. When 
you inject a DLL, you create a thread for it to run so you can detect it
 this way.

5) Debugger to Capture Threads or Loaded Modules: Using a 
debugger, you can also keep track of any modules that have been loaded 
in the process and also started threads. An anti-cheat could just debug 
the process and see when a module has been loaded or a thread has been 
started and based on that could tell if there has been a DLL injection.

And there are many more, other people can complete this

Bypassing: This can be bypassed by doing things like: cloaking 
the module from the module list to prevent people from being able to see
 your module from user-mode, hijacking a thread to execute your code, 
not using the LoadLibrary API (or any other APIs if possible).

Here's a bit of info relating to some detections. There's really just a 
lot to write for each of them and it's 4AM now so I'll just continue 
tomorrow if there's not anyone else that could do a better job. 

How anti-cheats detect Cheat Engine or other memory editors:

An anti-cheat cannot simply detect memory being scanned unless the 
memory scanner is changing protections of the regions (which isn't done)
 or if they hook functions (afaik most don't do this), so here are some 
ways they can tell if a memory scanner is present:

1) Scanning process handles: Obviously for a memory scanner to 
work you would need an open handle to the process, so the process can 
just go trough the handle list to all processes and see if any of them 
have an open process handle to them (the protected client). This can be 
done using the following APIs:

- NtQuerySystemInformation (To get the handle list)
- NtQueryObject (There are alternatives to using this, but this is the easiest way to check if the handle is a process handle)

Source code for doing this: [Click me](https://github.com/Zer0Mem0ry/WindowsNT-Handle-Scanner/blob/master/FindHandles/main.cpp).

Possible bypass (not for the memory editors, but for your own projects):
 There isn't really one way to bypass stuff that would work for 
absolutely all of the anti-cheats, but some ways of doing it are 
removing the handle to that process from the handle list (didn't really 
test it but I guess it could be done), this link out.

Possible bypass (for Cheat Engine): You can switch the debugger in Cheat
 Engine's settings to use the VEH debugger (exception-based debugger) 
which can be detected, but not a lot of games do, or you can use the 
kernel-mode debugger if your system supports it.

3) Checking the window name, window class and process name: The 
single most easy way for anti-cheats to detect Cheat Engine or other 
memory scanners is by using the window name, window class, process name 
and other information about the process to check if any of the programs 
are running on your system and avoid false-positives. 

Possible bypass: The way you can fix this is by writing a simple program
 to change the name of cheat engine's executable to a random name, run 
it as a child process, once it runs, to change its window name and class
 if possible and maybe the icon too (this can be used for detection). 
You can also do this all manually.

4) The memory scanner isn't detected, but what you are doing is: The
 single most easiest way that this can get detected is simply by the 
user either editing an address or more that is causing the CRC/checksum 
to fail. This cannot be bypassed this easily, you will have to reverse 
the game in order to bypass the checksum, so I cannot really give you 
any help here, you'll have to figure that out on your own.
