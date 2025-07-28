-- ✅ CONFIGURAÇÕES: Ative ou desative recursos
local autoTrain = true
local autoPrestige = true
local autoFood = true

-- ⏱️ Ajuste de velocidade (quanto menor, mais rápido — cuidado!)
local trainDelay = 0.01
local prestigeDelay = 3
local foodDelay = 1

-- 🧩 Referências básicas
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- 🔁 Função Auto Train
local function train()
    local success, result = pcall(function()
        local workoutModule = require(workspace.Src.C)
        local workoutData = workoutModule.Gen(LocalPlayer)
        ReplicatedStorage.WorkoutHandler_TriggerWorkoutGain:FireServer(workoutData)
    end)
    if not success then
