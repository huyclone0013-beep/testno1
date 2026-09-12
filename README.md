local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

local flying = false
local speed = 50
local inputMap = {W = false, S = false, A = false, D = false, Space = false, LeftShift = false}

local bodyVelocity = Instance.new("BodyVelocity")
bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
bodyVelocity.Velocity = Vector3.new(0, 0, 0)

local bodyGyro = Instance.new("BodyGyro")
bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
bodyGyro.P = 9000

-- Tạo giao diện UI cơ bản
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TestControlPanel"
screenGui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 200, 0, 150)
frame.Position = UDim2.new(0.1, 0, 0.1, 0)
frame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "Control Panel"
title.TextSize = 14
title.Parent = frame

local flyButton = Instance.new("TextButton")
flyButton.Size = UDim2.new(0.9, 0, 0, 35)
flyButton.Position = UDim2.new(0.05, 0, 0.25, 0)
flyButton.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
flyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
flyButton.Text = "Fly: OFF"
flyButton.TextSize = 14
flyButton.Parent = frame

local speedBox = Instance.new("TextBox")
speedBox.Size = UDim2.new(0.9, 0, 0, 35)
speedBox.Position = UDim2.new(0.05, 0, 0.6, 0)
speedBox.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
speedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
speedBox.Text = tostring(speed)
speedBox.PlaceholderText = "Nhập tốc độ"
speedBox.TextSize = 14
speedBox.Parent = frame

local function toggleFlight()
    flying = not flying
    if flying then
        humanoid.PlatformStand = true
        bodyVelocity.Parent = humanoidRootPart
        bodyGyro.Parent = humanoidRootPart
        flyButton.Text = "Fly: ON"
        flyButton.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
    else
        humanoid.PlatformStand = false
        bodyVelocity.Parent = nil
        bodyGyro.Parent = nil
        flyButton.Text = "Fly: OFF"
        flyButton.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    end
end

flyButton.MouseButton1Click:Connect(toggleFlight)

speedBox.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local newSpeed = tonumber(speedBox.Text)
        if newSpeed then
            speed = newSpeed
        else
            speedBox.Text = tostring(speed)
        end
    end
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.W then inputMap.W = true
    elseif input.KeyCode == Enum.KeyCode.S then inputMap.S = true
    elseif input.KeyCode == Enum.KeyCode.A then inputMap.A = true
    elseif input.KeyCode == Enum.KeyCode.D then inputMap.D = true
    elseif input.KeyCode == Enum.KeyCode.Space then inputMap.Space = true
    elseif input.KeyCode == Enum.KeyCode.LeftShift then inputMap.LeftShift = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.W then inputMap.W = false
    elseif input.KeyCode == Enum.KeyCode.S then inputMap.S = false
    elseif input.KeyCode == Enum.KeyCode.A then inputMap.A = false
    elseif input.KeyCode == Enum.KeyCode.D then inputMap.D = false
    elseif input.KeyCode == Enum.KeyCode.Space then inputMap.Space = false
    elseif input.KeyCode == Enum.KeyCode.LeftShift then inputMap.LeftShift = false
    end
end)

RunService.RenderStepped:Connect(function()
    if not flying then return end
    local camera = workspace.CurrentCamera
    local moveDirection = Vector3.new(0, 0, 0)
    
    if inputMap.W then moveDirection = moveDirection + camera.CFrame.LookVector end
    if inputMap.S then moveDirection = moveDirection - camera.CFrame.LookVector end
    if inputMap.A then moveDirection = moveDirection - camera.CFrame.RightVector end
    if inputMap.D then moveDirection = moveDirection + camera.CFrame.RightVector end
    if inputMap.Space then moveDirection = moveDirection + Vector3.new(0, 1, 0) end
    if inputMap.LeftShift then moveDirection = moveDirection - Vector3.new(0, 1, 0) end
    
    bodyVelocity.Velocity = moveDirection * speed
    bodyGyro.CFrame = camera.CFrame
end)
