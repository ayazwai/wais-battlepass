Hi, this resource will explain to you how to add rewards, levels and quests to waist-battlepass, how to trigger quests.

# Add Rewards

- Goto config file and open
- Find _Config.Battlepass_
- We decide which membership to add this reward to. We choose the regular or premium member.

Example:
```
    ["normal"] = { -- classic memberships items
        [1] = { -- Level 
            ["level"]    = 1, -- level [INT]
            ["itemname"] = "itemname", [STRING]
            ["amount"]   = 1, -- Give item count [INT]
            ["money"]    = true, -- This item has money? [BOOLEAN]            
            ["car"]      = false, -- This item has car? If the car prize is to be awarded, this variable should be set to true and the money variable should be set to false. [BOOLEAN]
            ["ui"] = {
                ["image"] = "money", -- Show ui image name [STRING]
                ["label"] = "Money $15.000" -- Show ui label [STRING]
            },
        },
    },
```
```
    ["premium"] = { -- premium memberships items
        [1] = { -- Level 
            ["level"]    = 1, -- level [INT]
            ["itemname"] = "itemname", -- Give item name  [STRING]
            ["amount"]   = 25000, -- Give item count [INT]
            ["money"]    = true, -- This item has money? [BOOLEAN]            
            ["car"]      = false, -- This item has car? If the car prize is to be awarded, this variable should be set to true and the money variable should be set to false. [BOOLEAN]
            ["ui"] = {
                ["image"] = "money", -- Show ui image name [STRING]
                ["label"] = "Money $15.000" -- Show ui label [STRING]
            },
        },
    }
```

# Add Levels

- Open config file and find this array _Config.Levels_
- Enter a new level. Only INT chars

Example:
```
    Config.Levels = {
        [1]  = 0,
        [2]  = 500,
        [3]  = 750,
        [4]  = 1000,
        [5]  = 1250,
        [6]  = 1500,
        [7]  = 1750,
        [8]  = 2000,
        [9]  = 2250,
        [10] = 2500,
        [level]  = needxp,
    }
```

# Add missions

- Open config file and find this array _Config.Missions_
- Copy the new task you will add by editing the table below and paste it at the bottom of this array

Example:
```
    ["short title"] = {                             -- mission name [STRING]
        ["title"]       = "Kill Player",            -- Main Tilte [STRING]
        ["information"] = "Lipsum",                 -- Informatıon messages. [STRING]
        ["addxp"]       = 100,                      -- XP will be given to the player when the mission is completed [INT]
        ["needcount"]   = 10,                       -- Total number of advances the player needs [INT]
        ["eventname"]   = "wais:addmissionxp:porance", -- Read the bottom line. [STRING]
    },
```

## Missions event triggers

This event name part is the most important field. A custom event should be created when a new task is added. The event name should not be used by another script before. The task you created is registered as client side when the script is started.
The places where you will trigger this event should be triggered in the successful result of a transaction.

Example:
## Client Side
```
    TriggerEvent("mythic_progbar:client:progress", {
        name = "pickorange",
        duration = 5000,
        label = "Picking Oranges [DEL]",
        useWhileDead = false,
        canCancel = true,
        controlDisables = {
            disableMovement = true,
            disableCarMovement = true,
            disableMouse = false,
            disableCombat = true,
        },
        animation = {
            animDict = "mp_arresting",
            anim = "a_uncuff",
            flags = 49,
        },
    }, function(status)
        if not status then
            TriggerServerEvent("wais:giveOrange")
            ESX.ShowNotification('You have successfully picked the orange.')
            -- When the progbar ends in this line of code, the user collects oranges. You can collect 5 oranges as a task.
            -- The player has collected the orange and now I need to progress in his quest
            -- So I find my orange task in the config and copy the event name and write it like this:
            TriggerEvent('wais:addmissionxp:porance', 1) -- The first argument you add specifies how many progress will be recorded. (For example, if 2 oranges are given to the player at the end of this process, the argument should change to 2.)
        end
    end)
```
## Server Side
```
RegisterNetEvent('wais:giveOrange', function()
    local src = source
    local xPlayer = ESX.GetPlayerFromId(src)
    xPlayer.addInventoryItem('orange', 1)
    -- If you cannot find a place to place this trigger on the client side, you can also do it from the server side.
    TriggerClientEvent('wais:addmissionxp:porance', src, 1) -- The first argument must specify the player. Therefore, we need to specify the source, if you type -1, each player will progress in the mission.
end)
```

# Tebex Settings

If you want to activate the Tebex sale, you need to apply what I said.

## Connecting Tebex to the server

- Create a tebex account, login if you have
- After logging in to your account, let's go to the page called "Game Servers" from the left side of the page.
- Click the "CONNECT GAME SERVER" button at the top right of the page.
- Let's choose the place named "PLUGIN" from the page that comes up and continue.
- Then let's choose your server name and in which packages these sales will be active, but "SELECT ALL" will help you.
- After continuing, it will give you an sv_tebexSecret key on the page that opens in front of you, let's copy it and paste it anywhere in server.cfg, but let's make sure that this code is on the top lines. Then let's start the server.
- When you start the server, the text "NOT CONNECT" on the top right of the page will change to "CONNECT" after a few seconds. After seeing this article, press the continue button and now there is a communication system between your server and tebex.

## Creating a Package Sending News to the Server

We have connected tebex with our server above, and now we will transfer the sales from tebex to the server instantly and make a system where the player can claim it.

- We click on the "PACKAGES" section from the left side and we are directed to the page.
- Then we click on the "ADD NEW" button on the top right of the page and choose the package option.
- On the page that opens before us, we enter the title of the product. This is very important because we will also use the product title you entered in the script, so be careful to write neat and plain text without emojis.
- After entering the other necessary information of the product, "Configure what your customers should receive upon purchasing this package".We select the "+GAME SERVER COMMANDS" option from the section.
- From the drop-down section, we choose the server name that we just created in the above steps. Then click the "ADD COMMAND" button at the bottom.
- After clicking the button, we paste the following code in the "Enter the command to execute on your server" section. This code: ```buypack {"tid": "{transaction}",  "mail": "{email}", "pname": "{packageName}", "price": "{price}"}```
- After pasting the code, click the settings icon on the right. From this section, we select our server in the "Game server to Execute On" section. From the "Require Player To Be Online" section, we select the "Execute the command even if the player is offline" option and create our package.
- Copy the product name you created. Find the TebexPack array and create a new table in it as follows: 
```
If this product is level:
    ["Package name"] = {"level", --[[dont touch!]] "level0" --[[In this section, you must specify which level you are selling. For example, if you sold 10 level boosts, this will be level10.]] }
    --level 5, level 10 etc. the names come from this table Config.Level Boost
If this product is a membership
    ["Package name"] = {"premium", --[[dont touch!]] "premium" --[[dont touch!]] },
```

Now when a product is sold, it will be forwarded to your server.

## How will the player get the package after this purchase?

There is a variable in _Config.TebexSettings_, _PlayerClaimTebexCodeCommand_ is a game code written inside this variable and it will claim with it.
In game command: /purchase tbx-referance-id

# CONTACT

If there is a problem or you want to ask a question Discord: Ayazwai#3900 or [Discord 0RESMON](discord.gg/0resmon)
