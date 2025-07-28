-- Variáveis globais
getgenv().autoTrain = false
getgenv().trainDelay = 0.01

-- Interface básica
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "iSmithzTrainCop"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 180, 0, 70)
frame.Position = UDim2.new(0, 100, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BorderSizePixel = 0

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
title.Text = "iSmithz Train Cop"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 16

local toggleButton = Instance.new("TextButton", frame)
toggleButton.Position = UDim2.new(0, 20, 0, 35)
toggleButton.Size = UDim2.new(0, 140, 0, 25)
toggleButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.Gotham
toggleButton.TextSize = 14
toggleButton.Text = "Start Auto Train"

toggleButton.MouseButton1Click:Connect(function()
	getgenv().autoTrain = not getgenv().autoTrain
	if getgenv().autoTrain then
		toggleButton.Text = "Stop Auto Train"
		toggleButton.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
	else
		toggleButton.Text = "Start Auto Train"
		toggleButton.BackgroundColor3 = Color3.fromRGB(150, 50, 50)
	end
end)

-- Loop infinito para o Auto Train com recriação dinâmica para evitar problemas
task.spawn(function()
	while true do
		if getgenv().autoTrain then
			local success, err = pcall(function()
				-- Recria referencias dentro do loop
				local ReplicatedStorage = game:GetService("ReplicatedStorage")
				local Player = game.Players.LocalPlayer
				local WorkoutHandler = ReplicatedStorage:WaitForChild("WorkoutHandler_TriggerWorkoutGain")
				local gen = require(workspace:WaitForChild("Src"):WaitForChild("C")).Gen

				WorkoutHandler:FireServer(gen(Player))
			end)
			if not success then
				warn("Erro no Auto Train:", err)
			else
				-- Para debug, remover depois se quiser
				-- print("Auto Train disparado")
			end
		end
		task.wait(getgenv().trainDelay)
	end
end)
