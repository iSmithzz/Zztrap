local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "FuncAbsorverGui"

local btn = Instance.new("TextButton", gui)
btn.Size = UDim2.new(0, 80, 0, 30)
btn.Position = UDim2.new(1, -90, 0, 10)
btn.Text = "Mostrar Função"
btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
btn.TextColor3 = Color3.new(1, 1, 1)
btn.Font = Enum.Font.SourceSans
btn.TextSize = 14

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 200)
frame.Position = UDim2.new(1, -310, 0, 50)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.Visible = false

local closeBtn = Instance.new("TextButton", frame)
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "X"
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeBtn.TextColor3 = Color3.new(1, 1, 1)
closeBtn.Font = Enum.Font.SourceSansBold
closeBtn.TextSize = 18

local textBox = Instance.new("TextBox", frame)
textBox.Size = UDim2.new(1, -10, 1, -40)
textBox.Position = UDim2.new(0, 5, 0, 35)
textBox.Text = ""
textBox.ClearTextOnFocus = false
textBox.MultiLine = true
textBox.TextWrapped = true
textBox.TextXAlignment = Enum.TextXAlignment.Left
textBox.TextYAlignment = Enum.TextYAlignment.Top
textBox.Font = Enum.Font.Code
textBox.TextSize = 12
textBox.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
textBox.TextColor3 = Color3.new(1, 1, 1)
textBox.ReadOnly = true

-- Função para exibir o bytecode em hex
local function mostrarFunc(func)
    if typeof(func) ~= "function" then
        textBox.Text = "Erro: Não é uma função"
        return
    end
    local ok, dumped = pcall(string.dump, func)
    if not ok then
        textBox.Text = "Não foi possível obter o bytecode"
        return
    end
    local hex = dumped:gsub('.', function(c)
        return string.format('%02X ', string.byte(c))
    end)
    textBox.Text = hex
end

-- Função exemplo para testar
local function teste()
    print("Função teste")
end

btn.MouseButton1Click:Connect(function()
    frame.Visible = true
    mostrarFunc(teste)
end)

closeBtn.MouseButton1Click:Connect(function()
    frame.Visible = false
end)
