-- Create the ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SprintGui"
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Create the TextButton for mobile sprint toggle
local sprintButton = Instance.new("TextButton")
sprintButton.Size = UDim2.new(0, 100, 0, 50)
sprintButton.Position = UDim2.new(0, 10, 0, 10)
sprintButton.Text = "Sprint"
sprintButton.Parent = screenGui

-- Variables to track sprint state
local isSprinting = false
local walkSpeedNormal = 16
local walkSpeedSprint = 24

-- Function to toggle sprint
local function toggleSprint()
    local character = game.Players.LocalPlayer.Character
    if character and character:FindFirstChild("Humanoid") then
        local humanoid = character.Humanoid
        if isSprinting then
            humanoid.WalkSpeed = walkSpeedNormal
            sprintButton.Text = "Sprint"
        else
            humanoid.WalkSpeed = walkSpeedSprint
            sprintButton.Text = "Walk"
        end
        isSprinting = not isSprinting
    end
end

-- Connect the sprint button for mobile
sprintButton.MouseButton1Click:Connect(toggleSprint)

-- Function to handle shift key for PC sprint
local function onInputBegan(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.LeftShift then
        local character = game.Players.LocalPlayer.Character
        if character and character:FindFirstChild("Humanoid") then
            character.Humanoid.WalkSpeed = walkSpeedSprint
        end
    end
end

local function onInputEnded(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.LeftShift then
        local character = game.Players.LocalPlayer.Character
        if character and character:FindFirstChild("Humanoid") then
            character.Humanoid.WalkSpeed = walkSpeedNormal
        end
    end
end

-- Connect the input events for PC
game:GetService("UserInputService").InputBegan:Connect(onInputBegan)
game:GetService("UserInputService").InputEnded:Connect(onInputEnded)

-- Make the button draggable
local UserInputService = game:GetService("UserInputService")

local dragging
local dragInput
local dragStart
local startPos

local function update(input)
    local delta = input.Position - dragStart
    sprintButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

sprintButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = sprintButton.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

sprintButton.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input == dragInput then
        update(input)
    end
end)
