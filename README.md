local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FuncMonitorGui"

-- Botão para abrir/fechar janela
local toggleBtn = Instance.new("TextButton", gui)
toggleBtn.Size = UDim2.new(0, 110, 0, 30)
toggleBtn.Position = UDim2.new(1, -120, 0, 10)
toggleBtn.Text = "Iniciar Monitor"
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

local function updateLog(text)
    logLabel.Text = text
    local textSize = logLabel.TextBounds.Y
    logLabel.Size = UDim2.new(1, -10, 0, textSize)
    scroll.CanvasSize = UDim2.new(0, 0, 0, textSize)
end

local monitoring = false
local callCount = 0
local maxCalls = 500  -- limite para evitar travar
local logText = ""

local function hook(event)
    if event == "call" then
        callCount = callCount + 1
        local info = debug.getinfo(2, "nSl")
        if info then
            local name = info.name or "<anon>"
            local src = info.short_src or "unknown"
            local line = info.currentline or 0
            local entry = string.format("%d: %s - %s:%d", callCount, name, src, line)
            if #logText > 10000 then
                logText = logText:sub(#entry + 2)
            end
            logText = logText .. entry .. "\n"
            updateLog(logText)
        end
        if callCount >= maxCalls then
            debug.sethook()
            monitoring = false
            toggleBtn.Text = "Iniciar Monitor"
            logText = logText .. "\nLimite de chamadas atingido, monitoramento parado.\n"
            updateLog(logText)
        end
    end
end

local function startMonitoring()
    if monitoring then return end
    callCount = 0
    logText = ""
    updateLog(logText)
    debug.sethook(hook, "c")
    monitoring = true
    toggleBtn.Text = "Parar Monitor"
end

local function stopMonitoring()
    if not monitoring then return end
    debug.sethook()
    monitoring = false
    toggleBtn.Text = "Iniciar Monitor"
end

toggleBtn.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
end)

closeBtn.MouseButton1Click:Connect(function()
    frame.Visible = false
end)

clearBtn.MouseButton1Click:Connect(function()
    logText = ""
    callCount = 0
    updateLog(logText)
end)

toggleBtn.MouseButton1Click:Connect(function()
    if monitoring then
        stopMonitoring()
    else
        startMonitoring()
    end
end)

-- Mensagem inicial
logText = "Clique 'Iniciar Monitor' para começar.\n"
updateLog(logText)
