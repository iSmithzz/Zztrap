local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FuncMonitorGui"

-- Botão para abrir/fechar janela
local toggleBtn = Instance.new("TextButton", gui)
toggleBtn.Size = UDim2.new(0, 110, 0, 30)
toggleBtn.Position = UDim2.new(1, -120, 0, 10)
toggleBtn.Text = "Abrir Monitor"
toggleBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleBtn.TextColor3 = Color3.new(1,1,1)
toggleBtn.Font = Enum.Font.SourceSansBold
toggleBtn.TextSize = 14

-- Janela principal
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 400, 0, 300)
frame.Position = UDim2.new(1, -410, 0, 50)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
frame.Visible = false

-- Botão fechar janela
local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "X"
closeBtn.BackgroundColor3 = Color3.fromRGB(180,50,50)
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 18

-- Botão limpar log
local clearBtn = Instance.new("TextButton", frame)
clearBtn.Size = UDim2.new(0, 60, 0, 30)
clearBtn.Position = UDim2.new(0, 5, 0, 5)
clearBtn.Text = "Limpar"
clearBtn.BackgroundColor3 = Color3.fromRGB(50,100,50)
clearBtn.TextColor3 = Color3.new(1,1,1)
clearBtn.Font = Enum.Font.SourceSansBold
clearBtn.TextSize = 14

-- Container com barra de rolagem
local scroll = Instance.new("ScrollingFrame", frame)
scroll.Size = UDim2.new(1, -10, 1, -45)
scroll.Position = UDim2.new(0, 5, 0, 40)
scroll.BackgroundColor3 = Color3.fromRGB(10,10,10)
scroll.BorderSizePixel = 0
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.ScrollBarThickness = 8

local logLabel = Instance.new("TextLabel", scroll)
logLabel.Size = UDim2.new(1, -10, 0, 0) -- altura dinâmica
logLabel.Position = UDim2.new(0, 5, 0, 0)
logLabel.BackgroundTransparency = 1
logLabel.TextColor3 = Color3.new(1,1,1)
logLabel.Font = Enum.Font.Code
logLabel.TextSize = 12
logLabel.TextXAlignment = Enum.TextXAlignment.Left
logLabel.TextYAlignment = Enum.TextYAlignment.Top
logLabel.TextWrapped = true
logLabel.Text = ""

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
end

-- Sobrescreve e monitora as funções do humanoid

-- TakeDamage
local originalTakeDamage = humanoid.TakeDamage
humanoid.TakeDamage = function(self, amount)
    addLog(string.format("TakeDamage chamado com amount = %s", tostring(amount)))
    return originalTakeDamage(self, amount)
end

-- MoveTo
local originalMoveTo = humanoid.MoveTo
humanoid.MoveTo = function(self, position)
    addLog(string.format("MoveTo chamado para posição = %s", tostring(position)))
    return originalMoveTo(self, position)
end

-- Jump
local originalJump = humanoid.Jump
humanoid.Jump = false -- garante que seja bool inicialmente
local jumpMeta = getmetatable(humanoid)
local jumpIndex = jumpMeta.__index

-- Monitorar mudança de Jump (quando pular)
-- Como Jump é uma propriedade, vamos usar Changed
humanoid.Changed:Connect(function(prop)
    if prop == "Jump" and humanoid.Jump == true then
        addLog("Jump chamado")
    end
end)

-- Interface e botões

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

-- Mensagem inicial
logText = "Monitorando funções: TakeDamage, MoveTo e Jump\nAbra a janela para ver os logs.\n"
updateLog(logText)
