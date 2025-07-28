getgenv().autoTrain = false
getgenv().autoPrestige = false
getgenv().autoFood = false
getgenv().trainDelay = 0.01

-- Interface
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "iSmithzTrain"

local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 220, 0, 250)
Frame.Position = UDim2.new(0, 100, 0, 100)
Frame.BackgroundColor3 = Color3.fromRGB(30,30,30)

local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "iSmithz Train"
Title.TextColor3 = Color3.new(1,1,1)
Title.BackgroundColor3 = Color3.fromRGB(40,40,40)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18

local yOffset = 40

local function createToggle(name, variable)
    local label = Instance.new("TextLabel", Frame)
    label.Size = UDim2.new(0, 100, 0, 30)
    label.Position = UDim2.new(0, 10, 0, yOffset)
    label.Text = name
    label.TextColor3 = Color3.new(1,1,1)
    label.BackgroundTransparency = 1
    label.Font = Enum.Font.Gotham
    label.TextSize = 14

    local button = Instance.new("TextButton", Frame)
    button.Size = UDim2.new(0, 80, 0, 30)
    button.Position = UDim2.new(0, 120, 0, yOffset)
    button.BackgroundColor3 = Color3.fromRGB(60,60,60)
    button.TextColor3 = Color3.new(1,1,1)
    button.Font = Enum.Font.Gotham
    button.TextSize = 14
    button.Text = "OFF"

    button.MouseButton1Click:Connect(function()
        getgenv()[variable] = not getgenv()[variable]
        button.Text = getgenv()[variable] and "ON" or "OFF"
    end)

    yOffset += 40
end

local function createSpeedButtons()
    local label = Instance.new("TextLabel", Frame)
    label.Size = UDim2.new(0, 200, 0, 20)
    label.Position = UDim2.new(0, 10, 0, yOffset)
    label.Text = "Train Speed: " .. tostring(getgenv().trainDelay)
    label.Name = "SpeedLabel"
    label.TextColor3 = Color3.new(1,1,1)
    label.BackgroundTransparency = 1
    label.Font = Enum.Font.Gotham
    label.TextSize = 14

    local plus = Instance.new("TextButton", Frame)
    plus.Size = UDim2.new(0, 40, 0, 25)
    plus.Position = UDim2.new(0, 10, 0, yOffset + 25)
    plus.Text = "+"
    plus.Font = Enum.Font.Gotham
    plus.TextSize = 18
    plus.BackgroundColor3 = Color3.fromRGB(50,120,50)
    plus.TextColor3 = Color3.new(1,1,1)

    local minus = Instance.new("TextButton", Frame)
    minus.Size = UDim2.new(0, 40, 0, 25)
    minus.Position = UDim2.new(0, 60, 0, yOffset + 25)
    minus.Text = "-"
    minus.Font = Enum.Font.Gotham
    minus.TextSize = 18
    minus.BackgroundColor3 = Color3.fromRGB(120,50,50)
    minus.TextColor3 = Color3.new(1,1,1)

    plus.MouseButton1Click:Connect(function()
        getgenv().trainDelay = math.max(0.001, getgenv().trainDelay - 0.005)
        label.Text = "Train Speed: " .. tostring(getgenv().trainDelay)
    end)

    minus.MouseButton1Click:Connect(function()
        getgenv().trainDelay = getgenv().trainDelay + 0.005
        label.Text = "Train Speed: " .. tostring(getgenv().trainDelay)
    end)

    yOffset += 60
end

-- Criar botões da interface
createToggle("Auto Train", "autoTrain")
createToggle("Auto Prestige", "autoPrestige")
createToggle("Auto Food", "autoFood")
createSpeedButtons()

-- Loops das funções
task.spawn(function()
    while true do
        if getgenv().autoTrain then
            pcall(function()
                local plr = game.Players.LocalPlayer
                local data = require(workspace.Src.C).Gen(plr)
                game.ReplicatedStorage.WorkoutHandler_TriggerWorkoutGain:FireServer(data)
            end)
        end
        task.wait(getgenv().trainDelay)
    end
end)

task.spawn(function()
    while true do
        if getgenv().autoPrestige then
            pcall(function()
                game.ReplicatedStorage.WorkoutHandler_Prestige:FireServer()
            end)
        end
        task.wait(3)
    end
end)

task.spawn(function()
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
