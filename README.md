-- Interface
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FuncCodeGui"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

local TextBox = Instance.new("TextBox")
TextBox.Size = UDim2.new(0.8, 0, 0.6, 0)
TextBox.Position = UDim2.new(0.1, 0, 0.2, 0)
TextBox.MultiLine = true
TextBox.ClearTextOnFocus = false
TextBox.Text = "Código da função vai aparecer aqui"
TextBox.Font = Enum.Font.Code
TextBox.TextSize = 14
TextBox.TextWrapped = true
TextBox.Parent = ScreenGui

-- Função para "absorver" outra função
local function absorverFunc(func)
    if typeof(func) ~= "function" then
        TextBox.Text = "O argumento passado não é uma função!"
        return
    end

    local status, dumped = pcall(string.dump, func)
    if status then
        -- dumped é o bytecode da função, vamos converter para string hexadecimal para visualização
        local hex = dumped:gsub('.', function(c)
            return string.format('%02X ', string.byte(c))
        end)
        TextBox.Text = "Bytecode da função (hexadecimal):\n\n" .. hex
    else
        TextBox.Text = "Não foi possível obter o bytecode da função."
    end
end

-- Exemplo de função para testar
local function exemplo()
    print("Esta é uma função de exemplo!")
end
