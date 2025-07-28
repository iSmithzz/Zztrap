-- Configuração Global
getgenv().autoTrain = false
getgenv().autoPrestige = false
getgenv().autoFood = false

-- Interface Simples
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local Title = Instance.new("TextLabel")
local TrainButton = Instance.new("TextButton")
local PrestigeButton = Instance.new("TextButton")
local FoodButton = Instance.new("TextButton")

ScreenGui.Name = "iSmithzTrain"
ScreenGui.Parent = game.CoreGui

Frame.Size = UDim2.new(0, 200, 0, 180)
Frame.Position = UDim2.new(0, 100, 0, 100)
Frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Frame.Parent = ScreenGui

Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "iSmithz Train"
Title.TextColor3 = Color3.new(1,1,1)
Title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.Parent = Frame

local function createButton(text, y, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 40)
    btn.Position = UDim2.new(0, 10, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.Gotham
    btn.TextSize = 14
    btn.Text = text
    btn.Parent = Frame
    btn.MouseButton1Click:Connect(callback)
end

createButton("Auto Train", 40, function()
    getgenv().autoTrain = not getgenv().autoTrain
end)

createButton("Auto Prestige", 90, function()
    getgenv().autoPrestige = not getgenv().autoPrestige
end)

createButton("Auto Food", 140, function()
    getgenv().autoFood = not getgenv().autoFood
end)

-- Auto Train Loop
spawn(function()
    while true do
        if getgenv().autoTrain then
            pcall(function()
                local player = game.Players.LocalPlayer
                local data = require(workspace.Src.C).Gen(player)
                game.ReplicatedStorage.WorkoutHandler_TriggerWorkoutGain:FireServer(data)
            end)
        end
        task.wait(0.01)
    end
end)

-- Auto Prestige Loop
spawn(function()
    while true do
        if getgenv().autoPrestige then
            pcall(function()
                game.ReplicatedStorage.WorkoutHandler_Prestige:FireServer()
            end)
        end
        task.wait(3)
    end
end)

-- Auto Food Loop
spawn(function()
    while true do
        if getgenv().autoFood then
            for _,v in pairs(workspace:GetDescendants()) do
                if v:IsA("ProximityPrompt") and v.Name:lower():find("food") then
                    pcall(function()
                        fireproximityprompt(v)
                    end)
                end
            end
        end
        task.wait(1)
    end
end)
