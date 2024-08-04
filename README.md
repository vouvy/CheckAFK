<table>
<tr>
<td>
This script is a basic example of the Lua programming language. It was created for personal use, but feel free to use or study it
</td>
</tr>
</table>

---

## Features
- Checks Beli Value: It monitors a value called "Beli" for the local player.
- Server Hopping: If the "Beli" value hasn't changed for a specified interval (600 seconds), it triggers a function to hop to a new server.
- Finds New Server: It uses Roblox’s HTTP service to find a list of available servers and randomly selects one.
- Teleports Player: It teleports the player to the selected server.

## Code Example:
```lua
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Data = LocalPlayer:FindFirstChild("Data")
local Beli = Data:FindFirstChild("Beli")
local checkInterval = 600
local lastBeliValue = Beli.Value

local function hopServer()
    print("Hopserver")
    local Http = game:GetService("HttpService")
    local TPS = game:GetService("TeleportService")
    local Api = "https://games.roblox.com/v1/games/"
    local _place = game.PlaceId
    local _servers = Api.._place.."/servers/Public?sortOrder=Asc&limit=100"
    
    local function ListServers(cursor)
        local Raw = game:HttpGet(_servers .. ((cursor and "&cursor="..cursor) or ""))
        return Http:JSONDecode(Raw)
    end
    
    local Server, Next
    repeat
        local Servers = ListServers(Next)
        local serverIndex = math.random(1, #Servers.data)
        Server = Servers.data[serverIndex]
        Next = Servers.nextPageCursor
    until Server
    
    if Server then
        TPS:TeleportToPlaceInstance(_place, Server.id, game:GetService('Players').LocalPlayer)
    else
        print("No suitable server found")
    end
end

local function checkBeli()
    local currentBeli = Beli.Value
    if currentBeli == lastBeliValue then
        hopServer()
    else
        lastBeliValue = currentBeli
    end
end

while true do
    wait(checkInterval)
    checkBeli()
end
```
