
-- Load Orion UI Library
local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/OrionUI/Orion/master/Source.lua"))()

-- Create the main window
local Window = OrionLib:MakeWindow({
    Name = "Kill Aura Test",
    HidePremium = true,
    SaveConfig = true,
    ConfigFolder = "OrionKillAura"
})

-- Create the "Killing" Tab
local KillingTab = Window:MakeTab({
    Name = "Killing",
    Icon = "rbxassetid://6031071050",
    PremiumOnly = false
})

-- Create the "Kill Farming" Section
local KillFarmingSection = KillingTab:AddSection({
    Name = "Kill Farming"
})

-- Create the "Auto Kill" toggle
local AutoKillToggle
AutoKillToggle = KillFarmingSection:AddToggle({
    Name = "Auto Kill",
    Default = false,
    Callback = function(State)
        if State then
            for _, player in pairs(game.Players:GetPlayers()) do
                if player ~= game.Players.LocalPlayer then
                    createPlayerHitboxes(player.Character)
                end
            end
        end
    end
})

-- Create the "Target Player" Section
local TargetPlayerSection = KillingTab:AddSection({
    Name = "Target Player"
})

-- Create the dropdown for selecting a player
local PlayerDropdown
local selectedPlayer
PlayerDropdown = TargetPlayerSection:AddDropdown({
    Name = "Select Player",
    Options = {},
    Default = nil,
    Callback = function(SelectedPlayer)
        selectedPlayer = SelectedPlayer
    end
})

-- Add function to update the player list in the dropdown
local function updatePlayerList()
    local playerNames = {}
    for _, player in pairs(game.Players:GetPlayers()) do
        table.insert(playerNames, player.Name)
    end
    PlayerDropdown:UpdateOptions(playerNames)
end

-- Automatically update player list when players join or leave
game.Players.PlayerAdded:Connect(updatePlayerList)
game.Players.PlayerRemoving:Connect(updatePlayerList)

-- Create the "Kill Target" toggle
local KillTargetToggle
KillTargetToggle = TargetPlayerSection:AddToggle({
    Name = "Kill Target",
    Default = false,
    Callback = function(State)
        if State and selectedPlayer then
            local target = game.Players:FindFirstChild(selectedPlayer)
            if target and target.Character then
                createPlayerHitboxes(target.Character)
            end
        end
    end
})

-- Create the "Spying" Section
local SpyingSection = KillingTab:AddSection({
    Name = "Spying"
})

-- Create the "Spy" toggle
local SpyToggle
SpyToggle = SpyingSection:AddToggle({
    Name = "Spy",
    Default = false,
    Callback = function(State)
        if State and selectedPlayer then
            local targetPlayer = game.Players:FindFirstChild(selectedPlayer)
            if targetPlayer and targetPlayer.Character then
                local targetCamera = game.Workspace.CurrentCamera
                targetCamera.CameraSubject = targetPlayer.Character.Humanoid
                targetCamera.CameraType = Enum.CameraType.Attach
            end
        else
            local targetCamera = game.Workspace.CurrentCamera
            targetCamera.CameraType = Enum.CameraType.Custom
        end
    end
})

-- Function to create the hitboxes for all players
local function createPlayerHitboxes(character)
    if not character then return end

    local hitboxSize = Vector3.new(5, 5, 5)
    local rightHand = character:FindFirstChild("RightHand")
    local leftHand = character:FindFirstChild("LeftHand")

    if not rightHand or not leftHand then
        return
    end

    -- Iterate through all players and create hitboxes
    for _, player in pairs(game.Players:GetPlayers()) do
        if player == game.Players.LocalPlayer then continue end

        local otherCharacter = player.Character
        if not otherCharacter then continue end
        
        local humanoidRootPart = otherCharacter:FindFirstChild("HumanoidRootPart")
        if not humanoidRootPart then continue end

        local hitbox = Instance.new("Part")
        hitbox.Size = hitboxSize
        hitbox.Position = humanoidRootPart.Position
        hitbox.Anchored = false
        hitbox.CanCollide = false
        hitbox.Transparency = 1
        hitbox.Parent = character

        local handToUse = rightHand
        if math.random() > 0.5 then
            handToUse = leftHand
        end
        
        hitbox.CFrame = handToUse.CFrame

        hitbox.Touched:Connect(function(otherPart)
            local otherPlayer = game.Players:GetPlayerFromCharacter(otherPart.Parent)
            if otherPlayer and otherPlayer ~= game.Players.LocalPlayer then
                print(otherPlayer.Name .. "'s HumanoidRootPart was touched by the hitbox!")
            end
        end)

        -- Update the hitbox position on every frame
        game:GetService("RunService").Heartbeat:Connect(function()
            if handToUse and hitbox then
                hitbox.CFrame = handToUse.CFrame
            end
        end)
    end
end

-- Initial update of player list when the script runs
updatePlayerList()

OrionLib:Init()
