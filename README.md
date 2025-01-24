local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local isAimLockActive = false
local targetPlayer = nil

local screenGui = Instance.new("ScreenGui")
local aimLockButton = Instance.new("TextButton")

screenGui.Name = "AimLockGui"
screenGui.Parent = player:WaitForChild("PlayerGui")

aimLockButton.Name = "AimLockButton"
aimLockButton.Size = UDim2.new(0, 200, 0, 50)
aimLockButton.Position = UDim2.new(0.5, -100, 0, 50)
aimLockButton.Text = "Ativar Aim Lock"
aimLockButton.BackgroundColor3 = Color3.new(0.2, 0.6, 0.8)
aimLockButton.TextColor3 = Color3.new(1, 1, 1)
aimLockButton.Font = Enum.Font.SourceSansBold
aimLockButton.TextSize = 18
aimLockButton.Parent = screenGui

local function toggleAimLock()
    isAimLockActive = not isAimLockActive
    aimLockButton.Text = isAimLockActive and "Desativar Aim Lock" or "Ativar Aim Lock"
end

aimLockButton.MouseButton1Click:Connect(toggleAimLock)

local function findClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge

    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local playerPosition = player.Character.HumanoidRootPart.Position

        for _, otherPlayer in pairs(game.Players:GetPlayers()) do
            if otherPlayer ~= player and otherPlayer.Character then
                local humanoidRootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    local distance = (playerPosition - humanoidRootPart.Position).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestPlayer = otherPlayer
                    end
                end
            end
        end
    end

    return closestPlayer
end

RunService.Heartbeat:Connect(function()
    if isAimLockActive then
        targetPlayer = findClosestPlayer()

        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local humanoidRootPart = targetPlayer.Character.HumanoidRootPart
            camera.CFrame = CFrame.new(camera.CFrame.Position, humanoidRootPart.Position)
        end
    end
end)
