In this article we write a complete Roblox exploit script. I walk you 
through the process of reverse engineering the game, to developing 
scripts which exploit game mechanics. By the end of this article you'll 
understand the thought process behind each step of Roblox exploit script
 development, and you'll have the source code for a complete script 
including a GUI.

## Completed Roblox Hack Tutorial Introduction

Hello everyone, in this article we're going to be wrapping up the course
 with a completed source code for a simple Roblox hack. We're going to 
start off by choosing a target and putting everything together that 
we've discussed so far. We're going to write a fully functional exploit 
script for the target Roblox game and create a simple user interface. 
Finally, we're going to talk about distributing your scripts. With all 
the information and experience we've gained from the previous lessons, 
we will conclude this course with a proper Roblox hack tutorial. Let's 
begin.

## Writing Roblox Exploit Scripts

The target I've chosen is a game called "Fruit Juice Tycoon: Refreshed".
 Tycoon is a common genre of Roblox game where the core loop is 
acquiring currency and spending it on various upgrades. A lot of them 
are developed without much effort, and use pre-made assets. You will run
 into lots of these assets across many games. I've chosen this game 
specifically because it's one of the more unconventional tycoons, and 
has novel mechanics to mess with.


## Discovery

Before doing anything else, I am going to hop into the game and play it 
for a while. My goal is to get a complete grasp of the game's mechanics 
so that I know what's available to be messed with. You spawn into the 
game and claim a tycoon, and your goal is to buy all of the fruit trees.
 You collect and sell the fruit to generate money. There is an obstacle 
course that when finished will grant you something called 'frenzy time'.
 When this is activated, fruits are automatically sold and a multiplier 
bonus is applied to the revenue. The obstacle course has a cool-down. 
That seems to be about all of the mechanics this game has to offer.


Based upon this I want to accomplish four things. I want to automate 
fruit collection, fruit selling, completion of the obstacle course, and 
upgrading the tycoon. The next thing I am going to do is dump the 
scripts using Synapse's script dumper and comb through them for 
potential vulnerabilities. After doing so I find two relevant scripts, 
one called 'CollectFruitClient' and the other 'ObbyClient'. 
'CollectFruitClient' shows us how the tool is telling the server to add 
the fruit to our inventory, and 'ObbyClient' shows us where the current 
obstacle course cool-down is stored.

<p align="middle">
  <br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060656860149071922/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060656860149071922/image.png" alt="">
  </a>
</p>



## Prototyping
I am now ready to begin prototyping my ideas. The difference between 
these scripts and the final result is that while prototyping I don't 
care about anything besides proving something is possible. The first 
step is going to be opening Synapse's explorer. I am going to begin by 
testing what I've found in the fruit collection script. From what I can 
see in the script, when you use the collection tool it creates a 
hovering orb that follows your mouse. When that orb comes into contact 
with a fruit, it fires a remote event. Before firing the remote it 
checks if the argument part has the tag 'Fruit'. From this I can infer 
that the object being passed is the fruit part itself. If there were any
 ambiguity, I could use a remote spy and get it to trigger the event.



In the explorer's settings I toggle 'click to select part' and click on a
 fruit. I scroll to find the highlighted part, and it shows me that 
every fruit is parented to a folder in my tycoon called 'Drops'. I 
believe I could enumerate the contents of this folder, and fire the 
remote on each of them. After testing this theory, all seems to be 
working and my first goal is accomplished.

```
local fruits = game.Workspace.Tycoons.Roberry.Drops:GetChildren()
local remote = game.ReplicatedStorage.CollectFruit

for i, v in ipairs(fruits) do
    remote:FireServer(v)
end
```

Now I need to automate the completion of the obstacle course. My first 
thought is to use the explorer to find the button that you step on at 
the end of the course in the explorer. Sure enough it's triggered by a 
simple TouchInterest. I try to fire it, but the developers are one step 
ahead. Instead of a bonus, I'm called out and given one pity dollar...

<p align="middle">
  <br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060657312357949503/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060657312357949503/image.png" alt="">
  </a>
</p>


I'm not going to give up so easily though. After skimming the script 
dump, I am certain the server isn't informed via a remote. I use the 
explorer to look at the other parts of the obstacle course and I notice 
another part with a TouchInterest called 'RealObbyStartPart'. Seeing 
this I wonder if I could touch this first, and then the finish. Bingo! 
Now I need to work with the cool-down. In the script I read earlier, I 
learned that the cool-down is stored as an attribute of the player 
instance. All I need to do is fetch this before attempting to complete 
course.


```
local player = game.Players.LocalPlayer
local root = game.Players.LocalPlayer.Character.HumanoidRootPart
local start = game.Workspace.ObbyParts.RealObbyStartPart
local finish = game.Workspace.ObbyParts.VictoryPart

local function touch_part(part)
    firetouchinterest(root, part, 0)
    wait(0.1)
    firetouchinterest(root, part, 1)
    wait(0.1)
end

if (player:GetAttribute("ObbyCooldown") == 0) then
    touch_part(start)
    touch_part(finish)
end
```

Next I am going to automate the selling, or 'juicing', of the fruits. 
The machine uses a proximity prompt, one of the few server-sided signals
 in Roblox. There is still a workaround however, I just need to be 
within a seemingly legitimate distance of the machine before firing the 
prompt. I can accomplish this by teleporting my player to the machine, 
firing the prompt, and then teleporting back to my original position. A 
simple, perfectly functioning solution.

```
local button = game.Workspace.Tycoons.Roberry.Essentials.JuiceMaker.StartJuiceMakerButton
local root = game.Players.LocalPlayer.Character.HumanoidRootPart
local prompt = button.PromptAttachment.StartPrompt

local function fire_juice_maker()
    local origin_pos = root.CFrame
    root.CFrame = button.CFrame
    wait(0.15)
    fireproximityprompt(prompt, 1)
    root.CFrame = origin_pos
end

fire_juice_maker()
```

Finally I want to make an auto-upgrade. This is a pretty simple task but
 requires a good bit more code. Inspecting all of the upgrade buttons in
 the explorer, I can see that each of them is triggered by a 
TouchInterest. I need to first find the cheapest upgrade available, 
check if I can afford it, and potentially fire the touch signal to 
purchase it.



```
local root = game.Players.LocalPlayer.Character.HumanoidRootPart
local money = game.Players.LocalPlayer.PlayerGui.MoneyGui.TweenValue.Value
local buttons = game.Workspace.Tycoons.Roberry.Buttons:GetChildren()

local function get_cheapest()
    local cheapest = nil
    for i, v in ipairs(buttons) do
        local cost_string = v.ButtonLabel.CostLabel.Text
        local stripped_cost_string = string.gsub(cost_string, ",", "")
        local cost_int = tonumber(stripped_cost_string)
        if (cheapest == nil) then
            cheapest = {}
            cheapest.cost = cost_int
            cheapest.part = v
        elseif (cheapest.cost > cost_int) then
            cheapest.cost = cost_int
            cheapest.part = v
        end
    end
    return(cheapest)
end

local function do_upgrade()
    local cheapest = get_cheapest()
    if (cheapest.cost <= money) then
        firetouchinterest(root, cheapest.part, 0)
    end
end

do_upgrade()
```

## Polish & Refactor

Now that I have functioning prototypes of each of my planned features, I
 need to combine them into one big script. I need to ensure that 
volatile instances aren't called when they don't exist, for example 
attempting to access the Humanoid when a re-spawn occurs. I need to 
dynamically grab information that may change from session to session, 
such as the location of the player's tycoon. I need to remove redundant 
code and add comments for future maintenance. I am not going to explain 
each of my actions here because this is more of a general programming 
skill.

```
local player = game.Players.LocalPlayer

local function get_player_tycoon()
    local tycoon_string = tostring(player.OwnedTycoon.Value)
    local tycoon = game.Workspace.Tycoons[tycoon_string]
    return(tycoon)
end

local function get_humanoid_root()
    local character = player.Character or player.CharacterAdded:Wait()
    local root = character:WaitForChild("HumanoidRootPart")
    return(root)
end

local function get_player_money()
    local money = player.PlayerGui.MoneyGui.TweenValue.Value
    return(money)
end

local function get_upgrade_buttons()
    local tycoon = get_player_tycoon()
    local upgrade_buttons = tycoon.Buttons:GetChildren()
    return(upgrade_buttons)
end

local function get_cheapest_upgrade()
    local upgrade_buttons = get_upgrade_buttons()
    local cheapest_upgrade = nil
 
    for index, part in ipairs(upgrade_buttons) do
        -- Convert from cost string to integer.
        -- i.e. "1,234,567" -> "1234567" -> 1234567
        local cost_string = part.ButtonLabel.CostLabel.Text
        local stripped_cost_string = string.gsub(cost_string, ",", "")
        local cost = tonumber(stripped_cost_string)

        if (not cheapest_upgrade) then
            cheapest_upgrade = {cost=cost, part=part}
        elseif (cheapest_upgrade.cost > cost) then
            cheapest_upgrade = {cost=cost, part=part}
        end
    end
    return(cheapest_upgrade)
end

local function try_upgrade()
    if (#get_upgrade_buttons() > 0) then
        local root = get_humanoid_root()
        local current_money = get_player_money()
        local cheapest_upgrade = get_cheapest_upgrade()
 
        if (cheapest_upgrade.cost <= current_money) then
            firetouchinterest(root, cheapest_upgrade.part, 0)
        end
    end
end

local function fire_juice_maker()
    local tycoon = get_player_tycoon()
    local root = get_humanoid_root()
    local button = tycoon.Essentials.JuiceMaker.StartJuiceMakerButton
    local prompt = button.PromptAttachment.StartPrompt
    local origin = root.CFrame

    root.CFrame = button.CFrame
    wait(0.15)
    fireproximityprompt(prompt, 1)
    root.CFrame = origin
end

local function collect_all_fruits()
    local tycoon = get_player_tycoon()
    local fruits = tycoon.Drops:GetChildren()
    local remote = game.ReplicatedStorage.CollectFruit
 
    for index, part in ipairs(fruits) do
        remote:FireServer(part)
    end
end

local function try_complete_obby()
    if (player:GetAttribute("ObbyCooldown") == 0) then
        local root = get_humanoid_root()
        local start = game.Workspace.ObbyParts.RealObbyStartPart
        local finish = game.Workspace.ObbyParts.VictoryPart
 
        -- Fire / Un-Fire the starting point first, then the finish point. (AC Workaround)
        firetouchinterest(root, start, 0)
        firetouchinterest(root, start, 1)
        firetouchinterest(root, finish, 0)
        firetouchinterest(root, finish, 1)
    end
end

-- Auto-Upgrade Callback
try_upgrade()

-- Auto-Sell Callback
fire_juice_maker()

-- Auto-Collect Callback
collect_all_fruits()

-- Auto-Obby Callback
try_complete_obby()
```

## GUI

The last thing I am going to do is build a GUI for the script. Right now
 I've got functions which perform the task I want, but only once. I want
 to be able to toggle each of these on and off, and have them 
automatically execute repeatedly. I also want to be able to hide my GUI 
so it's not taking up any screen space. So let's see how that looks in 
practice.

```
local user_input_service = game:GetService("UserInputService")

local function create_gui()
    local screen_gui = Instance.new("ScreenGui")
    local frame = Instance.new("Frame")
    local list_layout = Instance.new("UIListLayout")
 
    syn.protect_gui(screen_gui)
    screen_gui.Parent = game.CoreGui
    screen_gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    frame.Parent = screen_gui
    frame.AnchorPoint = Vector2.new(1, 1)
    frame.Position = UDim2.new(1, 0, 1, 0)
    frame.Size = UDim2.new(0.1, 0, 0.25, 0)
    list_layout.Parent = frame
    list_layout.SortOrder = Enum.SortOrder.LayoutOrder
 
    user_input_service.InputBegan:Connect(function(input)
        if (input.KeyCode == Enum.KeyCode.RightControl) then
            screen_gui.Enabled = not screen_gui.Enabled
        end
    end)
 
    return(frame)
end

local function add_button(parent, callback, interval)
    local button = Instance.new("TextButton")
 
    button.Parent = parent
    button.BackgroundColor3 = Color3.fromRGB(255, 170, 127)
    button.Size = UDim2.new(1, 0, 0.25, 0)
    button.Font = Enum.Font.SourceSans
    button.TextColor3 = Color3.fromRGB(0, 0, 0)
    button.TextSize = 14.000
    button:SetAttribute("Active", false)
 
    button.MouseButton1Click:Connect(function()
        button:SetAttribute("Active", (not button:GetAttribute("Active")))
        if (button:GetAttribute("Active")) then
            -- On
            button.BackgroundColor3 = Color3.fromRGB(85, 255, 127)
            -- If a thread isn't running, launch a new thread which calls the callback every interval.
            if not (button:GetAttribute("Debounce")) then
                spawn(function()
                    button:SetAttribute("Debounce", true)
                    while (button:GetAttribute("Active")) do
                        callback()
                        wait(interval)
                    end
                    button:SetAttribute("Debounce", false)
                end)
            end
        else
            -- Off
            button.BackgroundColor3 = Color3.fromRGB(255, 170, 127)
        end
    end)
end
```

<p align="middle">
  <br>
  <a align="center" href="https://cdn.discordapp.com/attachments/1029516494176006254/1060657849090441267/image.png">
    <img    src="https://cdn.discordapp.com/attachments/1029516494176006254/1060657849090441267/image.png" alt="">
  </a>
</p>


But we run into the problem that if we rapidly click, many threads will 
spawn. So we add a 'Debounce' attribute so that each button can only 
spawn one thread at a time. UI libraries can be very hard to understand,
 do not be put off if you have to read through the code a few times. It 
will click in your head with some time.


## Final Exploit Script Source Code

I know this article has been very code heavy, and you've seen the entire
 process already, so I am putting the final result in a spoiler. If all 
went well, this should have reinforced your understanding of everything 
prior. You now know, start to finish, how to approach a game and write 
an exploit script.

<iframe src="https://player.vimeo.com/video/722404354?h=d7de953db4" width="640" height="360" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen></iframe>
<p><a href="https://vimeo.com/722404354">Demo.mp4</a> from <a href="https://vimeo.com/user178815305">Mewspaper</a> on <a href="https://vimeo.com">Vimeo</a>.</p>


# Complete Source
```
local user_input_service = game:GetService("UserInputService")
local player = game.Players.LocalPlayer

local function get_player_tycoon()
    local tycoon_string = tostring(player.OwnedTycoon.Value)
    local tycoon = game.Workspace.Tycoons[tycoon_string]
    return(tycoon)
end

local function get_humanoid_root()
    local character = player.Character or player.CharacterAdded:Wait()
    local root = character:WaitForChild("HumanoidRootPart")
    return(root)
end

local function get_player_money()
    local money = player.PlayerGui.MoneyGui.TweenValue.Value
    return(money)
end

local function get_upgrade_buttons()
    local tycoon = get_player_tycoon()
    local upgrade_buttons = tycoon.Buttons:GetChildren()
    return(upgrade_buttons)
end

local function get_cheapest_upgrade()
    local upgrade_buttons = get_upgrade_buttons()
    local cheapest_upgrade = nil
 
    for index, part in ipairs(upgrade_buttons) do
        -- Convert from cost string to integer.
        -- i.e. "1,234,567" -> "1234567" -> 1234567
        local cost_string = part.ButtonLabel.CostLabel.Text
        local stripped_cost_string = string.gsub(cost_string, ",", "")
        local cost = tonumber(stripped_cost_string)

        if (not cheapest_upgrade) then
            cheapest_upgrade = {cost=cost, part=part}
        elseif (cheapest_upgrade.cost > cost) then
            cheapest_upgrade = {cost=cost, part=part}
        end
    end
    return(cheapest_upgrade)
end

local function try_upgrade()
    if (#get_upgrade_buttons() > 0) then
        local root = get_humanoid_root()
        local current_money = get_player_money()
        local cheapest_upgrade = get_cheapest_upgrade()
 
        if (cheapest_upgrade.cost <= current_money) then
            firetouchinterest(root, cheapest_upgrade.part, 0)
        end
    end
end

local function fire_juice_maker()
    local tycoon = get_player_tycoon()
    local root = get_humanoid_root()
    local button = tycoon.Essentials.JuiceMaker.StartJuiceMakerButton
    local prompt = button.PromptAttachment.StartPrompt
    local origin = root.CFrame

    root.CFrame = button.CFrame
    wait(0.15)
    fireproximityprompt(prompt, 1)
    root.CFrame = origin
end

local function collect_all_fruits()
    local tycoon = get_player_tycoon()
    local fruits = tycoon.Drops:GetChildren()
    local remote = game.ReplicatedStorage.CollectFruit
 
    for index, part in ipairs(fruits) do
        remote:FireServer(part)
    end
end

local function try_complete_obby()
    if (player:GetAttribute("ObbyCooldown") == 0) then
        local root = get_humanoid_root()
        local start = game.Workspace.ObbyParts.RealObbyStartPart
        local finish = game.Workspace.ObbyParts.VictoryPart
 
        -- Fire / Un-Fire the starting point first, then the finish point. (AC Workaround)
        firetouchinterest(root, start, 0)
        firetouchinterest(root, start, 1)
        firetouchinterest(root, finish, 0)
        firetouchinterest(root, finish, 1)
    end
end

local function create_gui()
    local screen_gui = Instance.new("ScreenGui")
    local frame = Instance.new("Frame")
    local list_layout = Instance.new("UIListLayout")
 
    syn.protect_gui(screen_gui)
    screen_gui.Parent = game.CoreGui
    screen_gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    frame.Parent = screen_gui
    frame.AnchorPoint = Vector2.new(1, 1)
    frame.Position = UDim2.new(1, 0, 1, 0)
    frame.Size = UDim2.new(0.1, 0, 0.25, 0)
    list_layout.Parent = frame
    list_layout.SortOrder = Enum.SortOrder.LayoutOrder
 
    user_input_service.InputBegan:Connect(function(input)
        if (input.KeyCode == Enum.KeyCode.RightControl) then
            screen_gui.Enabled = not screen_gui.Enabled
        end
    end)
 
    return(frame)
end

local function add_button(parent, name, callback, interval)
    local button = Instance.new("TextButton")
 
    button.Parent = parent
    button.BackgroundColor3 = Color3.fromRGB(255, 170, 127)
    button.Size = UDim2.new(1, 0, 0.25, 0)
    button.Font = Enum.Font.SourceSans
    button.TextColor3 = Color3.fromRGB(0, 0, 0)
    button.TextSize = 14.000
    button.Text = name
    button:SetAttribute("Active", false)
 
    button.MouseButton1Click:Connect(function()
        button:SetAttribute("Active", (not button:GetAttribute("Active")))
        if (button:GetAttribute("Active")) then
            -- On
            button.BackgroundColor3 = Color3.fromRGB(85, 255, 127)
            -- If a thread isn't running, launch a new thread which calls the callback every interval.
            if not (button:GetAttribute("Debounce")) then
                spawn(function()
                    button:SetAttribute("Debounce", true)
                    while (button:GetAttribute("Active")) do
                        callback()
                        wait(interval)
                    end
                    button:SetAttribute("Debounce", false)
                end)
            end
        else
            -- Off
            button.BackgroundColor3 = Color3.fromRGB(255, 170, 127)
        end
    end)
end

local button_frame = create_gui()
add_button(button_frame, "Auto-Collect", collect_all_fruits, 0.5)
add_button(button_frame, "Auto-Juicer", fire_juice_maker, 10)
add_button(button_frame, "Auto-Upgrade", try_upgrade, 15)
add_button(button_frame, "Auto-Obby", try_complete_obby, 20)
```

## Roblox Hack Distribution

Before we wrap up this course, I want to discuss distribution of your 
scripts. Your first decision to make is whether you want to charge money
 for it or not. If you are aiming to sell a script, you're going to need
 to keep it secure. You will have to implement a whitelist system, 
normally done by hosting an API and having the script communicate with 
it through Synapse's HTTP library. Even if you don't intend to sell your
 script, it may be worth looking into using an obfuscation service like Luraph.
 Rolling your own obfuscator is generally a bad idea, and free ones are 
usually free for a reason. It will help to protect your work from game 
developers monitoring for exploits, and other competing exploit script 
developers.



## Roblox Hack Tutorial Conclusion

With that, we wrap up this course! I've put a lot of work into this 
course, and I'm confident this is the most comprehensive introduction to
 the subject out there. I hope I was successful in teaching you a thing 
or two, and if you have any questions I will be happy to answer them. If
 you can think of anything I missed, or that you'd like to see a 
write-up about in the future, do not hesitate to let me know. Until next
 time.
