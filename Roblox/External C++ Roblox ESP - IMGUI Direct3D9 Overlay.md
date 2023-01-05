Roblox is one of the most popular game in the world right now. It's got 
insanely huge player base which is constantly growing, so you shouldn't 
be surprised that it's got cheating problem also. People on our forum 
really seam to like this game and we get tons of threads and request 
from players looking for Roblox ESP. That's why I decided to share 
little external cheat that I've found.


This external C++ Roblox ESP comes with a lot of handy features when 
going into PVP combat. External ImGui cheat menu is fully customizable 
so you will be able to change the size of it, move it around the screen 
or even change the color of checkboxes and buttons. It's super easy to 
make the Roblox Cheat look the way you want it to. When you look at the 
features, it doesn't seam much. But trust me, once you start using it 
you will definitely see it's power. Roblox ESP will track your enemies 
position through walls and render 3D boxes around them. You can also 
enable feature that will print their names on top of the boxes. On top 
of that, you are able to save your config so next time you load your 
external cheat you won't need to setup everything again.


## Sample Code

Function DrawPlayer will loop through all the players on the map and 
find their 3D locations. It will do a simple math to figure out how far 
away they are, calculate distance between head and legs in order to 
properly size the box around them. It will convert their 3D world 
position onto 2D screen and render a green box around them. Also, 
function will take their name and print it as a yellow text on top of 
the box.

### DrawPlayer

```

void DrawPlayer(DWORD Instance)
    {
        try
        {
            Globals::rbx.LocalCharacter = Globals::rbx.GetCharacter(Globals::rbx.LocalPlayer);
            Globals::rbx.LocalHumanoid = Globals::rbx.FindFirstChild(Globals::rbx.LocalCharacter, "Humanoid");
            Globals::rbx.LocalHead = Globals::rbx.FindFirstChild(Globals::rbx.LocalCharacter, "Head");
            Globals::rbx.LocalLeftLeg = Globals::rbx.FindFirstChild(Globals::rbx.LocalCharacter, "Left Leg");
            if (Globals::rbx.LocalLeftLeg < 1)
            {
                Globals::rbx.LocalLeftLeg = Globals::rbx.FindFirstChild(Globals::rbx.LocalCharacter, "LeftLowerLeg");
            }
            DWORD Character = Globals::rbx.GetCharacter(Instance);
            DWORD Head = Globals::rbx.FindFirstChild(Character, "Head");
            DWORD Torso = Globals::rbx.FindFirstChild(Character, "Torso");
            if (Torso < 1)
            {
                Torso = Globals::rbx.FindFirstChild(Character, "UpperTorso");
            }
            DWORD LeftLeg = Globals::rbx.FindFirstChild(Character, "Left Leg");
            if (LeftLeg < 1)
            {
                LeftLeg = Globals::rbx.FindFirstChild(Character, "LeftLowerLeg");
            }
            D3DXVECTOR2 LocalPlayerLeftLegScreenPosition;
            D3DXVECTOR2 screenPos1;
            D3DXVECTOR2 screenPos2;
            D3DXVECTOR2 screenPos3;
            D3DXVECTOR3 LocalPlayerHeadPosition;
            D3DXVECTOR3 LocalPlayerLeftLegPosition;
            D3DXVECTOR3 vpos1;
            D3DXVECTOR3 vpos2;
            D3DXVECTOR3 vpos3;
            LocalPlayerHeadPosition = Globals::rbx.GetPlayerPosition(Globals::rbx.LocalHead);
            LocalPlayerLeftLegPosition = Globals::rbx.GetPlayerPosition(Globals::rbx.LocalLeftLeg);
            vpos1 = Globals::rbx.GetPlayerPosition(Head);
            vpos2 = Globals::rbx.GetPlayerPosition(Torso);
            vpos3 = Globals::rbx.GetPlayerPosition(LeftLeg);
            vpos1.y = vpos1.y + 2.5f;
            vpos3.y = vpos3.y - 3.0f;
            LocalPlayerLeftLegPosition.y = LocalPlayerLeftLegPosition.y - 3.0f;
            if (Globals::rbx.WorldToScreen(vpos1, screenPos1, Globals::rbx.ViewMatrix) && Globals::rbx.WorldToScreen(vpos2, screenPos2, Globals::rbx.ViewMatrix) && Globals::rbx.WorldToScreen(vpos3, screenPos3, Globals::rbx.ViewMatrix))
            {
                if (Globals::rbx.nameesp)
                {
                    DrawStringOutline(Globals::rbx.GetName(Instance).c_str(), screenPos3.x - 20, screenPos3.y, 224, 196, 13, 255, pFontVisualsLarge);
                }
                if (Globals::rbx.boxesp)
                {
                    RECT rPosition;
                    rPosition.left = screenPos2.x - (screenPos1.y - screenPos2.y) / 2.1f;
                    rPosition.top = screenPos1.y;
                    rPosition.bottom = screenPos3.y;
                    rPosition.right = screenPos2.x + (screenPos1.y - screenPos2.y) / 2.1f;
                    DrawRectOutlined(rPosition, 22, 163, 43, 255);
                }
            }
        }
        catch (exception e)
        {
            std::cout << "Drawing Error!" << std::endl;
        }
    }
```


This external Roblox cheat works by reading memory, so it needs to find 
right memory addresses to read the information about players. It is 
easily done through scripting engine that has full control over the 
game, this function will find the scripting engine and read different 
information by passing the name of things it wants to read.

### LoadAddresses
```

    void LoadAddresses()
    {
        DWORD ScriptContext = Scan(Proc, Mem.CalculateOffset(0x1861570));
        DataModel = RBX::GetParent(ScriptContext);
        Workspace = RBX::FindFirstChild(DataModel, "Workspace");
        Players = RBX::FindFirstChild(DataModel, "Players");
        Camera = RBX::FindFirstChild(Workspace, "Camera");
        LocalPlayer = RBX::GetLocalPlayer(Players);
        LocalCharacter = RBX::FindFirstChild(Workspace, RBX::GetName(LocalPlayer));
        LocalHumanoid = RBX::FindFirstChild(LocalCharacter, "Humanoid");
        LocalHead = RBX::FindFirstChild(LocalCharacter, "Head");
        LocalTorso = RBX::FindFirstChild(LocalCharacter, "Torso");
        if (LocalTorso < 1)
        {
            LocalTorso = RBX::FindFirstChild(LocalCharacter, "UpperTorso");
        }
    }
```

