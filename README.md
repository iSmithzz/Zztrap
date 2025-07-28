local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FuncInspector"

-- Botão pequeno para abrir a janela
local openBtn = Instance.new("TextButton", gui)
openBtn.Size = UDim2.new(0, 100, 0, 30)
openBtn.Position = UDim2.new(1, -110, 0, 10)
openBtn.Text = "Inspecionar"
openBtn.BackgroundColor3 = Color3.fromRGB(50,50,50)
openBtn.TextColor3 = Color3.new(1,1,1)
openBtn.Font = Enum.Font.SourceSansBold
openBtn.TextSize = 14

-- Janela para mostrar dados
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 350, 0, 250)
frame.Position = UDim2.new(1, -360, 0, 50)
frame.BackgroundColor3 = Color3.fromRGB(20,20,20)
frame.Visible = false

-- Botão fechar
local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "X"
closeBtn.BackgroundColor3 = Color3.fromRGB(180,50,50)
closeBtn.TextColor3 = Color3.new(1,1,1)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 18

-- Caixa de texto para exibir infos
local infoBox = Instance.new("TextBox", frame)
infoBox.Size = UDim2.new(1, -10, 1, -40)
infoBox.Position = UDim2.new(0, 5, 0, 35)
infoBox.MultiLine = true
infoBox.ClearTextOnFocus = false
infoBox.TextWrapped = true
infoBox.TextXAlignment = Enum.TextXAlignment.Left
infoBox.TextYAlignment = Enum.TextYAlignment.Top
infoBox.Font = Enum.Font.Code
infoBox.TextSize = 12
infoBox.BackgroundColor3 = Color3.fromRGB(10,10,10)
infoBox.TextColor3 = Color3.new(1,1,1)
infoBox.ReadOnly = true

-- Função para inspecionar
local function inspecionarFunc(func)
    if typeof(func) ~= "function" then
        infoBox.Text = "Erro: O argumento não é uma função."
        return
    end

    local info = debug.getinfo(func)
    local dumpOk, dumpData = pcall(string.dump, func)

    local texto = {}
    table.insert(texto, ("Nome da função: %s"):format(info.name or "desconhecido"))
    table.insert(texto, ("Origem: %s"):format(info.source or "desconhecido"))
    table.insert(texto, ("Definida na linha: %d"):format(info.linedefined or 0))
    table.insert(texto, ("Última linha: %d"):format(info.lastlinedefined or 0))
    table.insert(texto, "")

    if dumpOk then
        local hexDump = dumpData:gsub('.', function(c)
            return string.format('%02X ', string.byte(c))
        end)
        table.insert(texto, "Bytecode (hex):")
        table.insert(texto, hexDump)
    else
        table.insert(texto, "Não foi possível obter o bytecode.")
    end

    infoBox.Text = table.concat(texto, "\n")
end

-- Exemplo para testar
local function exemplo()
    print("Função de exemplo")
end

openBtn.MouseButton1Click:Connect(function()
    frame.Visible = true
    inspecionarFunc(exemplo)
end)

closeBtn.MouseButton1Click:Connect(function()
    frame.Visible = false
end)
