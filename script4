local feUI = loadstring(game:HttpGet("https://raw.githubusercontent.com/formidy/feUI/refs/heads/main/main.lua"))()

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local Teams = game:GetService("Teams")

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

local GunRemotes = ReplicatedStorage:WaitForChild("GunRemotes")
local ShootEvent = GunRemotes:WaitForChild("ShootEvent")
local ReloadFunc = GunRemotes:WaitForChild("FuncReload")
local MeleeEvent = ReplicatedStorage:WaitForChild("meleeEvent")
local Remote = Workspace:WaitForChild("Remote")
local ItemHandler = Remote:WaitForChild("ItemHandler")
local TeamEvent = Remote:WaitForChild("TeamEvent")
local ArrestRemote = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("ArrestPlayer")

local currentGun = nil
local gunStats = nil
local autoShootEnabled = false
local autoShootConnection = nil
local targetedPlayer = nil
local shotPlayers = {}
local deathPosition = nil
local isReloading = false

local killAuraEnabled = false
local killAuraConnection = nil
local killAllActive = false
local loopKillAllActive = false

local arrestAllActive = false
local loopArrestAllActive = false
local loopArrestTarget = nil
local loopArrestActive = false
local loopArrestConnection = nil

local noclipEnabled = false
local noclipConnection = nil
local walkspeedEnabled = false
local walkspeedConnection = nil

local espEnabled = false
local tracersEnabled = false
local espObjects = {}
local tracerObjects = {}

local ignoredPlayers = {}
local ignoredTeams = {}
local targetSpecificPlayer = nil
local targetSpecificTeams = {}
local loopKillTarget = nil
local loopKillActive = false
local loopKillConnection = nil

local godmodeEnabled = false
local godmodeConnection = nil
local respawnConnection = nil

local raycastParams = RaycastParams.new()
raycastParams.CollisionGroup = "ClientBullet"
raycastParams.FilterType = Enum.RaycastFilterType.Exclude

local gunLocations = {
    ["Remington 870"] = CFrame.new(820.0814819335938, 100.59066772460938, 2217.83740234375),
    ["M9"] = CFrame.new(813.3773193359375, 100.73532104492188, 2217.36279296875),
    ["AK-47"] = CFrame.new(-937.3514404296875, 94.16865539550781, 2049.382080078125)
}

local quickTeleports = {
    ["Guard Weapons Room"] = Vector3.new(829.599609375, 99.97666931152344, 2239.587646484375),
    ["Guard Room"] = Vector3.new(828.6632690429688, 99.989990234375, 2282.14794921875),
    ["Guard Break Room"] = Vector3.new(767.1735229492188, 99.989990234375, 2227.497802734375),
    ["Guard Office Cubical"] = Vector3.new(854.9359741210938, 99.98999786376953, 2266.459716796875),
    ["Prison Yard"] = Vector3.new(808.11572265625, 97.99993896484375, 2475.045654296875),
    ["Prison Yard Tower"] = Vector3.new(824.5584106445312, 130.0399932861328, 2572.764892578125),
    ["Prison End of Hallway"] = Vector3.new(977.7395629882812, 99.98999786376953, 2363.099853515625),
    ["Prison Hallway Entrance"] = Vector3.new(983.260498046875, 99.98999786376953, 2300.717041015625),
    ["Prison Kitchen"] = Vector3.new(923.7960815429688, 99.98995208740234, 2224.975341796875),
    ["Prison Cafeteria"] = Vector3.new(906.5176391601562, 99.98995208740234, 2298.1455078125),
    ["Prison Cellblock"] = Vector3.new(917.4382934570312, 99.98998260498047, 2447.181396484375),
    ["Prison Security Checkpoint"] = Vector3.new(755.7481079101562, 99.98999786376953, 2312.918212890625),
    ["Prison Entrance"] = Vector3.new(656.0863037109375, 99.98999786376953, 2273.043701171875),
    ["Prison Gate Entrance"] = Vector3.new(488.4753723144531, 98.03993225097656, 2217.425048828125),
    ["Prison Gate Guard Room"] = Vector3.new(504.82391357421875, 102.0399169921875, 2252.72216796875),
    ["Prison Gate Wall"] = Vector3.new(504.25225830078125, 125.03993225097656, 2314.994384765625),
    ["Prison Guard Car Spawn"] = Vector3.new(637.7882690429688, 98.20001983642578, 2493.134765625),
    ["Criminal Base Inside"] = Vector3.new(-941.0030517578125, 94.12877655029297, 2056.3828125),
    ["Criminal Base Outside"] = Vector3.new(-864.29833984375, 94.47604370117188, 2057.989990234375),
    ["Criminal Car Spawn"] = Vector3.new(-909.4756469726562, 95.12728881835938, 2149.561767578125),
    ["Criminal Base Secret #1"] = Vector3.new(-939.6616821289062, 94.12877655029297, 1990.828369140625),
    ["Criminal Base Secret #2"] = Vector3.new(-939.5564575195312, 94.12877655029297, 1918.9310302734375),
    ["Supermarket Parkinglot"] = Vector3.new(438.47943115234375, 11.425362586975098, 1215.2066650390625),
    ["Gas Station"] = Vector3.new(-481.6697998046875, 54.39378356933594, 1628.049560546875),
    ["Gas Station Indoors"] = Vector3.new(-567.0712890625, 54.59358215332031, 1651.441650390625),
    ["Hilltop Grocery Store"] = Vector3.new(-461.52630615234375, 54.17503356933594, 1693.8529052734375),
    ["Hilltop Grocery Store Indoors"] = Vector3.new(-405.55303955078125, 54.20008087158203, 1764.185546875)
}

local criminalTeleportPos = Vector3.new(-975.715576171875, 109.32378387451172, 2070.5771484375)

local Window = feUI:CreateWindow({
    Title = "Prison Life",
    Subtitle = "is ts ud",
    Size = UDim2.new(0, 580, 0, 480),
    Style = "modern",
    Theme = "midnight",
    AutoSave = true
})

local TargetingTab = Window:CreateTab("Player Target")
local CombatTab = Window:CreateTab("Combat")
local MeleeTab = Window:CreateTab("Melee")
local ArrestTab = Window:CreateTab("Arrest")
local CharacterTab = Window:CreateTab("Character")
local VisualTab = Window:CreateTab("Visuals")
local TeleportTab = Window:CreateTab("Teleport")
local TeamTab = Window:CreateTab("Teams")
local GunTab = Window:CreateTab("Guns")

local function Refresh(Team, Position)
    if typeof(Position):lower() == "position" then 
        Position = CFrame.new(Position) 
    end
    
    local LastPosition = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character.HumanoidRootPart.CFrame or CFrame.new(0, 100, 0)
    
    if not Team then 
        Team = LocalPlayer.Team 
    end
    
    local done = false
    
    if respawnConnection then
        respawnConnection:Disconnect()
    end
    
    respawnConnection = LocalPlayer.CharacterAdded:Connect(function(char)
        if done then return end
        done = true
        
        task.spawn(function()
            Workspace.CurrentCamera.CameraType = Enum.CameraType.Scriptable
            wait(0.1)
            Workspace.CurrentCamera.CameraType = Enum.CameraType.Custom
            Workspace.CurrentCamera.CameraSubject = char:WaitForChild("Humanoid")
        end)
        
        task.spawn(function()
            local ff = char:WaitForChild("ForceField", 5)
            if ff then
                ff:Destroy()
            end
        end)
        
        local hrp = char:WaitForChild("HumanoidRootPart")
        for i = 1, 3 do
            hrp.CFrame = LastPosition
            RunService.Stepped:Wait()
        end
        
        if respawnConnection then
            respawnConnection:Disconnect()
            respawnConnection = nil
        end
    end)
    
    if Team ~= Teams.Criminals then
        TeamEvent:FireServer(Team.TeamColor.Name)
    else
        repeat
            RunService.Stepped:Wait()
            if firetouchinterest then
                firetouchinterest(LocalPlayer.Character:FindFirstChildOfClass("Part"), Workspace["Criminals Spawn"]:GetChildren()[1], 0)
                firetouchinterest(LocalPlayer.Character:FindFirstChildOfClass("Part"), Workspace["Criminals Spawn"]:GetChildren()[1], 1)
            end
            Workspace["Criminals Spawn"]:GetChildren()[1].Transparency = 1
            Workspace["Criminals Spawn"]:GetChildren()[1].CanCollide = false
            Workspace["Criminals Spawn"]:GetChildren()[1].CFrame = LastPosition
        until LocalPlayer.Team == Teams.Criminals
        Workspace["Criminals Spawn"]:GetChildren()[1].CFrame = CFrame.new(0, 3125, 0)
    end
    
    return nil
end

local function setupGodmode()
    if godmodeEnabled then
        if godmodeConnection then
            godmodeConnection:Disconnect()
        end
        
        local character = LocalPlayer.Character
        if character then
            local humanoid = character:FindFirstChild("Humanoid")
            if humanoid then
                humanoid.BreakJointsOnDeath = false
                
                godmodeConnection = humanoid.HealthChanged:Connect(function(health)
                    if godmodeEnabled and health <= 5 then
                        local currentPos = character:FindFirstChild("HumanoidRootPart") and character.HumanoidRootPart.CFrame
                        Refresh(LocalPlayer.Team, currentPos)
                    end
                end)
            end
        end
    else
        if godmodeConnection then
            godmodeConnection:Disconnect()
            godmodeConnection = nil
        end
        
        local character = LocalPlayer.Character
        if character then
            local humanoid = character:FindFirstChild("Humanoid")
            if humanoid then
                humanoid.BreakJointsOnDeath = true
            end
        end
    end
end

local function calculateSpread(origin, target, spread, range)
    local distance = (origin - target).Magnitude / spread
    local offsetX = math.random(-distance, distance) / 10
    local offsetY = math.random(-distance, distance) / 10
    local offsetZ = math.random(-distance, distance) / 10
    
    local direction = (target + Vector3.new(offsetX, offsetY, offsetZ) - origin).Unit * range
    local result = workspace:Raycast(origin, direction, raycastParams)
    
    local hitPart = result and result.Instance or nil
    local hitPos = result and result.Position or origin + direction
    
    return hitPart, hitPos
end

local function getGunOrigin()
    if not currentGun then return nil end
    
    local character = LocalPlayer.Character
    if not character then return nil end
    
    local head = character:FindFirstChild("Head")
    if not head then return nil end
    
    return head.Position
end

local function getPlayerPosition()
    local character = LocalPlayer.Character
    if not character then return nil end
    
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
    
    return hrp.Position
end

local function isPlayerAlive(player)
    if not player or not player.Character then return false end
    
    local humanoid = player.Character:FindFirstChild("Humanoid")
    if not humanoid then return false end
    
    return humanoid.Health > 0
end

local function isPlayerIgnored(player)
    if ignoredPlayers[player.Name] then
        return true
    end
    
    if player.Team and ignoredTeams[player.Team.Name] then
        return true
    end
    
    return false
end

local function shouldTargetPlayer(player)
    if #targetSpecificTeams > 0 then
        if player.Team then
            for _, teamName in pairs(targetSpecificTeams) do
                if player.Team.Name == teamName then
                    return true
                end
            end
        end
        return false
    end
    
    return true
end

local function hasBeenShot(player)
    if not Window:GetFlag("one_shot_mode") then
        return false
    end
    
    if currentGun and currentGun.Name == "Remington 870" then
        return shotPlayers[player.UserId] == true
    end
    
    return false
end

local function markAsShot(player)
    if currentGun and currentGun.Name == "Remington 870" then
        shotPlayers[player.UserId] = true
    end
end

local function getClosestPlayer()
    if targetSpecificPlayer and isPlayerAlive(targetSpecificPlayer) and not isPlayerIgnored(targetSpecificPlayer) then
        return targetSpecificPlayer
    end
    
    local range = Window:GetFlag("shoot_range") or 100
    local teamCheck = Window:GetFlag("team_check") or false
    local visibilityCheck = Window:GetFlag("visibility_check") or false
    
    local origin = getGunOrigin()
    if not origin then return nil end
    
    local closestPlayer = nil
    local closestDistance = range
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if isPlayerIgnored(player) then
                continue
            end
            
            if not shouldTargetPlayer(player) then
                continue
            end
            
            if teamCheck and player.Team == LocalPlayer.Team then
                continue
            end
            
            if not isPlayerAlive(player) then
                continue
            end
            
            if hasBeenShot(player) then
                continue
            end
            
            local character = player.Character
            if character then
                local humanoid = character:FindFirstChild("Humanoid")
                local head = character:FindFirstChild("Head")
                
                if humanoid and head and humanoid.Health > 0 then
                    local distance = (head.Position - origin).Magnitude
                    
                    if distance <= range and distance < closestDistance then
                        if visibilityCheck then
                            local direction = (head.Position - origin).Unit * distance
                            local rayResult = workspace:Raycast(origin, direction, raycastParams)
                            
                            if rayResult and rayResult.Instance then
                                local hitCharacter = rayResult.Instance:FindFirstAncestorOfClass("Model")
                                if hitCharacter ~= character then
                                    continue
                                end
                            end
                        end
                        
                        closestPlayer = player
                        closestDistance = distance
                    end
                end
            end
        end
    end
    
    return closestPlayer
end

local function getPlayersInMeleeRange()
    local meleeRange = Window:GetFlag("melee_range") or 15
    local teamCheck = Window:GetFlag("melee_team_check") or false
    
    local myPos = getPlayerPosition()
    if not myPos then return {} end
    
    local playersInRange = {}
    
    if targetSpecificPlayer and isPlayerAlive(targetSpecificPlayer) and not isPlayerIgnored(targetSpecificPlayer) then
        local character = targetSpecificPlayer.Character
        if character then
            local hrp = character:FindFirstChild("HumanoidRootPart")
            if hrp then
                local distance = (hrp.Position - myPos).Magnitude
                if distance <= meleeRange then
                    table.insert(playersInRange, targetSpecificPlayer)
                    return playersInRange
                end
            end
        end
    end
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if isPlayerIgnored(player) then
                continue
            end
            
            if not shouldTargetPlayer(player) then
                continue
            end
            
            if teamCheck and player.Team == LocalPlayer.Team then
                continue
            end
            
            if not isPlayerAlive(player) then
                continue
            end
            
            local character = player.Character
            if character then
                local hrp = character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    local distance = (hrp.Position - myPos).Magnitude
                    if distance <= meleeRange then
                        table.insert(playersInRange, player)
                    end
                end
            end
        end
    end
    
    return playersInRange
end

local function attackPlayer(player)
    if not player then return end
    MeleeEvent:FireServer(player)
end

local function grabHandcuffs()
    if LocalPlayer.Backpack:FindFirstChild("Handcuffs") then
       LocalPlayer.Backpack:FindFirstChild("Handcuffs").Parent = LocalPlayer.Character
    end
end

local function arrestPlayer(player)
    if not player then return false end
    
    pcall(function()
        ArrestRemote:InvokeServer(player)
    end)
    
    return true
end

local function isPlayerArrested(player)
    if not player or not player.Character then return false end
    
    local humanoid = player.Character:FindFirstChild("Humanoid")
    if not humanoid then return false end
    
    return humanoid.WalkSpeed <= 0 or player.Team == Teams.Inmates
end

local function killAuraLoop()
    if not killAuraEnabled then return end
    
    local targets = getPlayersInMeleeRange()
    
    if #targets > 0 then
        for _, player in pairs(targets) do
            if isPlayerAlive(player) then
                attackPlayer(player)
            end
        end
    end
end

local function teleportToPlayer(player)
    local character = LocalPlayer.Character
    if not character then return false end
    
    local myHRP = character:FindFirstChild("HumanoidRootPart")
    if not myHRP then return false end
    
    if not player.Character then return false end
    local targetHRP = player.Character:FindFirstChild("HumanoidRootPart")
    if not targetHRP then return false end
    
    myHRP.CFrame = targetHRP.CFrame * CFrame.new(0, 0, 3)
    return true
end

local function getAlivePlayers()
    local alivePlayers = {}
    local teamCheck = Window:GetFlag("melee_team_check") or false
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if isPlayerIgnored(player) then
                continue
            end
            
            if not shouldTargetPlayer(player) then
                continue
            end
            
            if teamCheck and player.Team == LocalPlayer.Team then
                continue
            end
            
            if isPlayerAlive(player) then
                table.insert(alivePlayers, player)
            end
        end
    end
    
    return alivePlayers
end

local function getArrestableTeamPlayers(teamName)
    local players = {}
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if player.Team and player.Team.Name == teamName then
                if isPlayerAlive(player) and not isPlayerArrested(player) then
                    table.insert(players, player)
                end
            end
        end
    end
    
    return players
end

local arrestAllRenderConnection = nil

local function arrestPlayerNow(player)
    if not player or not isPlayerAlive(player) then return end
    
    task.spawn(function()
        grabHandcuffs()
        task.wait(0.2)
        
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "ArrestPlatform"
        platform.Parent = workspace
        
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
        end
        
        local arrestConnection = RunService.RenderStepped:Connect(function()
            if isPlayerArrested(player) or not isPlayerAlive(player) then
                return
            end
            
            local myChar = LocalPlayer.Character
            if not myChar then return end
            
            local myHRP = myChar:FindFirstChild("HumanoidRootPart")
            if not myHRP then return end
            
            local targetChar = player.Character
            if targetChar then
                local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                if targetHRP then
                    local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                    local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                    
                    myHRP.CFrame = targetCFrame
                    platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                    
                    arrestPlayer(player)
                end
            end
        end)
        
        while not isPlayerArrested(player) and isPlayerAlive(player) do
            task.wait(0.1)
        end
        
        if arrestConnection then
            arrestConnection:Disconnect()
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
    end)
end

local function loopArrestPlayer(player)
    if not player then return end
    
    loopArrestTarget = player
    loopArrestActive = true
    
    task.spawn(function()
        grabHandcuffs()
        task.wait(0.2)
        
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "LoopArrestPlatform"
        platform.Parent = workspace
        
        while loopArrestActive and loopArrestTarget == player do
            if not isPlayerAlive(player) then
                task.wait(2)
                continue
            end
            
            if isPlayerArrested(player) then
                task.wait(2)
                continue
            end
            
            if loopArrestConnection then
                loopArrestConnection:Disconnect()
            end
            
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
            end
            
            loopArrestConnection = RunService.RenderStepped:Connect(function()
                if isPlayerArrested(player) or not isPlayerAlive(player) or not loopArrestActive or loopArrestTarget ~= player then
                    if loopArrestConnection then
                        loopArrestConnection:Disconnect()
                        loopArrestConnection = nil
                    end
                    return
                end
                
                local myChar = LocalPlayer.Character
                if not myChar then return end
                
                local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                if not myHRP then return end
                
                local targetChar = player.Character
                if targetChar then
                    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                        local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                        
                        myHRP.CFrame = targetCFrame
                        platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                        
                        arrestPlayer(player)
                    end
                end
            end)
            
            while not isPlayerArrested(player) and isPlayerAlive(player) and loopArrestActive and loopArrestTarget == player do
                task.wait(0.1)
            end
            
            if loopArrestConnection then
                loopArrestConnection:Disconnect()
                loopArrestConnection = nil
            end
            
            task.wait(0.5)
        end
        
        if loopArrestConnection then
            loopArrestConnection:Disconnect()
            loopArrestConnection = nil
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
        
        loopArrestActive = false
        loopArrestTarget = nil
    end)
end

local function arrestAll()
    if arrestAllActive then return end
    
    arrestAllActive = true
    
    task.spawn(function()
        grabHandcuffs()
        task.wait(0.2)
        
        local targets = getAlivePlayers()
        
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "ArrestAllPlatform"
        platform.Parent = workspace
        
        for i, player in pairs(targets) do
            if not arrestAllActive then break end
            
            if arrestAllRenderConnection then
                arrestAllRenderConnection:Disconnect()
            end
            
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
            end
            
            arrestAllRenderConnection = RunService.RenderStepped:Connect(function()
                if isPlayerArrested(player) or not isPlayerAlive(player) or not arrestAllActive then
                    if arrestAllRenderConnection then
                        arrestAllRenderConnection:Disconnect()
                        arrestAllRenderConnection = nil
                    end
                    return
                end
                
                local myChar = LocalPlayer.Character
                if not myChar then return end
                
                local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                if not myHRP then return end
                
                local targetChar = player.Character
                if targetChar then
                    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                        local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                        
                        myHRP.CFrame = targetCFrame
                        platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                        
                        arrestPlayer(player)
                    end
                end
            end)
            
            while not isPlayerArrested(player) and isPlayerAlive(player) and arrestAllActive do
                task.wait(0.1)
            end
            
            if arrestAllRenderConnection then
                arrestAllRenderConnection:Disconnect()
                arrestAllRenderConnection = nil
            end
            
            task.wait(0.3)
        end
        
        if arrestAllRenderConnection then
            arrestAllRenderConnection:Disconnect()
            arrestAllRenderConnection = nil
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
        
        arrestAllActive = false
    end)
end

local function loopArrestAll()
    task.spawn(function()
        grabHandcuffs()
        task.wait(0.2)
        
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "LoopArrestAllPlatform"
        platform.Parent = workspace
        
        while loopArrestAllActive do
            local targets = getAlivePlayers()
            
            local unarrestedTargets = {}
            for _, player in pairs(targets) do
                if not isPlayerArrested(player) then
                    table.insert(unarrestedTargets, player)
                end
            end
            
            if #unarrestedTargets == 0 then
                task.wait(2)
                continue
            end
            
            for i, player in pairs(unarrestedTargets) do
                if not loopArrestAllActive then break end
                
                if arrestAllRenderConnection then
                    arrestAllRenderConnection:Disconnect()
                end
                
                if player.Character and player.Character:FindFirstChild("Humanoid") then
                    workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
                end
                
                arrestAllRenderConnection = RunService.RenderStepped:Connect(function()
                    if isPlayerArrested(player) or not isPlayerAlive(player) or not loopArrestAllActive then
                        if arrestAllRenderConnection then
                            arrestAllRenderConnection:Disconnect()
                            arrestAllRenderConnection = nil
                        end
                        return
                    end
                    
                    local myChar = LocalPlayer.Character
                    if not myChar then return end
                    
                    local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                    if not myHRP then return end
                    
                    local targetChar = player.Character
                    if targetChar then
                        local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                        if targetHRP then
                            local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                            local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                            
                            myHRP.CFrame = targetCFrame
                            platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                            
                            arrestPlayer(player)
                        end
                    end
                end)
                
                while not isPlayerArrested(player) and isPlayerAlive(player) and loopArrestAllActive do
                    task.wait(0.1)
                end
                
                if arrestAllRenderConnection then
                    arrestAllRenderConnection:Disconnect()
                    arrestAllRenderConnection = nil
                end
                
                task.wait(0.3)
            end
            
            task.wait(1)
        end
        
        if arrestAllRenderConnection then
            arrestAllRenderConnection:Disconnect()
            arrestAllRenderConnection = nil
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
    end)
end

local function arrestTeam(teamName)
    task.spawn(function()
        grabHandcuffs()
        task.wait(0.2)
        
        local targets = getArrestableTeamPlayers(teamName)
        
        if #targets == 0 then
            feUI:Notify({
                Title = "No Targets",
                Content = "No arrestable players on " .. teamName,
                Type = "warning"
            })
            return
        end
        
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "TeamArrestPlatform"
        platform.Parent = workspace
        
        for i, player in pairs(targets) do
            if arrestAllRenderConnection then
                arrestAllRenderConnection:Disconnect()
            end
            
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
            end
            
            arrestAllRenderConnection = RunService.RenderStepped:Connect(function()
                if isPlayerArrested(player) or not isPlayerAlive(player) then
                    if arrestAllRenderConnection then
                        arrestAllRenderConnection:Disconnect()
                        arrestAllRenderConnection = nil
                    end
                    return
                end
                
                local myChar = LocalPlayer.Character
                if not myChar then return end
                
                local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                if not myHRP then return end
                
                local targetChar = player.Character
                if targetChar then
                    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                        local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                        
                        myHRP.CFrame = targetCFrame
                        platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                        
                        arrestPlayer(player)
                    end
                end
            end)
            
            while not isPlayerArrested(player) and isPlayerAlive(player) do
                task.wait(0.1)
            end
            
            if arrestAllRenderConnection then
                arrestAllRenderConnection:Disconnect()
                arrestAllRenderConnection = nil
            end
            
            task.wait(0.3)
        end
        
        if arrestAllRenderConnection then
            arrestAllRenderConnection:Disconnect()
            arrestAllRenderConnection = nil
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
        
        feUI:Notify({
            Title = "Team Arrest Complete",
            Content = "Arrested all " .. teamName .. " players",
            Type = "success"
        })
    end)
end

local killAllRenderConnection = nil

local function killAll()
    if killAllActive then return end
    
    killAllActive = true
    
    local wasKillAuraEnabled = killAuraEnabled
    if not killAuraEnabled then
        Window:SetFlag("kill_aura", true, true)
    end
    
    task.spawn(function()
        local targets = getAlivePlayers()
        
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "KillAllPlatform"
        platform.Parent = workspace
        
        for i, player in pairs(targets) do
            if not killAllActive then break end
            
            if killAllRenderConnection then
                killAllRenderConnection:Disconnect()
            end
            
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
            end
            
            killAllRenderConnection = RunService.RenderStepped:Connect(function()
                if not isPlayerAlive(player) or not killAllActive then
                    if killAllRenderConnection then
                        killAllRenderConnection:Disconnect()
                        killAllRenderConnection = nil
                    end
                    return
                end
                
                local myChar = LocalPlayer.Character
                if not myChar then return end
                
                local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                if not myHRP then return end
                
                local targetChar = player.Character
                if targetChar then
                    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                        local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                        
                        myHRP.CFrame = targetCFrame
                        
                        platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                        
                        for _ = 1, 3 do
                            attackPlayer(player)
                        end
                    end
                end
            end)
            
            while isPlayerAlive(player) and killAllActive do
                task.wait(0.1)
            end
            
            if killAllRenderConnection then
                killAllRenderConnection:Disconnect()
                killAllRenderConnection = nil
            end
            
            task.wait(0.3)
        end
        
        if killAllRenderConnection then
            killAllRenderConnection:Disconnect()
            killAllRenderConnection = nil
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
        
        killAllActive = false
        
        if not wasKillAuraEnabled then
            Window:SetFlag("kill_aura", false, true)
        end
    end)
end

local function loopKillAll()
    task.spawn(function()
        local wasKillAuraEnabled = killAuraEnabled
        if not killAuraEnabled then
            Window:SetFlag("kill_aura", true, true)
        end
        
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "KillAllPlatform"
        platform.Parent = workspace
        
        while loopKillAllActive do
            local targets = getAlivePlayers()
            
            if #targets == 0 then
                task.wait(2)
                continue
            end
            
            for i, player in pairs(targets) do
                if not loopKillAllActive then break end
                
                if killAllRenderConnection then
                    killAllRenderConnection:Disconnect()
                end
                
                if player.Character and player.Character:FindFirstChild("Humanoid") then
                    workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
                end
                
                killAllRenderConnection = RunService.RenderStepped:Connect(function()
                    if not isPlayerAlive(player) or not loopKillAllActive then
                        if killAllRenderConnection then
                            killAllRenderConnection:Disconnect()
                            killAllRenderConnection = nil
                        end
                        return
                    end
                    
                    local myChar = LocalPlayer.Character
                    if not myChar then return end
                    
                    local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                    if not myHRP then return end
                    
                    local targetChar = player.Character
                    if targetChar then
                        local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                        if targetHRP then
                            local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                            local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                            
                            myHRP.CFrame = targetCFrame
                            
                            platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                            
                            for _ = 1, 3 do
                                attackPlayer(player)
                            end
                        end
                    end
                end)
                
                while isPlayerAlive(player) and loopKillAllActive do
                    task.wait(0.1)
                end
                
                if killAllRenderConnection then
                    killAllRenderConnection:Disconnect()
                    killAllRenderConnection = nil
                end
                
                task.wait(0.3)
            end
            
            task.wait(1)
        end
        
        if killAllRenderConnection then
            killAllRenderConnection:Disconnect()
            killAllRenderConnection = nil
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
        
        if not wasKillAuraEnabled then
            Window:SetFlag("kill_aura", false, true)
        end
    end)
end

local function loopKillPlayer(player)
    if not player then return end
    
    loopKillTarget = player
    loopKillActive = true
    
    local wasKillAuraEnabled = killAuraEnabled
    if not killAuraEnabled then
        Window:SetFlag("kill_aura", true, true)
    end
    
    task.spawn(function()
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "LoopKillPlatform"
        platform.Parent = workspace
        
        while loopKillActive and loopKillTarget == player do
            if not isPlayerAlive(player) then
                task.wait(2)
                continue
            end
            
            if loopKillConnection then
                loopKillConnection:Disconnect()
            end
            
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
            end
            
            loopKillConnection = RunService.RenderStepped:Connect(function()
                if not isPlayerAlive(player) or not loopKillActive or loopKillTarget ~= player then
                    if loopKillConnection then
                        loopKillConnection:Disconnect()
                        loopKillConnection = nil
                    end
                    return
                end
                
                local myChar = LocalPlayer.Character
                if not myChar then return end
                
                local myHRP = myChar:FindFirstChild("HumanoidRootPart")
                if not myHRP then return end
                
                local targetChar = player.Character
                if targetChar then
                    local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                    if targetHRP then
                        local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                        local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                        
                        myHRP.CFrame = targetCFrame
                        
                        platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                        
                        for _ = 1, 3 do
                            attackPlayer(player)
                        end
                    end
                end
            end)
            
            while isPlayerAlive(player) and loopKillActive and loopKillTarget == player do
                task.wait(0.1)
            end
            
            if loopKillConnection then
                loopKillConnection:Disconnect()
                loopKillConnection = nil
            end
            
            task.wait(0.5)
        end
        
        if loopKillConnection then
            loopKillConnection:Disconnect()
            loopKillConnection = nil
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
        
        if not wasKillAuraEnabled then
            Window:SetFlag("kill_aura", false, true)
        end
        
        loopKillActive = false
        loopKillTarget = nil
    end)
end

local function killPlayer(player)
    if not player or not isPlayerAlive(player) then return end
    
    local wasKillAuraEnabled = killAuraEnabled
    if not killAuraEnabled then
        Window:SetFlag("kill_aura", true, true)
    end
    
    task.spawn(function()
        local platform = Instance.new("Part")
        platform.Size = Vector3.new(10, 1, 10)
        platform.Anchored = true
        platform.CanCollide = true
        platform.Material = Enum.Material.ForceField
        platform.Transparency = 0.5
        platform.Name = "KillPlatform"
        platform.Parent = workspace
        
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = player.Character.Humanoid
        end
        
        local killConnection = RunService.RenderStepped:Connect(function()
            if not isPlayerAlive(player) then
                return
            end
            
            local myChar = LocalPlayer.Character
            if not myChar then return end
            
            local myHRP = myChar:FindFirstChild("HumanoidRootPart")
            if not myHRP then return end
            
            local targetChar = player.Character
            if targetChar then
                local targetHRP = targetChar:FindFirstChild("HumanoidRootPart")
                if targetHRP then
                    local underPosition = targetHRP.Position - Vector3.new(0, 8, 0)
                    local targetCFrame = CFrame.new(underPosition) * CFrame.Angles(0, math.rad(90), math.rad(90))
                    
                    myHRP.CFrame = targetCFrame
                    
                    platform.CFrame = CFrame.new(underPosition - Vector3.new(0, 3, 0))
                    
                    for _ = 1, 3 do
                        attackPlayer(player)
                    end
                end
            end
        end)
        
        while isPlayerAlive(player) do
            task.wait(0.1)
        end
        
        if killConnection then
            killConnection:Disconnect()
        end
        
        if platform and platform.Parent then
            platform:Destroy()
        end
        
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
        end
        
        if not wasKillAuraEnabled then
            Window:SetFlag("kill_aura", false, true)
        end
    end)
end

local function needsReload()
    if not currentGun then return false end
    return currentGun:GetAttribute("Local_CurrentAmmo") <= 0
end

local function isGunReloading()
    if not currentGun then return false end
    return currentGun:GetAttribute("Local_ReloadSession") > 0
end

local function reloadGun()
    if not currentGun or isReloading or isGunReloading() then return end
    
    isReloading = true
    
    task.spawn(function()
        local result = ReloadFunc:InvokeServer()
        task.wait(gunStats.ReloadTime or 2)
        isReloading = false
    end)
end

local function shootAtPosition(targetPos)
    if not currentGun or not gunStats then
        return false
    end
    
    if needsReload() then
        if Window:GetFlag("auto_reload") then
            reloadGun()
        end
        return false
    end
    
    if isGunReloading() or isReloading then
        return false
    end
    
    local origin = getGunOrigin()
    if not origin then return false end
    
    local projectileCount = gunStats.ProjectileCount or 1
    local projectiles = {}
    
    for _ = 1, projectileCount do
        local hitPart, hitPos = calculateSpread(
            origin,
            targetPos,
            gunStats.Spread or 10,
            gunStats.Range or 1000
        )
        table.insert(projectiles, {origin, hitPos, hitPart})
    end
    
    ShootEvent:FireServer(projectiles)
    
    local newAmmo = currentGun:GetAttribute("Local_CurrentAmmo") - 1
    currentGun:SetAttribute("Local_CurrentAmmo", newAmmo)
    
    return true
end

local function autoShootLoop()
    if not autoShootEnabled then return end
    
    targetedPlayer = getClosestPlayer()
    
    if targetedPlayer then
        if not isPlayerAlive(targetedPlayer) then
            targetedPlayer = nil
            return
        end
        
        local character = targetedPlayer.Character
        if character then
            local targetPart = character:FindFirstChild(Window:GetFlag("target_part") or "Head")
            
            if targetPart then
                local success = shootAtPosition(targetPart.Position)
                
                if success then
                    markAsShot(targetedPlayer)
                end
            end
        end
    end
end

local function updateGunReference()
    local character = LocalPlayer.Character
    if not character then return end
    
    for _, tool in pairs(character:GetChildren()) do
        if tool:IsA("Tool") and tool:GetAttribute("ToolType") == "Gun" then
            currentGun = tool
            gunStats = tool:GetAttributes()
            
            raycastParams.FilterDescendantsInstances = {character}
            return
        end
    end
    
    currentGun = nil
    gunStats = nil
end

local function getAvailableGuns()
    local character = LocalPlayer.Character
    if not character then return {} end
    
    local backpack = LocalPlayer:WaitForChild("Backpack")
    local guns = {}
    
    for _, tool in pairs(backpack:GetChildren()) do
        if tool:IsA("Tool") and tool:GetAttribute("ToolType") == "Gun" then
            table.insert(guns, tool)
        end
    end
    
    for _, tool in pairs(character:GetChildren()) do
        if tool:IsA("Tool") and tool:GetAttribute("ToolType") == "Gun" then
            table.insert(guns, tool)
        end
    end
    
    return guns
end

local function equipGun(gun)
    if not gun then return false end
    
    local character = LocalPlayer.Character
    if not character then return false end
    
    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid then return false end
    
    if gun.Parent == LocalPlayer.Backpack then
        humanoid:EquipTool(gun)
        return true
    end
    
    return false
end

local function getGunWithAmmo()
    local guns = getAvailableGuns()
    
    for _, gun in pairs(guns) do
        local ammo = gun:GetAttribute("CurrentAmmo") or 0
        if ammo > 0 then
            return gun
        end
    end
    
    return nil
end

local function autoSwitchGun()
    if not Window:GetFlag("auto_switch") then return false end
    
    if currentGun and currentGun:GetAttribute("Local_CurrentAmmo") > 0 then
        return false
    end
    
    local newGun = getGunWithAmmo()
    if newGun then
        equipGun(newGun)
        return true
    end
    
    return false
end

local function grabGunRemote(gunName)
    local gunGiver = Workspace:WaitForChild("Prison_ITEMS"):WaitForChild("giver"):FindFirstChild(gunName)
    if gunGiver then
        local pickup = gunGiver:FindFirstChild("ITEMPICKUP")
        if pickup then
            ItemHandler:InvokeServer(pickup)
            return true
        end
    end
    return false
end

local function grabGunTeleport(gunName)
    local character = LocalPlayer.Character
    if not character then return false end
    
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then return false end
    
    local gunCFrame = gunLocations[gunName]
    if not gunCFrame then return false end
    
    local originalPos = hrp.CFrame
    
    hrp.CFrame = gunCFrame
    task.wait(0.5)
    
    hrp.CFrame = originalPos
    
    return true
end

local function autoGrabGuns()
    if not Window:GetFlag("auto_grab") then return end
    
    local method = Window:GetFlag("grab_method") or "Teleport"
    local gunsToGrab = {}
    
    if Window:GetFlag("grab_remington") then table.insert(gunsToGrab, "Remington 870") end
    if Window:GetFlag("grab_m9") then table.insert(gunsToGrab, "M9") end
    if Window:GetFlag("grab_ak47") then table.insert(gunsToGrab, "AK-47") end
    
    for _, gunName in pairs(gunsToGrab) do
        local success = false
        
        if method == "Remote" then
            success = grabGunRemote(gunName)
        elseif method == "Teleport" then
            success = grabGunTeleport(gunName)
        end
        
        task.wait(0.5)
    end
    
    if deathPosition and Window:GetFlag("death_return") then
        local character = LocalPlayer.Character
        if character then
            local hrp = character:FindFirstChild("HumanoidRootPart")
            if hrp then
                hrp.CFrame = deathPosition
            end
        end
        deathPosition = nil
    end
end

local function switchTeam(teamName)
    local character = LocalPlayer.Character
    if not character then
        return
    end
    
    local hrp = character:FindFirstChild("HumanoidRootPart")
    if not hrp then
        return
    end
    
    local originalPos = hrp.CFrame
    
    if teamName == "Criminal" then
        hrp.CFrame = CFrame.new(criminalTeleportPos)
        task.wait(0.5)
        hrp.CFrame = originalPos
        
        task.wait(0.3)
        
        if LocalPlayer.Team and LocalPlayer.Team.Name == "Criminals" then
            feUI:Notify({
                Title = "Team Switched",
                Content = "Switched to Criminal",
                Type = "success"
            })
        else
            feUI:Notify({
                Title = "Team Switch Failed",
                Content = "Could not switch to Criminal",
                Type = "error"
            })
        end
    else
        local team = nil
        
        if teamName == "Neutral" then
            team = Teams:FindFirstChild("Neutral")
        elseif teamName == "Guard" then
            team = Teams:FindFirstChild("Guards")
        elseif teamName == "Inmate" then
            team = Teams:FindFirstChild("Inmates")
        end
        
        if team then
            TeamEvent:FireServer(team)
            
            task.wait(0.3)
            
            if LocalPlayer.Team == team then
                feUI:Notify({
                    Title = "Team Switched",
                    Content = "Switched to " .. teamName,
                    Type = "success"
                })
            else
                feUI:Notify({
                    Title = "Team Switch Failed",
                    Content = "Could not switch to " .. teamName,
                    Type = "error"
                })
            end
        end
    end
end

local function setupNoclip()
    if noclipEnabled then
        if noclipConnection then
            noclipConnection:Disconnect()
        end
        
        noclipConnection = RunService.Stepped:Connect(function()
            if not noclipEnabled then return end
            
            local character = LocalPlayer.Character
            if character then
                for _, part in pairs(character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        if noclipConnection then
            noclipConnection:Disconnect()
            noclipConnection = nil
        end
        
        local character = LocalPlayer.Character
        if character then
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end

local function setupWalkspeed()
    local character = LocalPlayer.Character
    if not character then return end
    
    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid then return end
    
    if walkspeedEnabled then
        local desiredSpeed = Window:GetFlag("walkspeed_value") or 16
        humanoid.WalkSpeed = desiredSpeed
        
        if walkspeedConnection then
            walkspeedConnection:Disconnect()
        end
        
        walkspeedConnection = humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
            if walkspeedEnabled then
                local currentDesired = Window:GetFlag("walkspeed_value") or 16
                if humanoid.WalkSpeed ~= currentDesired then
                    humanoid.WalkSpeed = currentDesired
                end
            end
        end)
    else
        if walkspeedConnection then
            walkspeedConnection:Disconnect()
            walkspeedConnection = nil
        end
        
        humanoid.WalkSpeed = 16
    end
end

local function clearESP()
    for _, obj in pairs(espObjects) do
        if obj then
            obj:Destroy()
        end
    end
    espObjects = {}
end

local function clearTracers()
    for _, obj in pairs(tracerObjects) do
        if obj then
            obj:Destroy()
        end
    end
    tracerObjects = {}
end

local function createESP(player)
    if not player.Character then return end
    
    local hrp = player.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    local existingESP = hrp:FindFirstChild("ESP_Highlight")
    if existingESP then
        existingESP:Destroy()
    end
    
    local highlight = Instance.new("Highlight")
    highlight.Name = "ESP_Highlight"
    highlight.Adornee = player.Character
    highlight.FillColor = Window:GetFlag("esp_color") or Color3.fromRGB(255, 100, 100)
    highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
    highlight.FillTransparency = 0.5
    highlight.OutlineTransparency = 0
    highlight.Parent = hrp
    
    table.insert(espObjects, highlight)
    
    if Window:GetFlag("esp_name") then
        local billboardGui = Instance.new("BillboardGui")
        billboardGui.Name = "ESP_Name"
        billboardGui.Adornee = hrp
        billboardGui.Size = UDim2.new(0, 200, 0, 50)
        billboardGui.StudsOffset = Vector3.new(0, 3, 0)
        billboardGui.AlwaysOnTop = true
        billboardGui.MaxDistance = 1000
        billboardGui.Parent = hrp
        
        local textLabel = Instance.new("TextLabel")
        textLabel.Size = UDim2.new(1, 0, 1, 0)
        textLabel.BackgroundTransparency = 1
        textLabel.Text = player.Name
        textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        textLabel.TextStrokeTransparency = 0
        textLabel.TextSize = 16
        textLabel.Font = Enum.Font.GothamBold
        textLabel.Parent = billboardGui
        
        table.insert(espObjects, billboardGui)
    end
end

local function createTracer(player)
    if not player.Character then return end
    
    local hrp = player.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    
    local camera = workspace.CurrentCamera
    local attachment0 = Instance.new("Attachment")
    attachment0.Parent = camera
    
    local attachment1 = Instance.new("Attachment")
    attachment1.Parent = hrp
    
    local beam = Instance.new("Beam")
    beam.Name = "ESP_Tracer"
    beam.Attachment0 = attachment0
    beam.Attachment1 = attachment1
    beam.Color = ColorSequence.new(Window:GetFlag("tracer_color") or Color3.fromRGB(100, 150, 255))
    beam.FaceCamera = true
    beam.Width0 = 0.1
    beam.Width1 = 0.1
    beam.Transparency = NumberSequence.new(0.5)
    beam.Parent = attachment0
    
    table.insert(tracerObjects, attachment0)
    table.insert(tracerObjects, attachment1)
    table.insert(tracerObjects, beam)
end

local function updateESP()
    if not espEnabled then
        clearESP()
        return
    end
    
    clearESP()
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if isPlayerIgnored(player) then
                continue
            end
            
            if Window:GetFlag("esp_team_check") and player.Team == LocalPlayer.Team then
                continue
            end
            
            if isPlayerAlive(player) then
                createESP(player)
            end
        end
    end
end

local function updateTracers()
    if not tracersEnabled then
        clearTracers()
        return
    end
    
    clearTracers()
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            if isPlayerIgnored(player) then
                continue
            end
            
            if Window:GetFlag("tracer_team_check") and player.Team == LocalPlayer.Team then
                continue
            end
            
            if isPlayerAlive(player) then
                createTracer(player)
            end
        end
    end
end

local function getPlayerList()
    local playerList = {}
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            table.insert(playerList, player.Name)
        end
    end
    table.sort(playerList)
    return playerList
end

local function getTeamList()
    local teamList = {}
    for _, team in pairs(Teams:GetTeams()) do
        table.insert(teamList, team.Name)
    end
    table.sort(teamList)
    return teamList
end

local targetPlayerDropdown
local currentTargetParagraph
local autoShootTargetDropdown

TargetingTab:AddSection({Name = "Auto-Shoot Targeting"})

currentTargetParagraph = TargetingTab:AddParagraph({
    Title = "Current Auto-Shoot Target",
    Content = "None - Will target closest player"
})

autoShootTargetDropdown = TargetingTab:AddDropdown({
    Name = "Lock Auto-Shoot Target",
    List = getPlayerList(),
    Default = ""
})

TargetingTab:AddButton({
    Name = "Set Auto-Shoot Target",
    Color = Color3.fromRGB(255, 200, 100),
    Callback = function()
        local selectedName = autoShootTargetDropdown.Value
        if selectedName and selectedName ~= "" then
            local player = Players:FindFirstChild(selectedName)
            if player then
                targetSpecificPlayer = player
                currentTargetParagraph:SetContent("Locked on: " .. selectedName)
                feUI:Notify({
                    Title = "Target Locked",
                    Content = "Auto-Shoot locked onto " .. selectedName,
                    Type = "info"
                })
            end
        end
    end
})

TargetingTab:AddButton({
    Name = "Clear Auto-Shoot Target",
    Callback = function()
        targetSpecificPlayer = nil
        currentTargetParagraph:SetContent("None - Will target closest player")
        feUI:Notify({
            Title = "Target Cleared",
            Content = "Auto-Shoot will target closest player",
            Type = "info"
        })
    end
})

TargetingTab:AddDivider()

TargetingTab:AddSection({Name = "Quick Player Actions"})

targetPlayerDropdown = TargetingTab:AddDropdown({
    Name = "Select Player",
    List = getPlayerList(),
    Default = "",
    Flag = "quick_target_player"
})

TargetingTab:AddButton({
    Name = "Kill Once",
    Description = "Teleport and kill the selected player once",
    Color = Color3.fromRGB(255, 100, 100),
    Callback = function()
        local selectedName = Window:GetFlag("quick_target_player")
        if selectedName and selectedName ~= "" then
            local player = Players:FindFirstChild(selectedName)
            if player then
                killPlayer(player)
            end
        end
    end
})

TargetingTab:AddToggle({
    Name = "Loop Kill",
    Description = "Continuously kill the selected player on respawn",
    Default = false,
    Flag = "quick_loop_kill",
    Callback = function(value)
        if value then
            local selectedName = Window:GetFlag("quick_target_player")
            if selectedName and selectedName ~= "" then
                local player = Players:FindFirstChild(selectedName)
                if player then
                    loopKillPlayer(player)
                end
            else
                Window:SetFlag("quick_loop_kill", false, true)
            end
        else
            loopKillActive = false
            loopKillTarget = nil
        end
    end
})

TargetingTab:AddButton({
    Name = "Teleport To",
    Description = "Teleport to the selected player",
    Color = Color3.fromRGB(100, 150, 255),
    Callback = function()
        local selectedName = Window:GetFlag("quick_target_player")
        if selectedName and selectedName ~= "" then
            local player = Players:FindFirstChild(selectedName)
            if player then
                teleportToPlayer(player)
                feUI:Notify({
                    Title = "Teleported",
                    Content = "Teleported to " .. selectedName,
                    Type = "info"
                })
            end
        end
    end
})

TargetingTab:AddButton({
    Name = "Refresh Player List",
    Callback = function()
        targetPlayerDropdown:Refresh(getPlayerList(), true)
        autoShootTargetDropdown:Refresh(getPlayerList(), true)
    end
})

TargetingTab:AddDivider()

TargetingTab:AddSection({Name = "Team Filtering"})

TargetingTab:AddDropdown({
    Name = "Target Specific Teams",
    List = {"Inmates", "Criminals", "Guards", "Neutral"},
    Default = {},
    Multi = true,
    Flag = "target_teams",
    Callback = function(value)
        targetSpecificTeams = value
    end
})

TargetingTab:AddButton({
    Name = "Clear Team Filter",
    Callback = function()
        targetSpecificTeams = {}
        Window:SetFlag("target_teams", {}, true)
    end
})

TargetingTab:AddDivider()

TargetingTab:AddSection({Name = "Ignore Lists"})

local ignorePlayerDropdown = TargetingTab:AddDropdown({
    Name = "Ignore Player",
    List = getPlayerList(),
    Default = "",
    Flag = "ignore_player_select"
})

TargetingTab:AddButton({
    Name = "Add to Ignore List",
    Color = Color3.fromRGB(255, 150, 50),
    Callback = function()
        local selectedName = Window:GetFlag("ignore_player_select")
        if selectedName and selectedName ~= "" then
            ignoredPlayers[selectedName] = true
            feUI:Notify({
                Title = "Player Ignored",
                Content = "Now ignoring " .. selectedName,
                Type = "info"
            })
        end
    end
})

TargetingTab:AddButton({
    Name = "Remove from Ignore List",
    Callback = function()
        local selectedName = Window:GetFlag("ignore_player_select")
        if selectedName and selectedName ~= "" then
            ignoredPlayers[selectedName] = nil
            feUI:Notify({
                Title = "Player Unignored",
                Content = "No longer ignoring " .. selectedName,
                Type = "info"
            })
        end
    end
})

TargetingTab:AddButton({
    Name = "Clear All Ignored Players",
    Callback = function()
        ignoredPlayers = {}
        feUI:Notify({
            Title = "Cleared",
            Content = "Cleared ignored players list",
            Type = "info"
        })
    end
})

local ignoreTeamDropdown = TargetingTab:AddDropdown({
    Name = "Ignore Team",
    List = getTeamList(),
    Default = "",
    Flag = "ignore_team_select"
})

TargetingTab:AddButton({
    Name = "Add Team to Ignore List",
    Color = Color3.fromRGB(255, 150, 50),
    Callback = function()
        local selectedTeam = Window:GetFlag("ignore_team_select")
        if selectedTeam and selectedTeam ~= "" then
            ignoredTeams[selectedTeam] = true
            feUI:Notify({
                Title = "Team Ignored",
                Content = "Now ignoring " .. selectedTeam,
                Type = "info"
            })
        end
    end
})

TargetingTab:AddButton({
    Name = "Remove Team from Ignore List",
    Callback = function()
        local selectedTeam = Window:GetFlag("ignore_team_select")
        if selectedTeam and selectedTeam ~= "" then
            ignoredTeams[selectedTeam] = nil
            feUI:Notify({
                Title = "Team Unignored",
                Content = "No longer ignoring " .. selectedTeam,
                Type = "info"
            })
        end
    end
})

TargetingTab:AddButton({
    Name = "Clear All Ignored Teams",
    Callback = function()
        ignoredTeams = {}
        feUI:Notify({
            Title = "Cleared",
            Content = "Cleared ignored teams list",
            Type = "info"
        })
    end
})

CombatTab:AddSection({Name = "Auto-Shoot"})

CombatTab:AddToggle({
    Name = "Enable Auto-Shoot",
    Default = false,
    Flag = "auto_shoot",
    Callback = function(value)
        autoShootEnabled = value
        
        if value then
            shotPlayers = {}
            
            if autoShootConnection then
                autoShootConnection:Disconnect()
            end
            
            autoShootConnection = RunService.Heartbeat:Connect(function()
                updateGunReference()
                
                if not currentGun then
                    if Window:GetFlag("auto_switch") then
                        autoSwitchGun()
                    end
                    return
                end
                
                if needsReload() then
                    if Window:GetFlag("auto_switch") then
                        if not autoSwitchGun() then
                            if Window:GetFlag("auto_reload") then
                                reloadGun()
                            end
                        end
                    else
                        if Window:GetFlag("auto_reload") then
                            reloadGun()
                        end
                    end
                    return
                end
                
                if not isGunReloading() and not isReloading then
                    autoShootLoop()
                    local fireRate = Window:GetFlag("shoot_speed") or 0.1
                    task.wait(fireRate)
                end
            end)
            
            feUI:Notify({
                Title = "Auto-Shoot",
                Content = "Auto-Shoot enabled",
                Type = "success"
            })
        else
            if autoShootConnection then
                autoShootConnection:Disconnect()
                autoShootConnection = nil
            end
            
            targetedPlayer = nil
            shotPlayers = {}
            
            feUI:Notify({
                Title = "Auto-Shoot",
                Content = "Auto-Shoot disabled",
                Type = "info"
            })
        end
    end
})

CombatTab:AddToggle({
    Name = "Auto Reload",
    Default = true,
    Flag = "auto_reload"
})

CombatTab:AddToggle({
    Name = "One Shot Mode",
    Description = "Only shoot each player once (Remington 870)",
    Default = true,
    Flag = "one_shot_mode"
})

CombatTab:AddSlider({
    Name = "Shoot Speed",
    Description = "Delay between shots",
    Min = 0.05,
    Max = 1,
    Default = 0.1,
    Decimals = 2,
    Suffix = "s",
    Flag = "shoot_speed"
})

CombatTab:AddButton({
    Name = "Reset Shot Tracking",
    Callback = function()
        shotPlayers = {}
    end
})

CombatTab:AddDivider()
CombatTab:AddSection({Name = "Targeting Settings"})

CombatTab:AddSlider({
    Name = "Range",
    Min = 10,
    Max = 500,
    Default = 100,
    Decimals = 0,
    Suffix = " studs",
    Flag = "shoot_range"
})

CombatTab:AddDropdown({
    Name = "Target Part",
    List = {"Head", "UpperTorso", "LowerTorso", "HumanoidRootPart"},
    Default = "Head",
    Flag = "target_part"
})

CombatTab:AddToggle({
    Name = "Team Check",
    Default = true,
    Flag = "team_check"
})

CombatTab:AddToggle({
    Name = "Visibility Check",
    Default = true,
    Flag = "visibility_check"
})

CombatTab:AddDivider()
CombatTab:AddSection({Name = "Gun Management"})

CombatTab:AddToggle({
    Name = "Auto Switch Guns",
    Description = "Switch to gun with ammo when empty",
    Default = true,
    Flag = "auto_switch"
})

CombatTab:AddButton({
    Name = "Manual Reload",
    Callback = function()
        if currentGun then
            reloadGun()
        end
    end
})

CombatTab:AddButton({
    Name = "Switch Gun",
    Callback = function()
        autoSwitchGun()
    end
})

MeleeTab:AddSection({Name = "Kill Aura"})

MeleeTab:AddToggle({
    Name = "Enable Kill Aura",
    Description = "Spam melee attack on nearby players",
    Default = false,
    Flag = "kill_aura",
    Callback = function(value)
        killAuraEnabled = value
        
        if value then
            if killAuraConnection then
                killAuraConnection:Disconnect()
            end
            
            killAuraConnection = RunService.Heartbeat:Connect(function()
                killAuraLoop()
                local meleeSpeed = Window:GetFlag("melee_speed") or 0.05
                task.wait(meleeSpeed)
            end)
            
            feUI:Notify({
                Title = "Kill Aura",
                Content = "Kill Aura enabled",
                Type = "success"
            })
        else
            if killAuraConnection then
                killAuraConnection:Disconnect()
                killAuraConnection = nil
            end
            
            feUI:Notify({
                Title = "Kill Aura",
                Content = "Kill Aura disabled",
                Type = "info"
            })
        end
    end
})

MeleeTab:AddSlider({
    Name = "Melee Range",
    Description = "Max distance for melee attacks",
    Min = 5,
    Max = 15,
    Default = 15,
    Decimals = 0,
    Suffix = " studs",
    Flag = "melee_range"
})

MeleeTab:AddSlider({
    Name = "Melee Speed",
    Description = "Delay between attacks",
    Min = 0.01,
    Max = 0.5,
    Default = 0.05,
    Decimals = 2,
    Suffix = "s",
    Flag = "melee_speed"
})

MeleeTab:AddToggle({
    Name = "Team Check",
    Description = "Don't attack teammates",
    Default = true,
    Flag = "melee_team_check"
})

MeleeTab:AddDivider()
MeleeTab:AddSection({Name = "Mass Elimination"})

MeleeTab:AddButton({
    Name = "Kill All",
    Description = "Teleport and kill all players once",
    Color = Color3.fromRGB(220, 80, 80),
    Callback = function()
        if killAllActive then
            return
        end
        
        feUI:CreateModal({
            Title = "Kill All Players?",
            Content = "This will teleport to and eliminate all players in the server.",
            ConfirmText = "Execute",
            CancelText = "Cancel",
            OnConfirm = function()
                killAll()
            end
        })
    end
})

MeleeTab:AddButton({
    Name = "Stop Kill All",
    Callback = function()
        if killAllActive then
            killAllActive = false
            if killAllRenderConnection then
                killAllRenderConnection:Disconnect()
                killAllRenderConnection = nil
            end
        end
    end
})

MeleeTab:AddToggle({
    Name = "Loop Kill All",
    Description = "Continuously kill all players",
    Default = false,
    Flag = "loop_kill_all",
    Callback = function(value)
        loopKillAllActive = value
        
        if value then
            loopKillAll()
        else
            if killAllRenderConnection then
                killAllRenderConnection:Disconnect()
                killAllRenderConnection = nil
            end
        end
    end
})

ArrestTab:AddSection({Name = "Quick Arrest"})

local arrestPlayerDropdown = ArrestTab:AddDropdown({
    Name = "Select Player",
    List = getPlayerList(),
    Default = "",
    Flag = "arrest_target_player"
})

ArrestTab:AddButton({
    Name = "Arrest Once",
    Description = "Teleport and arrest the selected player",
    Color = Color3.fromRGB(60, 120, 255),
    Callback = function()
        local selectedName = Window:GetFlag("arrest_target_player")
        if selectedName and selectedName ~= "" then
            local player = Players:FindFirstChild(selectedName)
            if player then
                arrestPlayerNow(player)
            end
        end
    end
})

ArrestTab:AddToggle({
    Name = "Loop Arrest",
    Description = "Continuously arrest the selected player",
    Default = false,
    Flag = "loop_arrest_player",
    Callback = function(value)
        if value then
            local selectedName = Window:GetFlag("arrest_target_player")
            if selectedName and selectedName ~= "" then
                local player = Players:FindFirstChild(selectedName)
                if player then
                    loopArrestPlayer(player)
                end
            else
                Window:SetFlag("loop_arrest_player", false, true)
            end
        else
            loopArrestActive = false
            loopArrestTarget = nil
        end
    end
})

ArrestTab:AddDivider()
ArrestTab:AddSection({Name = "Team Arrest"})

ArrestTab:AddDropdown({
    Name = "Select Team",
    List = {"Inmates", "Criminals", "Neutral"},
    Default = "Criminals",
    Flag = "arrest_team_select"
})

ArrestTab:AddButton({
    Name = "Arrest Team",
    Description = "Arrest all players on selected team",
    Color = Color3.fromRGB(100, 150, 255),
    Callback = function()
        local teamName = Window:GetFlag("arrest_team_select")
        if teamName then
            arrestTeam(teamName)
        end
    end
})

ArrestTab:AddDivider()
ArrestTab:AddSection({Name = "Mass Arrest"})

ArrestTab:AddButton({
    Name = "Arrest All",
    Description = "Arrest all arrestable players once",
    Color = Color3.fromRGB(60, 180, 255),
    Callback = function()
        if arrestAllActive then
            return
        end
        
        feUI:CreateModal({
            Title = "Arrest All Players?",
            Content = "This will attempt to arrest all players in the server.",
            ConfirmText = "Execute",
            CancelText = "Cancel",
            OnConfirm = function()
                arrestAll()
            end
        })
    end
})

ArrestTab:AddButton({
    Name = "Stop Arrest All",
    Callback = function()
        if arrestAllActive then
            arrestAllActive = false
            if arrestAllRenderConnection then
                arrestAllRenderConnection:Disconnect()
                arrestAllRenderConnection = nil
            end
        end
    end
})

ArrestTab:AddToggle({
    Name = "Loop Arrest All",
    Description = "Continuously arrest all players",
    Default = false,
    Flag = "loop_arrest_all",
    Callback = function(value)
        loopArrestAllActive = value
        
        if value then
            loopArrestAll()
        else
            if arrestAllRenderConnection then
                arrestAllRenderConnection:Disconnect()
                arrestAllRenderConnection = nil
            end
        end
    end
})

ArrestTab:AddButton({
    Name = "Refresh Player List",
    Callback = function()
        arrestPlayerDropdown:Refresh(getPlayerList(), true)
    end
})

CharacterTab:AddSection({Name = "Movement"})

CharacterTab:AddToggle({
    Name = "Noclip",
    Description = "Walk through walls",
    Default = false,
    Flag = "noclip",
    Callback = function(value)
        noclipEnabled = value
        setupNoclip()
    end
})

CharacterTab:AddToggle({
    Name = "Custom Walkspeed",
    Default = false,
    Flag = "walkspeed_enabled",
    Callback = function(value)
        walkspeedEnabled = value
        setupWalkspeed()
    end
})

CharacterTab:AddSlider({
    Name = "Walkspeed Value",
    Min = 16,
    Max = 200,
    Default = 50,
    Decimals = 0,
    Flag = "walkspeed_value",
    Callback = function(value)
        if walkspeedEnabled then
            setupWalkspeed()
        end
    end
})

CharacterTab:AddDivider()
CharacterTab:AddSection({Name = "Protection"})

CharacterTab:AddToggle({
    Name = "Godmode",
    Description = "Auto respawns you before you die",
    Default = false,
    Flag = "godmode",
    Callback = function(value)
        godmodeEnabled = value
        setupGodmode()
    end
})

VisualTab:AddSection({Name = "ESP"})

VisualTab:AddToggle({
    Name = "Enable ESP",
    Default = false,
    Flag = "esp_enabled",
    Callback = function(value)
        espEnabled = value
        
        if value then
            task.spawn(function()
                while espEnabled do
                    updateESP()
                    task.wait(1)
                end
            end)
        else
            clearESP()
        end
    end
})

VisualTab:AddToggle({
    Name = "Show Names",
    Default = true,
    Flag = "esp_name",
    Callback = function()
        if espEnabled then
            updateESP()
        end
    end
})

VisualTab:AddToggle({
    Name = "ESP Team Check",
    Default = true,
    Flag = "esp_team_check",
    Callback = function()
        if espEnabled then
            updateESP()
        end
    end
})

VisualTab:AddColorPicker({
    Name = "ESP Color",
    Default = Color3.fromRGB(255, 100, 100),
    Flag = "esp_color",
    Callback = function()
        if espEnabled then
            updateESP()
        end
    end
})

VisualTab:AddDivider()
VisualTab:AddSection({Name = "Tracers"})

VisualTab:AddToggle({
    Name = "Enable Tracers",
    Default = false,
    Flag = "tracers_enabled",
    Callback = function(value)
        tracersEnabled = value
        
        if value then
            task.spawn(function()
                while tracersEnabled do
                    updateTracers()
                    task.wait(0.5)
                end
            end)
        else
            clearTracers()
        end
    end
})

VisualTab:AddToggle({
    Name = "Tracer Team Check",
    Default = true,
    Flag = "tracer_team_check",
    Callback = function()
        if tracersEnabled then
            updateTracers()
        end
    end
})

VisualTab:AddColorPicker({
    Name = "Tracer Color",
    Default = Color3.fromRGB(100, 150, 255),
    Flag = "tracer_color",
    Callback = function()
        if tracersEnabled then
            updateTracers()
        end
    end
})

VisualTab:AddDivider()
VisualTab:AddSection({Name = "Theme"})

VisualTab:AddDropdown({
    Name = "Select Theme",
    List = {"dark", "blossom", "greenapple", "ocean", "sunset", "midnight"},
    Default = "midnight",
    Callback = function(value)
        Window:SetTheme(value)
    end
})

TeleportTab:AddSection({Name = "Quick Teleport"})

local teleportList = {}
for name, _ in pairs(quickTeleports) do
    table.insert(teleportList, name)
end
table.sort(teleportList)

TeleportTab:AddDropdown({
    Name = "Location",
    List = teleportList,
    Default = teleportList[1],
    Flag = "teleport_location"
})

TeleportTab:AddButton({
    Name = "Teleport",
    Color = Color3.fromRGB(100, 150, 255),
    Callback = function()
        local location = Window:GetFlag("teleport_location")
        local position = quickTeleports[location]
        
        if position then
            local character = LocalPlayer.Character
            if character then
                local hrp = character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    hrp.CFrame = CFrame.new(position)
                end
            end
        end
    end
})

TeamTab:AddSection({Name = "Team Switcher"})

local teamParagraph = TeamTab:AddParagraph({
    Title = "Current Team",
    Content = "Loading..."
})

task.spawn(function()
    while true do
        task.wait(1)
        
        if LocalPlayer.Team then
            teamParagraph:SetContent(LocalPlayer.Team.Name)
        else
            teamParagraph:SetContent("No Team")
        end
    end
end)

TeamTab:AddDivider()

TeamTab:AddButton({
    Name = "Switch to Neutral",
    Color = Color3.fromRGB(150, 150, 150),
    Callback = function()
        switchTeam("Neutral")
    end
})

TeamTab:AddButton({
    Name = "Switch to Guard",
    Color = Color3.fromRGB(60, 120, 255),
    Callback = function()
        switchTeam("Guard")
    end
})

TeamTab:AddButton({
    Name = "Switch to Inmate",
    Color = Color3.fromRGB(255, 140, 60),
    Callback = function()
        switchTeam("Inmate")
    end
})

TeamTab:AddButton({
    Name = "Switch to Criminal",
    Color = Color3.fromRGB(255, 60, 60),
    Callback = function()
        switchTeam("Criminal")
    end
})

GunTab:AddSection({Name = "Auto Grab"})

GunTab:AddToggle({
    Name = "Enable Auto Grab",
    Description = "Auto grab guns on respawn",
    Default = false,
    Flag = "auto_grab"
})

GunTab:AddToggle({
    Name = "Death Return",
    Description = "Return to death position after grabbing guns",
    Default = true,
    Flag = "death_return"
})

GunTab:AddDropdown({
    Name = "Grab Method",
    List = {"Remote", "Teleport"},
    Default = "Teleport",
    Flag = "grab_method"
})

GunTab:AddDivider()
GunTab:AddSection({Name = "Guns to Grab"})

GunTab:AddToggle({
    Name = "Remington 870",
    Default = true,
    Flag = "grab_remington"
})

GunTab:AddToggle({
    Name = "M9",
    Default = false,
    Flag = "grab_m9"
})

GunTab:AddToggle({
    Name = "AK-47",
    Default = false,
    Flag = "grab_ak47"
})

GunTab:AddDivider()

GunTab:AddButton({
    Name = "Grab Guns Now",
    Color = Color3.fromRGB(60, 180, 100),
    Callback = function()
        autoGrabGuns()
    end
})

LocalPlayer.CharacterAdded:Connect(function(character)
    shotPlayers = {}
    currentGun = nil
    gunStats = nil
    targetedPlayer = nil
    
    local humanoid = character:WaitForChild("Humanoid")
    
    humanoid.Died:Connect(function()
        local hrp = character:FindFirstChild("HumanoidRootPart")
        if hrp then
            deathPosition = hrp.CFrame
        end
    end)
    
    character:WaitForChild("HumanoidRootPart")
    
    if Window:GetFlag("auto_grab") then
        task.wait(1)
        autoGrabGuns()
    end
    
    if Window:GetFlag("noclip") then
        setupNoclip()
    end
    
    if Window:GetFlag("walkspeed_enabled") then
        task.wait(0.5)
        setupWalkspeed()
    end
    
    if Window:GetFlag("godmode") then
        task.wait(0.5)
        setupGodmode()
    end
    
    raycastParams.FilterDescendantsInstances = {character}
end)

if LocalPlayer.Character then
    local character = LocalPlayer.Character
    local humanoid = character:FindFirstChild("Humanoid")
    
    if humanoid then
        humanoid.Died:Connect(function()
            local hrp = character:FindFirstChild("HumanoidRootPart")
            if hrp then
                deathPosition = hrp.CFrame
            end
        end)
    end
    
    raycastParams.FilterDescendantsInstances = {character}
end

Players.PlayerRemoving:Connect(function(player)
    if player == LocalPlayer then
        clearESP()
        clearTracers()
    end
end)

Players.PlayerAdded:Connect(function()
    task.wait(1)
    if targetPlayerDropdown then
        targetPlayerDropdown:Refresh(getPlayerList(), true)
    end
    if autoShootTargetDropdown then
        autoShootTargetDropdown:Refresh(getPlayerList(), true)
    end
    if ignorePlayerDropdown then
        ignorePlayerDropdown:Refresh(getPlayerList(), true)
    end
    if arrestPlayerDropdown then
        arrestPlayerDropdown:Refresh(getPlayerList(), true)
    end
end)

Players.PlayerRemoving:Connect(function(player)
    ignoredPlayers[player.Name] = nil
    
    if targetSpecificPlayer == player then
        targetSpecificPlayer = nil
        currentTargetParagraph:SetContent("None - Will target closest player")
    end
    
    if loopKillTarget == player then
        loopKillActive = false
        loopKillTarget = nil
        Window:SetFlag("quick_loop_kill", false, true)
    end
    
    if loopArrestTarget == player then
        loopArrestActive = false
        loopArrestTarget = nil
        Window:SetFlag("loop_arrest_player", false, true)
    end
    
    task.wait(1)
    if targetPlayerDropdown then
        targetPlayerDropdown:Refresh(getPlayerList(), true)
    end
    if autoShootTargetDropdown then
        autoShootTargetDropdown:Refresh(getPlayerList(), true)
    end
    if ignorePlayerDropdown then
        ignorePlayerDropdown:Refresh(getPlayerList(), true)
    end
    if arrestPlayerDropdown then
        arrestPlayerDropdown:Refresh(getPlayerList(), true)
    end
end)
