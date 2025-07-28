local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CharacterModels = ReplicatedStorage:WaitForChild("Models"):WaitForChild("CharacterModels")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

local screenGui = script.Parent
local templateLabel = screenGui:WaitForChild("TextLabel") -- modelo de linha
templateLabel.Visible = false

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = screenGui
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 5)

local function createCharacterLine(characterModel)
    local label = templateLabel:Clone()
    label.Visible = true
    label.Text = ""

    local humanoid = characterModel:FindFirstChild("Humanoid")
    if humanoid then
        local bodyWidth = humanoid:FindFirstChild("BodyWidthScale")
        local bodyHeight = humanoid:FindFirstChild("BodyHeightScale")
        local bodyDepth = humanoid:FindFirstChild("BodyDepthScale")
        
        label.Text = characterModel.Name .. "\n" ..
                     "Width: " .. (bodyWidth and string.format("%.2f", bodyWidth.Value) or "N/A") .. " | " ..
                     "Height: " .. (bodyHeight and string.format("%.2f", bodyHeight.Value) or "N/A") .. " | " ..
                     "Depth: " .. (bodyDepth and string.format("%.2f", bodyDepth.Value) or "N/A")
    else
        label.Text = characterModel.Name .. " (No Humanoid found)"
    end

    label.Parent = screenGui
end

local function refreshList()
    -- Remove linhas antigas
    for _, child in pairs(screenGui:GetChildren()) do
        if child:IsA("TextLabel") and child ~= templateLabel then
            child:Destroy()
        end
    end

    for _, model in pairs(CharacterModels:GetChildren()) do
        createCharacterLine(model)
    end
end

refreshList()
