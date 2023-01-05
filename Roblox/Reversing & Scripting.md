<p align="middle">
<br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060638998931972096/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060638998931972096/image.png" alt="">
  </a>
  
  <a align="center" href="https://roblox.fandom.com/wiki/Security_context">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639030749974538/image.png" alt="">
  </a>
  
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639084495765644/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639084495765644/image.png" alt="">
  </a>
  
  ```
-- Resolve player, team, and locate collector & clicker.
local player = game.Players.LocalPlayer
local team = tostring(player.Team)
local collector = game.Workspace.Tycoons.Tycoons[team].Essentials.Giver
local clicker = game.Workspace.Tycoons.Tycoons[team].Essentials.Drop0.Model.clicker.ClickDetector

-- Function to click the dropper once.
local function click_dropper()
    local distance = 0
    fireclickdetector(clicker, distance)
end

-- Function to touch collector once.
local function touch_dropper()
    local character = player.Character or player.CharacterAdded:Wait()
    local root = character.HumanoidRootPart
    firetouchinterest(root, collector, 0) -- Fire
    firetouchinterest(root, collector, 1) -- Un-Fire
end

-- Main auto-farm loop, toggled by global condition.
_G.farming = true
while (_G.farming) do
    click_dropper()
    touch_dropper()
    wait()
end
  ```
  
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639188535496714/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639188535496714/image.png" alt="">
  </a>
  
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639221020377118/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639221020377118/image.png" alt="">
  </a>
  
  <a align="center" href="https://media.discordapp.net/attachments/1029516494176006254/1060639248732135444/image.png">
    <img    src="https://media.discordapp.net/attachments/1029516494176006254/1060639248732135444/image.png" alt="">
  </a>

  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639280575303730/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639280575303730/image.png" alt="">
  </a>

  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639312754004059/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639312754004059/image.png" alt="">
  </a>
  
  ```
local player = game.Players.LocalPlayer
local oldIndex = nil

oldIndex = hookmetamethod(game, '__index', function(table, index)
    if (table == player and index == 'UserId') then
        return(1333636279)
    end
    return oldIndex(table, index)
end)
  ```
  
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639373323935744/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639373323935744/image.png" alt="">
  </a>
  
  ```
local main_script = game.Players.LocalPlayer.PlayerScripts.MainLocalScript
hookfunction(getsenv(main_script)['getGamemode'], function()
    return(1)
end)
  ```
  
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639456085942312/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639456085942312/image.png" alt="">
  </a>
  
  ```
local gc = getgc(true)
local main = game:GetService("Players").AlternateMew.PlayerScripts.MainLocalScript

for i,v in pairs(gc) do
    if (type(v) == 'function' and getfenv(v).script == main) then
        print(v) -- Identify and alter local function here...
    end
end
  ```
  
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060639510645452931/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060639510645452931/image.png" alt="">
  </a>
</p> 
