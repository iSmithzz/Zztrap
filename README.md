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

-- Caixa de texto para mostrar chamadas
local logBox = Instance.new("TextBox", frame)
logBox.Size = UDim2.new(1, -10, 1, -45)
logBox.Position = UDim2.new(0, 5, 0, 40)
logBox.MultiLine = true
logBox.ClearTextOnFocus = false
logBox.TextWrapped = true
logBox.TextXAlignment = Enum.TextXAlignment.Left
logBox.TextYAlignment = Enum.TextYAlignment.Top
logBox.Font = Enum.Font.Code
logBox.TextSize = 12
logBox.BackgroundColor3 = Color3.fromRGB(10,10,10)
logBox.TextColor3 = Color3.new(1,1,1)
logBox.ReadOnly = true

local monitoring = false
local callCount = 0
local maxCalls = 500  -- limite para evitar travar

local function hook(event)
    if event == "call" then
        callCount = callCount + 1
        local info = debug.getinfo(2, "nSl")
        if info then
            local name = info.name or "<anon>"
            local src = info.short_src or "unknown"
            local line = info.currentline or 0
            local entry = string.format("%d: %s - %s:%d", callCount, name, src, line)
            -- Atualiza log (limitando tamanho)
            if #logBox.Text > 10000 then
                logBox.Text = logBox.Text:sub(#entry + 2)
            end
            logBox.Text = logBox.Text .. entry .. "\n"
        end
        if callCount >= maxCalls then
            debug.sethook()
            monitoring = false
            toggleBtn.Text = "Iniciar Monitor"
            logBox.Text = logBox.Text .. "\nLimite de chamadas atingido, monitoramento parado.\n"
        end
    end
end

local function startMonitoring()
    if monitoring then return end
    callCount = 0
    logBox.Text = ""
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
    logBox.Text = ""
    callCount = 0
end)

-- Monitorar ao clicar no botão para iniciar/parar
logBox.Text = "Clique 'Iniciar Monitor' para começar.\n"

-- Botão iniciar/parar monitoramento (clicar na área do botão)
toggleBtn.MouseButton1Click:Connect(function()
    if monitoring then
        stopMonitoring()
    else
        startMonitoring()
    end
end)
