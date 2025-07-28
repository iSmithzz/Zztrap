local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local gui = Instance.new("ScreenGui")
gui.Name = "FuncMonitorGui"
gui.Parent = player:WaitForChild("PlayerGui")

-- Botão para abrir/fechar janela
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 110, 0, 30)
toggleBtn.Position = UDim2.new(1, -120, 0, 10)
toggleBtn.AnchorPoint = Vector2.new(1,0)
toggleBtn.Text = "Abrir Monitor"
toggleBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleBtn.TextColor3 = Color3.new(1,1,1)
toggleBtn.Font = Enum.Font.SourceSansBold
toggleBtn.TextSize = 14
toggleBtn.Parent = gui

-- Janela principal
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 400, 0, 350)
frame.Position = UDim2.new(0.5, -200, 0.5, -175) -- centralizado na tela
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
frame.Visible = false
frame.Parent = gui

-- Botão fechar janela
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "X"
closeBtn.BackgroundColor3 = Color3.fromRGB(180,50,50)
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 18
closeBtn.Parent = frame

-- Botão limpar log
local clearBtn = Instance.new("TextButton")
clearBtn.Size = UDim2.new(0, 60, 0, 30)
clearBtn.Position = UDim2.new(0, 5, 0, 5)
clearBtn.Text = "Limpar"
clearBtn.BackgroundColor3 = Color3.fromRGB(50,100,50)
clearBtn.TextColor3 = Color3.new(1,1,1)
clearBtn.Font = Enum.Font.SourceSansBold
clearBtn.TextSize = 14
clearBtn.Parent = frame

-- Botão de teste para chamar as funções manualmente
local testBtn = Instance.new("TextButton")
testBtn.Size = UDim2.new(0, 80, 0, 30)
testBtn.Position = UDim2.new(0, 75, 0, 5)
testBtn.Text = "Testar"
testBtn.BackgroundColor3 = Color3.fromRGB(50,50,150)
testBtn.TextColor3 = Color3.new(1,1,1)
testBtn.Font = Enum.Font.SourceSansBold
testBtn.TextSize = 14
testBtn.Parent = frame

-- Container com barra de rolagem
local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, -10, 1, -45)
scroll.Position = UDim2.new(0, 5, 0, 40)
scroll.BackgroundColor3 = Color3.fromRGB(10,10,10)
scroll.BorderSizePixel = 0
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.ScrollBarThickness = 8
scroll.Parent = frame

local logLabel = Instance.new("TextLabel")
logLabel.Size = UDim2.new(1, -10, 0, 0)
logLabel.Position = UDim2.new(0, 5, 0, 0)
logLabel.BackgroundTransparency = 1
logLabel.TextColor3 = Color3.new(1,1,1)
logLabel.Font = Enum.Font.Code
logLabel.TextSize = 14
logLabel.TextXAlignment = Enum.TextXAlignment.Left
logLabel.TextYAlignment = Enum.TextYAlignment.Top
logLabel.TextWrapped = true
logLabel.Text = ""
logLabel.Parent = scroll

local logText = ""

local function updateLog(text)
    logLabel.Text = text
    local textSize = logLabel.TextBounds.Y
    logLabel.Size = UDim2.new(1, -10, 0, textSize)
    scroll.CanvasSize = UDim2.new(0, 0, 0, textSize)
end

local function addLog(line)
    logText = logText .. line .. "\n"
    updateLog(logText)
    print(line)
end

-- Monitorar mudança de Health (dano)
humanoid:GetPropertyChangedSignal("Health"):Connect(function()
    addLog("Health mudou para: " .. tostring(humanoid.Health))
end)

-- Sobrescreve MoveTo para monitorar chamadas
local originalMoveTo = humanoid.MoveTo
humanoid.MoveTo = function(self, position)
    addLog(string.format("MoveTo chamado para posição = %s", tostring(position)))
    return originalMoveTo(self, position)
end

-- Monitorar Jump via Changed
humanoid.Changed:Connect(function(prop)
    if prop == "Jump" and humanoid.Jump == true then
        addLog("Jump chamado")
    end
end)

toggleBtn.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
end)

closeBtn.MouseButton1Click:Connect(function()
    frame.Visible = false
end)

clearBtn.MouseButton1Click:Connect(function()
    logText = ""
    updateLog(logText)
end)

testBtn.MouseButton1Click:Connect(function()
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    humanoid.Health = math.max(humanoid.Health - 10, 0) -- causa dano
    humanoid:MoveTo(rootPart and rootPart.Position + Vector3.new(5,0,0) or Vector3.new(0,0,0))
    humanoid.Jump = true
end)

logText = "Monitorando funções: alteração de Health, MoveTo e Jump\nUse o botão 'Testar' para disparar chamadas.\nAbra a janela para ver os logs.\n"
updateLog(logText)
