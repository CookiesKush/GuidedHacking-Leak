<p align="middle">
<br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060662445846298695/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060662445846298695/image.png" alt="">
  </a>
</p>

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

<p align="middle">
<br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060663261369995284/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060663261369995284/image.png" alt="">
  </a>
</p>

https://www.youtube.com/watch?v=GeZz8xtdLgQ



In this thread I will share my Discord overlay hook. Same way gamers use discord's features, game hackers can use it as well. Main Discord feature used by game hackers is his hook into game's DirectX or OGL engine. We can abuse this hook and make Discord overlay hook to render our menus. Every anti cheat will block any external process accessing game's memory, but that's not they case for Discord. That's why it's really easy to get your cheat running through Discord overlay hook. Check out the code down below that I provided on how to make Discord overlay hook.


using f_present = HRESULT ( __stdcall* )( IDXGISwapChain * pthis, UINT sync_interval, UINT flags );
f_present o_present = nullptr;

HRESULT __stdcall hk_present( IDXGISwapChain * pthis, UINT sync_interval, UINT flags )
{
    std::cout << "okay\n";
    return o_present( pthis, sync_interval, flags );
}

void initialize()
{
    //hook discord overlay
    const auto pcall_present_discord = helpers::find_pattern_module( "DiscordHook64.dll",
        "\xFF\x15\x00\x00\x00\x00\x8B\xD8\xE8\x00\x00\x00\x00\xE8\x00\x00\x00\x00\xEB\x10", "xx????xxx????x????xx" );

    if ( !pcall_present_discord )
        return;

    const auto poriginal_present = reinterpret_cast< f_present* >( pcall_present_discord + *reinterpret_cast< int32_t* >( pcall_present_discord + 0x2 ) + 0x6 );

    if ( !*poriginal_present )
        return;

    o_present = *poriginal_present;

    *poriginal_present = hk_present;
}


