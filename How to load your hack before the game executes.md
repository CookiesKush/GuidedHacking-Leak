You might have faced the problem of having to inject your hack before 
anything else of the game has run, so you can be sure that you are the 
first to apply a hook or to call a function and no code has been run 
yet. Today I will provide you a solution to this question: How to load 
your hack before the game executes?



Disclaimer: If the game is initializing stuff in TLS callbacks, 
this solution won’t work for you as TLS callbacks are executed before 
the entry point is. To modify those, you have to do different things, 
but that’s for another time. If you do not know what TLS Callbacks are, 
don’t worry about it.


## How to start a process suspended and load our hack before the process executes?


To load your hack before the game executes, we have start a process in 
“suspended“ mode. You might think that’s the solution, start the process
 in suspended mode and inject my haxy.dll and that’s, unfortunately it’s
 not that easy.



If you start a process in “suspended” mode, the only thing which is 
available is a raw skeleton of the process, with no modules loaded, so 
assuming you are trying to load your haxy.dll using 
"LoadLibraryA("haxy.dll")", the name won’t resolve as there is no 
kernel32.dll loaded yet. So we have to use a different approach to load 
the hack before the game executes.



### What do we know about a process?

A process is a collection of threads which run and one of them is the main thread which does all the lifting (simplified) like:

- Loading the dependencies
- Executing the TLS Callbacks
- Executing the actual code

So what if during step 3 we could interfere with the actual first code execution and modify it, so it executes our own code?

That’s exactly what we will do, we will hijack the first thread let it 
do what we want him to do, in this case load our own haxy.dll and then 
just resume executing the actual game’s code. How to inject before a 
process executes is no longer a problem is you do this.


### How does it work in theory then?

The Windows API provides two functions called “SetThreadContext” and 
“GetThreadContext”, these two set the threads context or get the 
threads' context.


What does the context of a thread contain?


Looking at the struct we can see that it holds the value of “eip” which 
is the register holding the current instruction pointer, the instruction
 pointer basically says at which address is going to be executed, 
assuming you modify it, you tell the CPU to execute the instruction at 
the address you set the “eip” to.

### What are the steps to implement such a solution?

- We need to allocate some memory in our target process to which the eip register should reference to.
- We need to write to that allocated memory, so it executes our own code.
- Provide the code which loads our own library
- Provide the code which returns to the normal flow of execution


### Therefore, the functions we need are:

- Get-&SetThreadContext, gets and sets the thread context
- VirtualAllocEx, allows you to allocate memory in a foreign process
- WriteProcessMemory, allows you to write the memory of a foreign process

x80_86 assembly knowledge, to write the code

Let’s implement it in the language of our choice, for today it will be C++ for me.


### Let this be our target process, which we will start suspended:

```
#include "iostream"

int main()
{
    std::cout << "Hello World i am a poor target application" << std::endl;
    std::cin.get();
}
```


### And this is our hack we want to load before it executes:

```
#include <windows.h>
#include <iostream>

void AmILoadedFirst()
{
    std::cout << "Hello from the very haxy.dll" << std::endl;
}

BOOL WINAPI DllMain(HINSTANCE hinstDLL,  DWORD fdwReason,     LPVOID lpReserved )
{
    switch( fdwReason )
    {
        case DLL_PROCESS_ATTACH:
            AmILoadedFirst();
            break;
        case DLL_THREAD_ATTACH:
            break;
        case DLL_THREAD_DETACH:
            break;
        case DLL_PROCESS_DETACH:
            break;
    }
    return TRUE;
}
```


Therefore, this is our solution, fully commented which shows you how 
to Create the process suspended, hijack execution and then inject our 
hack before it begins normal execution:


```
std::vector<byte> dwordToByte(DWORD number) //we need this function to convert a number like 0x12345678 into a byte array like this [0x12,0x34,0x56,0x74]
{                                           //later on we have to reverse this array so it looks this [0x74, 0x56, 0x34, 0x12] because of windows being little endian
    std::vector<byte> v(4);                 //which means that the smallest bit is at the first position
    for (int i = 0; i < 4; i++)
         v[3 - i] = (number >> (i * 8));
 
     return v;
}


int main()
{
    std::cout << "Loader started..." << std::endl;
    std::cout << "Trying to load target.exe suspended..." << std::endl;

    auto sInfo = new STARTUPINFOA();
    auto pInfo = new PROCESS_INFORMATION();

    CreateProcessA("target.exe",0,0,0,false,CREATE_SUSPENDED | CREATE_NEW_CONSOLE,0,0,sInfo,pInfo); //At this point we create the target.exe in the suspended state and tell it to use a new console
                                                                                                    //as we want to have a new console window and not interfere with the loader
    auto threadContext = new CONTEXT();
    threadContext->ContextFlags = CONTEXT_ALL; //if you get the CONTEXT iof a thread, you have to specify what you want from the thread, CONTEXT_ALL basically says "gimme all"

    GetThreadContext(pInfo->hThread, threadContext);

    std::cout << "Current EIP: " << std::hex << threadContext->Eip << std::endl;
 
    auto oldEip = threadContext->Eip; //we save the old Entry point to make sure we can return to it
    auto newEntryPoint = VirtualAllocEx(pInfo->hProcess,0,1024,MEM_COMMIT,PAGE_EXECUTE_READWRITE); //allocate the new stub in the target process

    auto dllName = VirtualAllocEx(pInfo->hProcess,0,128,MEM_COMMIT,PAGE_EXECUTE_READWRITE); //allocate memory for out dll name
    WriteProcessMemory(pInfo->hProcess,dllName,"haxy.dll",strlen("haxy.dll"),0);

    auto loadLibFromBase = GetProcAddress(GetModuleHandleA("kernel32.dll"),"LoadLibraryA"); //here we get the relative adress of the LoadLibrary function from the target process base

    auto loadlib = (DWORD)loadLibFromBase - (DWORD)newEntryPoint - 11; //this is very important, we have to translate the relative offset from the target to the relative addresss
                                                                       //of our new stub to archive that we just subtract the entrypoints address plus the size of our code until the call to loadlib
                                                                       //mathematically speaking: ourloadlib = loadlibFromBase - (ourEntryPoint + sizeOfCodeUntilCall)
    //The Idea ist:
    //push oldeip; //save the oldeip on the stack
    //pushfd; //save all flags on the stack
    //pushad; //save all registers on the stack
    //push ourLibrary; //push the address of our library so it can be used by LoadLibraryA
    //call loadLibrary; //call Loadlibrary with ourLibrary as the parameter
    //popad; //restore the registers
    //popfd; //restore the flags
    //ret; //take the top of the stack(in this case the oldeip) and put it into the eip registers to be executed next

    auto code = new byte[256] {                                    
        0x68, dwordToByte(oldEip)[3], dwordToByte(oldEip)[2], dwordToByte(oldEip)[1] , dwordToByte(oldEip)[0], //push oldEip
        0x9C, //pushfd
        0x60, //pushad
        0x68, dwordToByte((DWORD)dllName)[3],dwordToByte((DWORD)dllName)[2],dwordToByte((DWORD)dllName)[1],dwordToByte((DWORD)dllName)[0], //push dllName
        0xE8, dwordToByte(((DWORD)loadlib))[3],dwordToByte(((DWORD)loadlib))[2],dwordToByte(((DWORD)loadlib))[1],dwordToByte(((DWORD)loadlib))[0], //call loadlib
        0x61, //popad
        0x9D, //popfd
        0xC3 //ret
    };

    WriteProcessMemory(pInfo->hProcess,newEntryPoint,code,256,0); //obvious write our code to the newstub

    threadContext->Eip = (DWORD)newEntryPoint; //set the thread CONTEXT eip value to our own stub
    SetThreadContext(pInfo->hThread,threadContext); //set the thread CONTEXT

    std::cout << "New Entrypoint:" << std::hex << newEntryPoint << std::endl;

    std::cin.get();
    std::cout << "Resuming target.exe..." << std::endl;
    ResumeThread(pInfo->hThread); //resuming the thread and technically it should work now

    delete sInfo;
    delete pInfo;
    delete code;
 
    //cleanup
    std::cin.get();
}
```


## Conclusion

And now if you run it, you will see it executes our haxy.dll code before
 initializing its own code. And that’s how you load your hack before the
 game executes.
 
I hope this answers all your questions regarding how to start a process suspended and how to inject before a process executes.
