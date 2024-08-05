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
-- Get the Players service from the game
local Players = game:GetService("Players")

-- Get the local player (the player running this script)
local LocalPlayer = Players.LocalPlayer

-- Find the "Data" folder in the local player's hierarchy
local Data = LocalPlayer:FindFirstChild("Data")

-- Find the "Beli" value inside the "Data" folder
local Beli = Data:FindFirstChild("Beli")

-- Set the interval for checking the Beli value (in seconds)
local checkInterval = 600

-- Store the initial value of Beli to compare later
local lastBeliValue = Beli.Value

-- Define a function to hop (switch) servers
local function hopServer()
    print("Hopserver")

    -- Get the HttpService to handle HTTP requests
    local Http = game:GetService("HttpService")

    -- Get the TeleportService to teleport players
    local TPS = game:GetService("TeleportService")

    -- Define the API endpoint for retrieving server data
    local Api = "https://games.roblox.com/v1/games/"

    -- Get the current place ID
    local _place = game.PlaceId

    -- Construct the URL to get a list of public servers
    local _servers = Api.._place.."/servers/Public?sortOrder=Asc&limit=100"
    
    -- Define a function to list servers with an optional cursor for pagination
    local function ListServers(cursor)
        -- Make an HTTP GET request to the server list URL with cursor if available
        local Raw = game:HttpGet(_servers .. ((cursor and "&cursor="..cursor) or ""))

        -- Decode the JSON response
        return Http:JSONDecode(Raw)
    end
    
    local Server, Next

    -- Repeat until a server is found
    repeat
        -- Get the list of servers, passing the next page cursor if available
        local Servers = ListServers(Next)

        -- Randomly select a server from the list
        local serverIndex = math.random(1, #Servers.data)
        Server = Servers.data[serverIndex]

        -- Get the next page cursor for further pagination if needed
        Next = Servers.nextPageCursor
    until Server
    
    -- If a server is found, teleport the local player to that server
    if Server then
        TPS:TeleportToPlaceInstance(_place, Server.id, game:GetService('Players').LocalPlayer)
    else
        print("No suitable server found")
    end
end

-- Define a function to check the Beli value
local function checkBeli()
    -- Get the current value of Beli
    local currentBeli = Beli.Value

    -- If the Beli value hasn't changed, hop to a different server
    if currentBeli == lastBeliValue then
        hopServer()
    else
        -- Otherwise, update the lastBeliValue to the current Beli value
        lastBeliValue = currentBeli
    end
end

-- Main loop to continuously check the Beli value at the specified interval
while true do
    -- Wait for the specified check interval
    wait(checkInterval)

    -- Call the checkBeli function to check and possibly hop servers
    checkBeli()
end
```
