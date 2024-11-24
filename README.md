local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/Marwanleprodu91670/muscle-legend-lite-hub-elerium-library-/refs/heads/main/library"))()
local Window = Library:AddWindow("Lite Hub Muscle Legend v8", { MinSize = Vector2.new(550, 500) })

-- Default values for sliders
local defaultWalkSpeed = 16
local defaultJumpPower = 50
local defaultHipHeight = 2

-- Main Tab
local MainTab = Window:AddTab("Main")
MainTab:AddSlider("WalkSpeed", function(Value)
    local player = game.Players.LocalPlayer
    if player and player.Character then
        player.Character.Humanoid.WalkSpeed = Value
    end
end, { Min = 16, Max = 2000, Default = defaultWalkSpeed })

MainTab:AddSlider("JumpPower", function(Value)
    local player = game.Players.LocalPlayer
    if player and player.Character then
        player.Character.Humanoid.JumpPower = Value
    end
end, { Min = 50, Max = 500, Default = defaultJumpPower })

MainTab:AddSlider("HipHeight", function(Value)
    local player = game.Players.LocalPlayer
    if player and player.Character then
        player.Character.Humanoid.HipHeight = Value
    end
end, { Min = 0, Max = 1000, Default = defaultHipHeight })

-- Add invisible baseplate
local function addInvisibleBaseplate()
    local baseplate = Instance.new("Part")
    baseplate.Size = Vector3.new(math.huge, 1, math.huge)
    baseplate.Anchored = true
    baseplate.CanCollide = true
    baseplate.Transparency = 1
    baseplate.Position = Vector3.new(0, -10, 0)
    baseplate.Parent = game.Workspace
end

addInvisibleBaseplate()

-- Kill Tab
local KillTab = Window:AddTab("Kill")

KillTab:AddButton("Speed Punch", function()
    local player = game.Players.LocalPlayer
    local punch = player.Backpack:FindFirstChild("Punch") or player.Character:FindFirstChild("Punch")
    if punch and punch:FindFirstChild("attackTime") then
        punch.attackTime.Value = 0
    end
end)

local AutoPunchToggle = false

KillTab:AddSwitch("Auto Punch", function(State)
    AutoPunchToggle = State
    if AutoPunchToggle then
        local function autoPunchLoop()
            while AutoPunchToggle do
                local player = game.Players.LocalPlayer
                local tool = player.Backpack:FindFirstChild("Punch") or player.Character:FindFirstChild("Punch")
                if tool and tool.Parent ~= player.Character then
                    tool.Parent = player.Character
                end
                if tool then
                    tool:Activate()
                end
                wait(0.01)
            end
        end
        coroutine.wrap(autoPunchLoop)()
    end
end)










local playerNames = {}

KillTab:AddTextBox("Spielernamen eingeben", function(text)
    if text ~= "" then
        table.insert(playerNames, text)
        print("Spieler hinzugefÃ¼gt: " .. text)
    end
end)

KillTab:AddButton("Clear", function()
    playerNames = {}
end)

local switch = KillTab:AddSwitch("Auto Kill Players", function(bool)
    if bool then
        while bool do
            wait(1)
            for _, playerName in ipairs(playerNames) do
                local player = game.Players:FindFirstChild(playerName)
                if player and player.Character and player.Character:FindFirstChild("Head") then
                    local headClone = player.Character.Head:Clone()
                    local leftHand = game.Players.LocalPlayer.Character:FindFirstChild("LeftHand")
                    if leftHand then
                        headClone.Parent = workspace
                        headClone.CFrame = leftHand.CFrame
                    end
                end
            end
        end
    end
end)


local whitelist = {}

KillTab:AddTextBox("Whitelist", function(text)
    if text ~= "" then
        table.insert(whitelist, text)
    end
end)

KillTab:AddButton("Clear", function()
    whitelist = {}
end)

local switch = KillTab:AddSwitch("Auto Kill", function(bool)
    if bool then
        while bool do
            wait(0.1)
            for _, player in ipairs(game.Players:GetPlayers()) do
                local isWhitelisted = false
                for _, whitelistedName in ipairs(whitelist) do
                    if player.Name == whitelistedName then
                        isWhitelisted = true
                        break
                    end
                end
                
                if not isWhitelisted and player.Character and player.Character:FindFirstChild("Head") then
                    local headClone = player.Character.Head:Clone()
                    local leftHand = game.Players.LocalPlayer.Character:FindFirstChild("LeftHand")
                    if leftHand then
                        headClone.Parent = workspace
                        headClone.CFrame = leftHand.CFrame
                    end
                end
            end
        end
    end
end)







-- Rebirth Tab
local rebirthAmount = 0 -- Store the desired rebirth amount
local currentRebirths = 0 -- Track the number of rebirths performed
local autoRebirthEnabled = false -- State of the Auto Rebirth toggle

-- Function to handle rebirthing
local function autoRebirth()
    spawn(function()
        while autoRebirthEnabled do
            -- Check if the rebirthRemote exists
            local rebirthevent = game.ReplicatedStorage.rEvents:FindFirstChild("rebirthRemote")
            if rebirthevent then
                -- Fire the rebirthRemote if the target hasn't been reached
                if rebirthAmount == 0 or currentRebirths < rebirthAmount then
                    rebirthevent:FireServer()
                    currentRebirths = currentRebirths + 1
                else
                    -- Stop rebirthing if the target is reached
                    autoRebirthEnabled = false
                    print("Reached the desired rebirth amount.")
                end
            end
            wait(0.1) -- Adjust delay as needed
        end
    end)
end

-- Add a Rebirth Tab
local RebirthTab = Window:AddTab("Rebirth")

-- Add the Auto Rebirth Toggle
RebirthTab:AddSwitch("Auto Rebirth", {
    Default = false,
    Callback = function(state)
        autoRebirthEnabled = state
        if state then
            autoRebirth() -- Start rebirthing when enabled
        end
    end
})

-- Add a Textbox for Rebirth Amount
RebirthTab:AddTextBox("Select Rebirth Amount", function(text)
    local inputNumber = tonumber(text) -- Convert input text to a number
    if inputNumber and inputNumber >= 0 then
        rebirthAmount = inputNumber -- Set the target rebirth amount
        currentRebirths = 0 -- Reset the counter
        print("Rebirth amount set to:", rebirthAmount)
    else
        print("Invalid input. Please enter a positive number.")
    end
end)




-- Misc Tab
local MiscTab = Window:AddTab("Misc")

-- AutoFarm Tab
local AutoFarmTab = Window:AddTab("Auto Farm")

local AutoWeightToggle = false
local AutoPushupsToggle = false
local SitupsToggle = false
local MuscleKingFarmToggle = false

-- Auto Punch
local function AutoPunch()
    spawn(function()
        while AutoPunchToggle do
            local player = game.Players.LocalPlayer
            local punchTool = player.Backpack:FindFirstChild("Punch") or player.Character:FindFirstChild("Punch")
            
            if punchTool then
                player.Character.Humanoid:EquipTool(punchTool)
                punchTool:Activate()
            end
            wait(0.1)
        end
    end)
end

-- Auto Weight
local function AutoWeight()
    spawn(function()
        while AutoWeightToggle do
            local player = game.Players.LocalPlayer
            local weightTool = player.Backpack:FindFirstChild("Weight") or player.Character:FindFirstChild("Weight")
            
            if weightTool then
                player.Character.Humanoid:EquipTool(weightTool)
                weightTool:Activate()
            end
            wait(0.1)
        end
    end)
end

-- Auto Pushups
local function AutoPushups()
    spawn(function()
        while AutoPushupsToggle do
            local player = game.Players.LocalPlayer
            local pushupsTool = player.Backpack:FindFirstChild("Pushups") or player.Character:FindFirstChild("Pushups")
            
            if pushupsTool then
                player.Character.Humanoid:EquipTool(pushupsTool)
                pushupsTool:Activate()
            end
            wait(0.1)
        end
    end)
end

-- Auto Situps
local function AutoSitups()
    spawn(function()
        while SitupsToggle do
            local player = game.Players.LocalPlayer
            local situpsTool = player.Backpack:FindFirstChild("Situps") or player.Character:FindFirstChild("Situps")
            
            if situpsTool then
                player.Character.Humanoid:EquipTool(situpsTool)
                situpsTool:Activate()
            end
            wait(0.1)
        end
    end)
end

-- Muscle King Farm
local function MuscleKingFarm()
    spawn(function()
        while MuscleKingFarmToggle do
            local player = game.Players.LocalPlayer
            player.Character.HumanoidRootPart.CFrame = CFrame.new(-8546.25879, 23.045435, -5636.78418)
            local pushupsTool = player.Backpack:FindFirstChild("Pushups") or player.Character:FindFirstChild("Pushups")
            if pushupsTool then
                player.Character.Humanoid:EquipTool(pushupsTool)
                pushupsTool:Activate()
            end
            wait(0.1)
        end
    end)
end

AutoFarmTab:AddSwitch("Auto Weight", function(value)
    AutoWeightToggle = value
    if AutoWeightToggle then
        AutoWeight()
    end
end)

AutoFarmTab:AddSwitch("Auto Pushups", function(value)
    AutoPushupsToggle = value
    if AutoPushupsToggle then
        AutoPushups()
    end
end)

AutoFarmTab:AddSwitch("Situps", function(value)
    SitupsToggle = value
    if SitupsToggle then
        AutoSitups()
    end
end)

AutoFarmTab:AddSwitch("Muscle King Farm", function(value)
    MuscleKingFarmToggle = value
    if MuscleKingFarmToggle then
        MuscleKingFarm()
    end
end)

-- Rock Tab
local RockTab = Window:AddTab("Rock")

-- Tiny Rock Teleport
RockTab:AddButton("Tiny Rock", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(17.6410236, -1.30998898, 2106.48926)
end)

-- Frozen Rock Teleport
RockTab:AddButton("Frozen Rock", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-2551.75854, -0.359962642, -243.308777)
end)

-- Mystic Rock Teleport
RockTab:AddButton("Mystic Rock", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(2186.14111, -0.359961987, 1250.59802)
end)

-- Inferno Rock Teleport
RockTab:AddButton("Inferno Rock", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-7262.18701, -0.359961987, -1259.24426)
end)

-- Legend Rock Teleport
RockTab:AddButton("Legend Rock", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(4140.41797, 987.453186, -4089.34937)
end)

-- MuscleKing Rock Teleport
RockTab:AddButton("Muscle King Rock", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-8971.56641, 27.4031715, -6061.27734)
end)

-- Teleport Tab
local TeleportTab = Window:AddTab("Teleport")

-- Beach Teleport
TeleportTab:AddButton("Beach", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-11, 5, -178)
end)

-- Legends Teleport
TeleportTab:AddButton("Legends", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(4603, 989, -3898)
end)

-- Muscle Teleport
TeleportTab:AddButton("Muscle", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-8626, 15, -5730)
end)

-- Tiny Teleport
TeleportTab:AddButton("Tiny", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-38, 5, 1884)
end)

-- Secret Teleport
TeleportTab:AddButton("Secret", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-2596, -1, 5738)
end)

-- Inferno Teleport
TeleportTab:AddButton("Inferno", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-6759, 5, -1285)
end)

-- Frost Teleport
TeleportTab:AddButton("Frost", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-2623, 5, -409)
end)

-- Mythical Teleport
TeleportTab:AddButton("Mythical", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(2251, 5, 1073)
end)

-- View Stats tab
local ViewStatsTab = Window:AddTab("ViewStats")

local playerData = {}
local currentSelectedPlayer = nil
local notFoundLabel = nil
local selectedPlayerName = nil

local function abbreviateNumber(value)
    if value >= 1e15 then
        return string.format("%.1fQa", value / 1e15)
    elseif value >= 1e12 then
        return string.format("%.1fT", value / 1e12)
    elseif value >= 1e9 then
        return string.format("%.1fB", value / 1e9)
    elseif value >= 1e6 then
        return string.format("%.1fM", value / 1e6)
    elseif value >= 1e3 then
        return string.format("%.1fK", value / 1e3)
    else
        return tostring(value)
    end
end

local function createPlayerLabels(player)
    local playerName = player.Name
    local leaderstats = player:FindFirstChild("leaderstats")
    local equippedPets = player:FindFirstChild("equippedPets")
    local ownedGamepasses = player:FindFirstChild("ownedGamepasses")

    local labels = {
        StrengthLabel = ViewStatsTab:AddLabel("Strength: " .. abbreviateNumber(leaderstats.Strength.Value or 0)),
        DurabilityLabel = ViewStatsTab:AddLabel("Durability: " .. abbreviateNumber(player.Durability.Value or 0)),
        KillsLabel = ViewStatsTab:AddLabel("Kills: " .. abbreviateNumber(leaderstats.Kills.Value or 0)),
        BrawlsLabel = ViewStatsTab:AddLabel("Brawls: " .. abbreviateNumber(leaderstats.Brawls.Value or 0)),
        AgilityLabel = ViewStatsTab:AddLabel("Agility: " .. abbreviateNumber(player.Agility.Value or 0)),
        EvilKarmaLabel = ViewStatsTab:AddLabel("evilKarma: " .. abbreviateNumber(player.evilKarma.Value or 0)),
        GoodKarmaLabel = ViewStatsTab:AddLabel("goodKarma: " .. abbreviateNumber(player.goodKarma.Value or 0)),
        MapLabel = ViewStatsTab:AddLabel("Map: " .. (player.currentMap.Value or "N/A")),
        KingTimeLabel = ViewStatsTab:AddLabel("KingTime: " .. abbreviateNumber(player.muscleKingTime.Value or 0)),
        PremiumLabel = ViewStatsTab:AddLabel("Premium: " .. (player.MembershipType == Enum.MembershipType.Premium and "true" or "false")),
    }

    for i = 1, 5 do
        local petValue = equippedPets:FindFirstChild("pet" .. i) and equippedPets["pet" .. i].Value or "N/A"
        labels["Pet" .. i .. "Label"] = ViewStatsTab:AddLabel("Pet" .. i .. ": " .. tostring(petValue))
    end

    local gamepassList = {}
    if ownedGamepasses then
        for _, gamepass in ipairs(ownedGamepasses:GetChildren()) do
            table.insert(gamepassList, gamepass.Name)
        end
    end

    local gamepassesText = #gamepassList > 0 and table.concat(gamepassList, ", ") or "N/A"
    labels.GamepassesLabel = ViewStatsTab:AddLabel("ownedGamepasses: " .. gamepassesText)

    playerData[playerName] = labels

    leaderstats.Kills.Changed:Connect(function()
        labels.KillsLabel.Text = "Kills: " .. abbreviateNumber(leaderstats.Kills.Value or 0)
    end)

    leaderstats.Strength.Changed:Connect(function()
        labels.StrengthLabel.Text = "Strength: " .. abbreviateNumber(leaderstats.Strength.Value or 0)
    end)

    leaderstats.Brawls.Changed:Connect(function()
        labels.BrawlsLabel.Text = "Brawls: " .. abbreviateNumber(leaderstats.Brawls.Value or 0)
    end)

    player.Durability.Changed:Connect(function()
        labels.DurabilityLabel.Text = "Durability: " .. abbreviateNumber(player.Durability.Value or 0)
    end)

    player.Agility.Changed:Connect(function()
        labels.AgilityLabel.Text = "Agility: " .. abbreviateNumber(player.Agility.Value or 0)
    end)

    player.evilKarma.Changed:Connect(function()
        labels.EvilKarmaLabel.Text = "evilKarma: " .. abbreviateNumber(player.evilKarma.Value or 0)
    end)

    player.goodKarma.Changed:Connect(function()
        labels.GoodKarmaLabel.Text = "goodKarma: " .. abbreviateNumber(player.goodKarma.Value or 0)
    end)

    player.currentMap.Changed:Connect(function()
        labels.MapLabel.Text = "Map: " .. (player.currentMap.Value or "N/A")
    end)

    player.muscleKingTime.Changed:Connect(function()
        labels.KingTimeLabel.Text = "KingTime: " .. abbreviateNumber(player.muscleKingTime.Value or 0)
    end)
end

local function removePlayerLabels(playerName)
    local labels = playerData[playerName]
    if labels then
        for _, label in pairs(labels) do
            label:Remove()
        end
        playerData[playerName] = nil
    end
end

local textbox = ViewStatsTab:AddTextBox("Player Name", function(playerName)
    selectedPlayerName = playerName
    if notFoundLabel then
        notFoundLabel:Remove()
        notFoundLabel = nil
    end

    local player = game.Players:FindFirstChild(playerName)
    if player then
        if currentSelectedPlayer then
            removePlayerLabels(currentSelectedPlayer)
        end
        createPlayerLabels(player)
        currentSelectedPlayer = playerName
    else
        notFoundLabel = ViewStatsTab:AddLabel("Player not found!")
    end
end)









-- Credits Tab
local CreditsTab = Window:AddTab("Credits")
CreditsTab:AddLabel("Script by Adopt, Cypher (LuckyIIIV) and ð“š_ð“¡")
CreditsTab:AddLabel("Discord: curve0610/kr.1220")
