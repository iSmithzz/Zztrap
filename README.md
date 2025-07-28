-- Variáveis globais
getgenv().autoTrain = false
getgenv().autoPrestige = false
getgenv().autoFood = false
getgenv().trainDelay = 0.01

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "iSmithzTrain"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 230, 0, 250)
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

-- Criação de botões com status ON/OFF
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

-- Botões
createToggle(40, "Auto Train", "autoTrain")
createToggle(80, "Auto Prestige", "autoPrestige")
createToggle(120, "Auto Food", "autoFood")

-- Controle de velocidade
local speedLabel = Instance.new("TextLabel", frame)
speedLabel.Position = UDim2.new(0, 10, 0, 160)
speedLabel.Size = UDim2.new(1, -20, 0, 20)
speedLabel.Text = "Train Speed: " .. tostring(getgenv().trainDelay)
speedLabel.TextColor3 = Color3.new(1, 1, 1)
speedLabel.BackgroundTransparency = 1
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextSize = 14

local plus = Instance.new("TextButton", frame)
plus.Position = UDim2.new(0, 10, 0, 190)
plus.Size = UDim2.new(0, 40, 0, 25)
plus.Text = "+"
plus.Font = Enum.Font.Gotham
plus.TextSize = 18
plus.BackgroundColor3 = Color3.fromRGB(50, 120, 50)
plus.TextColor3 = Color3.new(1, 1, 1)

local minus = Instance.new("TextButton", frame)
minus.Position = UDim2.new(0, 60, 0, 190)
minus.Size = UDim2.new(0, 40, 0, 25)
minus.Text = "-"
minus.Font = Enum.Font.Gotham
minus.TextSize = 18
minus.BackgroundColor3 = Color3.fromRGB(120, 50, 50)
minus.TextColor3 = Color3.new(1, 1, 1)

plus.MouseButton1Click:Connect(function()
	getgenv().trainDelay = math.max(0.001, getgenv().trainDelay - 0.005)
	speedLabel.Text = "Train Speed: " .. string.format("%.3f", getgenv().trainDelay)
end)

minus.MouseButton1Click:Connect(function()
	getgenv().trainDelay = getgenv().trainDelay + 0.005
	speedLabel.Text = "Train Speed: " .. string.format("%.3f", getgenv().trainDelay)
end)

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
