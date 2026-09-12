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

local function toggleFlight()
    flying = not flying
    if flying then
        humanoid.PlatformStand = true
        bodyVelocity.Parent = humanoidRootPart
        bodyGyro.Parent = humanoidRootPart
    else
        humanoid.PlatformStand = false
        bodyVelocity.Parent = nil
        bodyGyro.Parent = nil
    end
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.F then -- Nhấn F để bật/tắt chế độ bay
        toggleFlight()
    elseif input.KeyCode == Enum.KeyCode.W then inputMap.W = true
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
