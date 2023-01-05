https://www.youtube.com/watch?v=GeZz8xtdLgQ

```
// BEClient_x64!Init
__declspec(dllexport)
battleye::instance_status Init(std::uint64_t integration_version,
                               battleye::becl_game_data* game_data,
                               battleye::becl_be_data* client_data);

enum instance_status
{
    NONE,
    NOT_INITIALIZED,
    SUCCESSFULLY_INITIALIZED,
    DESTROYING,
    DESTROYED
};

struct becl_game_data
{
    char*         game_version;
    std::uint32_t address;
    std::uint16_t port;

    // FUNCTIONS
    using print_message_t = void(*)(char* message);
    print_message_t print_message;

    using request_restart_t = void(*)(std::uint32_t reason);
    request_restart_t request_restart;

    using send_packet_t = void(*)(void* packet, std::uint32_t length);
    send_packet_t send_packet;

    using disconnect_peer_t = void(*)(std::uint8_t* guid, std::uint32_t guid_length, char* reason);
    disconnect_peer_t disconnect_peer;
};

struct becl_be_data
{
    using exit_t = bool(*)();
    exit_t exit;

    using run_t = void(*)();
    run_t run;

    using command_t = void(*)(char* command);
    command_t command;

    using received_packet_t = void(*)(std::uint8_t* received_packet, std::uint32_t length);
    received_packet_t received_packet;

    using on_receive_auth_ticket_t = void(*)(std::uint8_t* ticket, std::uint32_t length);
    on_receive_auth_ticket_t on_receive_auth_ticket;

    using add_peer_t = void(*)(std::uint8_t* guid, std::uint32_t guid_length);
    add_peer_t add_peer;

    using remove_peer_t = void(*)(std::uint8_t* guid, std::uint32_t guid_length);
    remove_peer_t remove_peer;
};
```
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




