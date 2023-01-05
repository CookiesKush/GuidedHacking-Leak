Easy Anti Cheat is probably the most popular kernel mode anticheat, it 
is used in many games and is owned by Epic Games. It is better than 
Battleye and is therefore harder to bypass. If you want to bypass it you
 must also have a kernel driver. If a game has easy anticheat you will 
not be able to inject, attach a debugger, including Cheat Engine or do 
anything else to the game process until you bypass EAC.



Most the information which will follow in this guide is thanks to @iPower & adrianyy so full credits to them.



You will want to read our kernel anticheat thread before continuing, it 
will have everything you need to get started with bypassing kernel 
anticheats:

[Guide - Kernel Mode Drivers Info for Anticheat Bypass](#)


If you manual map your driver, you must clear the traces including PiDDBCacheTable because they detect you based on that.

## Games that Use Easy Anti CheatApex Legends
- Fortnite
- Rust
- Paladins
- Dead by Daylight
- For Honor
- Gears 5
- Ghost Recon Wildlands
- The Division 2
- ArcheAge
- Cabal Online
- Combat Arms
- Darkfall: Rise of Agaon
- Dauntless
- Dirty Bomb
- Xenoverse 2
- FlyFF Online
- Halo Mastier Chief Collection
- Special Force 2
- and many more

## Easy Anti Cheat Capabilities
Because EAC is a kernel anticheat, it can detect anything and everything. You must load your kernel driver first before the anticheat starts to prevent it.

- Block all interaction with game process
- Block creation of process handles
- Scan for hidden processes & modules
- Scan for known suspicious DLL modules
- Scan for known suspicious drivers
- Get a list of all open handles
- Scan for disks & devices
- Log all loaded drivers
- Gather HWID information
- Detect debuggers
- Find manually mapped drivers
- Detect manually mapped driver traces
- check for kernel patches
- Find handles to physical memory
- detect modules using VirtualProtect
- dumps suspect strings from regions not backed by actual modules
- scans for possible syscall stubs in regions that are not backed by modules (edited)
- does window enumeration to detect suspect overlays
- enumerates suspect shared memory sections
- Detect hooks
- Checks all services
- Scan all threads & system threads
- Stack walking
- Detection of manually mapped modules
- Turla Driver Loader detection
- Hypervisor & VM detection
- DbgUiRemoteBreakin patch
- PsGetProcessDebugPort
- Set HideFromDebugger flag manually
- Reads DR6 and DR7
- Instrumentation callbacks


## Manually Mapped Driver DetectionPiDDBCacheTable & MmUnloadedDrivers
- system pool detection
- system thread detection

## EAC HWID GenerationEAC knows exactly who you are, lots of spoofing might be necessary
- KUSER_SHARED_DATA.ProcessorFeatures ( 0xFFFFF78000000274 )
- Registry
- WMI
- Ntoskernl.exe version
- Mac address
- Disk serials

## Registry Keys for HWIDHKEY_LOCAL_MACHINE\Hardware\Description\System\CentralProcessor\0SystemProductName
- HKEY_LOCAL_MACHINE\HARDWARE\DEVICEMAP\Scsi\Scsi Port 0\Scsi Bus 0\Target Id 0\Logical Unit Id 0Identifier
  - SerialNumber
  - SystemManufacturer
- Computer\HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SystemInformationComputerHardwareId
- Computer\HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\BIOSBIOSVendor
  - BIOSReleaseDate
  - ProductId
  - ProcessorNameString
- Computer\HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Class\{4d36e968-e325-11ce-bfc1-08002be10318}\0000
- Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersionInstallDate
  - DriverDesc
- Computer\HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\WindowsUpdateSusClientId
- Registry\Machine\System\CurrentControlSet\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}\0001
- Registry\Machine\Software\Microsoft\Windows NT\CurrentVersion\Windows Activation Technologies\AdminObject\Store



## Dumped Modules
Easy Anticheat uses kernel drivers and 
usermode modules. You cannot attach a debugger until you bypass EAC, so 
reversing EAC itself becomes a problem. But, if you can get into the 
kernel you can dump the modules, allowing you to statically analyze them
 later. These modules are frequently updated but we have some dumps 
available. This is your first stop for learning how to bypass EAC, you 
can statically analyze these binaries and discover how Easy Anti Cheat 
works.

- Main EAC Dump Thread
- UPDATED RUST EasyAntiCheat Dumps
- UPDATED RUST EasyAntiCheat Dumps #2
- Unpacked Modules & Drivers for Rust
- Apex Legends EAC Dumps
- EasyAntiCheat.sys dump + tracer log file (the log file is 2.6GB lmao)

## Easy Anticheat Hypervisor and VM Detection - secret.club
Currently EAC performs a single vmread upon driver initialization.

### RDTSC/CPUID/RDTSC

EasyAntiCheat also uses the standard timing attack leaving them subject 
to being circumvented through proper TSC emulation (described in an 
earlier subsection).



## IA32_EFER

It came to our attention that EAC, after ~30 minutes of gameplay, 
queried IA32_EFER. We waited for a bit longer to see if any more 
reads/writes to MSRs came through, but after 40 minutes of sitting and 
waiting it was clear nothing else was coming. Below is the notification 
received in iPower’s tracer.

IofCallDriver/NtDeviceIoControlFile

continue reading @ secret.club



## CVEAC-2020: Bypassing EasyAntiCheat integrity checks
"Cheat developers have specific interest in anti-cheat self-integrity checks. 
If you can circumvent them, you can effectively patch out or “hook” any 
anti-cheat code that could lead to a kick or even a ban. In 
EasyAntiCheat’s case, they use a kernel-mode driver which contains some 
interesting detection routines. We are going to examine how their 
integrity checks work and how to circumvent them, effectively allowing 
us to disable the anti-cheat.



### Reversing process

The first thing to do is actually determine if there is any sort of 
integrity check. The easiest way is to patch any byte from .text and see
 if the anti-cheat decides to kick or ban you after some time. About 
10-40 seconds after I patched a random function, I was kicked, revealing
 that they are indeed doing integrity checks in their kernel module. 
With the assistance of my hypervisor-based debugger, which makes use of 
EPT facilities, I set a memory breakpoint on a function that was called 
by their LoadImage notify routine (see PsSetLoadImageNotifyRoutine). After some time, I could find where they were accessing memory.



After examining xrefs in IDA Pro and setting some instruction 
breakpoints, I discovered where the integrity check function gets called
 from, one of them being inside the CreateProcess notify routine (see PsSetCreateProcessNotifyRoutine).
 This routine takes care of some parts of the anti-cheat initialization,
 such as creating internal structures that will be used to represent the
 game process. EAC won’t initialize if it finds out that their kernel 
module has been tampered with...


Continue reading @ secret.club: CVEAC-2020: Bypassing EasyAntiCheat integrity checks




## Suspicious modules that EAC logs (from adriayny)
- Dumper.dll
- Glob.dll
- mswsock.dll
- perl512.dll
- vmclientcore.dll
- vmwarewui.dll
- virtualbox.dll
- qtcorevbox4.dll
- vboxvmm.dll
- netredirect.dll
- atmfd.dll
- cdd.dll
- rdpdd.dll
- vga.dll
- workerdd.dll
- msvbvm60.dll


```
if ( AttachToProcess(process, (__int64)&v5) )
{
    if ( GetUsermodeModule((UNICODE_STRING *)(StringTable + 4830))// Dumper.dll
        && GetUsermodeModule((UNICODE_STRING *)(StringTable + 4852))// Glob.dll
        && GetUsermodeModule((UNICODE_STRING *)(StringTable + 4870))// mswsock.dll
        && GetUsermodeModule((UNICODE_STRING *)(StringTable + 4894))// perl512.dll
        || GetUsermodeModule((UNICODE_STRING *)(StringTable + 4918))// vmclientcore.dll
        || GetUsermodeModule((UNICODE_STRING *)(StringTable + 4952))// vmwarewui.dll
        || GetUsermodeModule((UNICODE_STRING *)(StringTable + 4980))// virtualbox.dll
        || GetUsermodeModule((UNICODE_STRING *)(StringTable + 5010))// qtcorevbox4.dll
        || GetUsermodeModule((UNICODE_STRING *)(StringTable + 5042))// vboxvmm.dll
        || GetUsermodeModule((UNICODE_STRING *)(StringTable + 5066)) )// netredirect.dll
    {
        v3 = 1;
    }
}
```

## Some drivers it looks forDbgv.sys
- PROCMON23.sys
- dbk64.sys

```
LOBYTE(v11) = 1;
if ( !(unsigned int)strstr2((__int64)&a1, (const char *)(StringTable + 8038), v11) )// Dbgv.sys
    break;
LOBYTE(v16) = 1;
if ( !(unsigned int)strstr2((__int64)&a1, (const char *)(StringTable + 8047), v16) )// PROCMON23.sys
    break;
LOBYTE(v17) = 1;
if ( !(unsigned int)strstr2((__int64)&a1, (const char *)(StringTable + 8061), v17) )// dbk64.sys
    break;
```

## EAC user-mode hooks:

```
hk_BaseThreadInitThunk (Kernel32ThreadInitThunkFunction - ntdll.dll)
hk_D3DXCreateFontA (EAT Hook)
hk_D3DXCreateFontIndirectA (EAT Hook)
hk_D3DXCreateSprite (EAT Hook)
hk_D3DXCreateTextureFromFileInMemory (EAT Hook)
hk_D3DXCreateTextureFromFileInMemoryEx (EAT Hook)
hk_D3DXLoadSurfaceFromMemory (EAT Hook)
hk_Dllmain_mono_dll (Inline Hook)
hk_LoadAppInitDlls (Inline Hook)
hk_LoadLibraryExW_user32 (IAT Hook - user32.dll)
hk_LoadLibraryExW_ws2_32 (IAT Hook - ws2_32.dll)
hk_LockResource_kernel32 (IAT Hook - kernel32.dll)
hk_NtCreateFile_kernelbase (IAT Hook - kernelbase.dll)
hk_NtDeviceIoControlFile_mswsock (IAT Hook - mswsock.dll)
hk_NtOpenFile_kernelbase (IAT Hook - kernelbase.dll)
hk_NtProtectVirtualMemory_kernelbase (IAT Hook - kernelbase.dll)
hk_NtQueryDirectoryFile_kernelbase (IAT Hook - kernelbase.dll)
hk_NtUserGetAsyncKeyState_user32 (IAT Hook - user32.dll)
hk_NtUserSendInput_user32 (IAT Hook - user32.dll)
hk_QueryPerformanceCounter (IAT Hook - game.exe)
hk_RtlExitUserProcess_kernel32 (IAT Hook - kernel32.dll)
hk_VirtualAlloc_iat_kernel32 (IAT Hook - kernel32.dll)
hk_mono_assembly_load_from_full (Inline Hook)
hk_mono_assembly_open_full (Inline Hook)
hk_mono_class_from_name (Inline Hook)
hk_mono_runtime_invoke (Inline Hook)
```

### EAC Suspect Threads detection routine for manually mapped code

APIs used for enumerating threads and opening handles to them: CreateToolhelp32Snapshot, Thread32First, Thread32Next, OpenThread
Getting Thread Information: NtQueryInformationThread (ThreadBasicInformation and ThreadQuerySetWin32StartAddress)
Stack walking: GetThreadContext, RtlLookupFunctionEntry and RtlVirtualUnwind


## Steps for detecting suspect threads:
Getting information from all threads in the current process (thread id, stack information, thread base address)
```
//getting thread info
if ( thread_info_obtained )
    {
      thread_info.ExitStatus = thread_basic_info.ExitStatus;
      thread_info.TebBaseAddress = (__int64)thread_basic_info.TebBaseAddress;
      thread_info.Priority = thread_basic_info.Priority;
      thread_info.BasePriority = thread_basic_info.BasePriority;
      thread_info.StartAddress = v18;
      if ( thread_basic_info.TebBaseAddress )
      {
        thread_info.StackBase = *((_QWORD *)thread_basic_info.TebBaseAddress + 1);
        thread_info.StackLimit = *((_QWORD *)thread_basic_info.TebBaseAddress + 2);
      }
      stack_walk_thread(*v8, v14, &thread_info.RipsStackWalk);
LABEL_22:
      v15 = v1->CurrentEntry;
      if ( v1->LastEntry == v15 )
      {
        reallocate_vector_thread_information(v1, v15, &thread_info);
      }
      else
      {
        memcpy_thread_information(v11, v15, &thread_info);
        ++v1->CurrentEntry;
      }
    }
    reset_thread_information_struct(&thread_info);
    ++v8;
    v19 = v8;
  }
```
```
//as the code is huge I'll be only posting their structure for memory regions

struct MEMORY_REGION_INFORMATION
{
  MEMORY_BASIC_INFORMATION mbi;
  STRING_STRUCT DllName;
  STRING_STRUCT SectionName;
};
```

### Finding suspect threads from start addresses/stack walk rips outside modules' ranges
You don't have an eac bypass unless you are hiding from this
```
  //start address check
  start_address = thread_info_1->StartAddress;
  if ( start_address
    && (unsigned __int8)get_region_from_address(start_address, memory_region_info_vec_1, &memory_region_info_) )
  {
    if ( (memory_region_info_.mbi.Protect & 0x10
       || memory_region_info_.mbi.Protect & 0x20
       || memory_region_info_.mbi.Protect & 0x40) //executable region
      && !memory_region_info_.DllName.Length ) //not associated with a module
    {
        //copy data from suspect region
    }
  ////////////////////////////////////////////////////////////////////////

  //stack walk rips check
  entry = thread_info_1->RipsStackWalk.FirstEntry;
  current_entry = thread_info_1->RipsStackWalk.CurrentEntry;
  while ( entry != current_entry )
  {
    if ( *entry
      && (unsigned __int8)get_region_from_address(*entry, memory_region_info_vec_1, &memory_region_info_)
      && (memory_region_info_.mbi.Protect & 0x10
       || memory_region_info_.mbi.Protect & 0x20
       || memory_region_info_.mbi.Protect & 0x40) //executable region
      && !memory_region_info_.DllName.Length ) //not associated with a module
    {
        //copy data
    }
    //...
  }
//...
```
### Copying data and sending to their server
```
//...
CEasyAntiCheat::send(eac_instance,
                     281i64,
                     Dst.FirstEntry,
                     (unsigned int)(LODWORD(Dst.CurrentEntry) - LODWORD(Dst.FirstEntry)));
//...
```
Hopefully after reading all of this, you realize that making a bypass 
for EAC is not a simple task. Sure you can get into kernel and you can 
gain access to the game's process. But EAC knows what you're doing, and 
it's only a matter of time before they ban you. If you want to bypass 
EAC just for screwing around, then that's fine. But if you're selling a 
paycheat where you have lots of users, you will need to make sure your 
EAC bypass is perfect. Luckily we have plenty of resources, and once you
 get into the kernel it's no too hard to dump the modules and start 
reversing.



# Some additional info I found on pastebin:

## Hardware scans by EAC:
EAC always gets your hard disk serial on boot up of their driver. They 
also get your mac address as well. This is always happening for any 
game, but the scans they do after this is different i think for each eac
 build/game.

They seem to do different scanning for different games/eac builds 
between the games. They have this array of numbers which points to the 
scan to be performed. and they loop though it. It seems to be 
static/hardcoded but probably changes for each game.

They allocate 0x400 bytes to store all this info below so they grab ALOT.


## The scans are:

hardware scan id 0-3: reg values keys grab

Using the array I talked about above the two DWORDs in this array 
beforehand determine the path and key they will extract from registry.

In memory the array is an array of dword values, for example, a reg scan
 would look like [3,5,1,...,...,...]. This would mean we are doing scan 
id 1 and extracting BIOSReleaseDate and also 
\Registry\Machine\Hardware\Description\System\BIOS as the array has 3,5 
for these parts.



These are the keys and path strings they can grab (don't ask me why they don't have number 7)

below are listed:

1 = \Registry\Machine\System\CurrentControlSet\Control\SystemInformation

2 = ComputerHardwareId

3 = \Registry\Machine\Hardware\Description\System\BIOS

4 = BIOSVendor

5 = BIOSReleaseDate

6 = SystemManufacturer

8 = SystemProductName

9 = \Registry\Machine\Hardware\DeviceMap\Scsi\Scsi Port 0\Scsi Bus 0\Target Id 0\Logical Unit Id 0

10 = Identifier

11 = SerialNumber

12 = \Registry\Machine\Hardware\Description\System\CentralProcessor\0

13 = ProcessorNameString

14 = <\Registry\Machine\System\CurrentControlSet\Control\Class\{4d36e968-e325-11ce-bfc1-08002be10318}\0000

15 = \Registry\Machine\Software\Microsoft\Windows NT\CurrentVersion

16 = InstallDate

17 = DriverDesc

18 = ProductId

19 = \Registry\Machine\Software\Microsoft\Windows\CurrentVersion\WindowsUpdate

20 = SusClientId

21 = \Registry\Machine\System\CurrentControlSet\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}\0001



Hardware Scan Id 4 = Find all Drivers running and get checksum version info (resource editor) in .sys file



They use QuerySystemInformation to get all drivers running on your system (I think they exclude easyanticheat.sys in this).

For each driver they get back, they read it from the filesystem, check 
it has a valid pe header and if it does, try and locate the "Resource 
section" of the file. When they do, they look for version info resource 
and then inside version info resource they find the product version and 
checksum it. They also seem to store the size of the image as well but 
i'm not 100% on this. If you right click on a file and select properties
 on a .sys file and go to details, you may see a "Product version" 
field. This is what they are checksumming. I discovered that on my 
machine, they at least checksum "Classpnp.sys" as it has a product 
version. It could be any .sys file loaded essentially. They capture the 
filename, checksum of product version string and I believe also the 
image size from the pe as well.



Hardware Scan Id 5 = 64 bit or not



They check if you are using 64 bit version of windows or 32 bit. Not really useful but it's included in their scan results.



Hardware Scan Id 6 = PCI and USB device symbolic names:



They get all devices using IoGetDeviceInterfaces using this GUID ( i don't know how long this is

suppose to be but this is the first bytes of the pointer to the guid):

{84,84,C8,CA,15,75,3,4C,82,E6,71,A8,7A,BA,C3,61,F2,A3,46,21,82,39,2D,C0,A8 }

If they contain in the symbolic link name: \??\PCI or \??\USB then it is
 added to a final checksum. Note the ?? would be like a hex string like 
{4d36e968-e325-11ce-bfc1-08002be10318}, so they use these hex string in 
the device name to id.



Hardware Scan Id 7 = Processor Features

They get 0x40 bytes from memory address 0xfffff78000000274 and encrypt this and send this.
Hardware Scan Id 8 = Read contents of SystemRoot\System32\restore\MachineGuid.txt (if exists)
They read contents of SystemRoot\System32\restore\MachineGuid.txt and either encrypt or hash it.
Here is the scan array taken from Rust EAC 17th December 2018 Build ( see below)


```
struct ScanData {

unsigned int parameter1ForScan;

unsigned int parameter2ForScan;

unsigned int scanCode;

};
```


So for the first we see scan code 0 and we see parameter1Scan as 1 and 
parameter2ForScan as 2. Scan code 0 is reg scan and we are grabbing 
string 1 
(\Registry\Machine\System\CurrentControlSet\Control\SystemInformation) 
& 2 (ComputerHardwareId)

Code:

/Registery/Machine/System/CurrentControlSet/Control/SystemInfomation/

* ComputerHardwareId



HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\BIOS

* BIOSVendor

* BIOSReleaseDate

* SystemManufacturer

* SystemProductName


HKEY_LOCAL_MACHINE\HARDWARE\DEVICEMAP\Scsi\Scsi Port 0\Scsi Bus 0\Target Id 0\Logical Unit Id 0

* Identifier

* SerialNumber



HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\CentralProcessor\0

* ProcessorNameString



HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\{4d36e968-e325-11ce-bfc1-08002be10318}\0000

* DriverDesc



HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion

* InstallDate

* ProductId



HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate

* SusClientId


HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}\0001



Registry\Machine\Software\Microsoft\Windows NT\CurrentVersion\Windows Activation Technologies\AdminObject\Store

* MachineId

* 64-bit

* 32-bit



SystemRoot\System32\restore\MachineGuid.txt <-- they look for this file and use info from this (not everyone has this file)














