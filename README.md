-- Variáveis globais
getgenv().autoTrain = false
getgenv().autoPrestige = false
getgenv().autoFood = false
getgenv().staminaLock1 = false
getgenv().staminaLock2 = false
getgenv().trainDelay = 0.01 -- delay fixo

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "iSmithzTrain"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 230, 0, 280)
frame.Position = UDim2.new(0, 100, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
title.Text = "iSmithz Train"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 18

-- Função para criar botões ON/OFF
local function createToggle(y, label, variableName)
	local text = Instance.new("TextLabel", frame)
	text.Position = UDim2.new(0, 10, 0, y)
	text.Size = UDim2.new(0, 100, 0, 30)
	text.BackgroundTransparency = 1
	text.Text = label
	text.TextColor3 = Color3.new(1, 1, 1)
	text.Font = Enum.Font.Gotham
	text.TextSize = 14

	local button = Instance.new("TextButton", frame)
	button.Position = UDim2.new(0, 120, 0, y)
	button.Size = UDim2.new(0, 90, 0, 30)
	button.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
	button.TextColor3 = Color3.new(1, 1, 1)
	button.Font = Enum.Font.Gotham
	button.TextSize = 14
	button.Text = "OFF"

	button.MouseButton1Click:Connect(function()
		getgenv()[variableName] = not getgenv()[variableName]
		button.Text = getgenv()[variableName] and "ON" or "OFF"
		button.BackgroundColor3 = getgenv()[variableName] and Color3.fromRGB(50, 150, 50) or Color3.fromRGB(150, 50, 50)
	end)
end

-- Criando botões
createToggle(40, "Auto Train", "autoTrain")
createToggle(80, "Auto Prestige", "autoPrestige")
createToggle(120, "Auto Food", "autoFood")
createToggle(160, "Stamina Lock 1", "staminaLock1")
createToggle(200, "Stamina Lock 2", "staminaLock2")

-- Auto Train com coroutine reiniciável usando fórmula do PrisonPump
local trainCoroutine
local function startAutoTrain()
	trainCoroutine = coroutine.create(function()
		while true do
			if getgenv().autoTrain then
				local success, result = pcall(function()
					local ReplicatedStorage = game:GetService("ReplicatedStorage")
					local WorkoutHandler = ReplicatedStorage:WaitForChild("WorkoutHandler_TriggerWorkoutGain")
					local player = game.Players.LocalPlayer
					local gen = require(workspace:WaitForChild("Src"):WaitForChild("C")).Gen
					WorkoutHandler:FireServer(gen(player))
				end)
				if not success then warn("[AutoTrain Error]:", result) end
			end
			task.wait(getgenv().trainDelay)
		end
	end)
	coroutine.resume(trainCoroutine)
end

-- Monitora toggle para reiniciar Auto Train coroutine
spawn(function()
	local lastState = getgenv().autoTrain
	while true do
		if getgenv().autoTrain ~= lastState then
			lastState = getgenv().autoTrain
			if lastState then
				startAutoTrain()
			end
		end
		task.wait(0.5)
	end
end)

-- Auto Prestige loop
task.spawn(function()
	while true do
		if getgenv().autoPrestige then
			pcall(function()
				game.ReplicatedStorage:WaitForChild("WorkoutHandler_Prestige"):FireServer()
			end)
		end
		task.wait(3)
	end
end)

-- Auto Food loop
task.spawn(function()
	while true do
		if getgenv().autoFood then
			for _, v in pairs(workspace:GetDescendants()) do
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

-- Stamina Lock 1: Setar atributo e valor Stamina no Humanoid
task.spawn(function()
	while true do
		if getgenv().staminaLock1 then
			local player = game.Players.LocalPlayer
			if player and player.Character then
				local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
				if humanoid then
					if humanoid:GetAttribute("Stamina") then
						humanoid:SetAttribute("Stamina", 100)
					end
					if humanoid:FindFirstChild("Stamina") and humanoid.Stamina:IsA("NumberValue") then
						humanoid.Stamina.Value = 100
					end
				end
			end
		end
		task.wait(0.1)
	end
end)

-- Stamina Lock 2: Setar stamina direto no personagem (exemplo genérico)
task.spawn(function()
	while true do
		if getgenv().staminaLock2 then
			local player = game.Players.LocalPlayer
			if player and player.Character then
				local stamina = player.Character:FindFirstChild("Stamina")
				if stamina and stamina:IsA("NumberValue") then
					stamina.Value = stamina.MaxValue or 100
				end
			end
		end
		task.wait(0.1)
	end
end)
