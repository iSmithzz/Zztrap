local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Verifica se Models existe
local ModelsFolder = ReplicatedStorage:WaitForChild("Models", 5)
if not ModelsFolder then
    warn("Pasta Models não encontrada em ReplicatedStorage")
    return
end

local CharacterModels = ModelsFolder:WaitForChild("CharacterModels", 5)
if not CharacterModels then
    warn("Pasta CharacterModels não encontrada em Models")
    return
end

local screenGui = script.Parent
local templateLabel = screenGui:FindFirstChild("TemplateLabel")

if not templateLabel then
    warn("TemplateLabel não encontrado na GUI")
    return
end

templateLabel.Visible = false

-- Cria UIListLayout se não existir
local UIListLayout = screenGui:FindFirstChildOfClass("UIListLayout")
if not UIListLayout then
    UIListLayout = Instance.new("UIListLayout")
    UIListLayout.Parent = screenGui
    UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
    UIListLayout.Padding = UDim.new(0, 5)
end

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
