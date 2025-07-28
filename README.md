getgenv().autoTrain = true
getgenv().autoPrestige = true
getgenv().autoFood = true

spawn(function()
    while getgenv().autoTrain do
        local success, err = pcall(function()
            local player = game.Players.LocalPlayer
            local data = require(workspace.Src.C).Gen(player)
            game.ReplicatedStorage.WorkoutHandler_TriggerWorkoutGain:FireServer(data)
        end)
        task.wait(0.01)
    end
end)

spawn(function()
    while getgenv().autoPrestige do
        pcall(function()
            game.ReplicatedStorage.WorkoutHandler_Prestige:FireServer()
        end)
        task.wait(3)
    end
end)

spawn(function()
    while getgenv().autoFood do
        for _,v in pairs(workspace:GetDescendants()) do
            if v:IsA("ProximityPrompt") and v.Name:lower():find("food") then
                pcall(function()
                    fireproximityprompt(v)
                end)
            end
        end
        task.wait(1)
    end
end)
