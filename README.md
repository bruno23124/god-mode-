--[[ 
    RIPA GOD MODE V7 - ULTRA REACH + FAST ATTACK
    Indetectável e Focado em Dano Real.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("RipaMaster") then coreGui:FindFirstChild("RipaMaster"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RipaMaster"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local isRipaActive = false
local attackRange = 25 -- Distância que a ripa vai alcançar

-- GUI Discreta
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 150, 0, 40)
frame.Position = UDim2.new(1, -170, 1, -60)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.Parent = screenGui

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(1, -10, 1, -10)
btn.Position = UDim2.new(0, 5, 0, 5)
btn.Text = "Ripa: OFF"
btn.BackgroundColor3 = Color3.fromRGB(100, 40, 0)
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Parent = frame

-- LOGICA DE ATAQUE RAPIDO
task.spawn(function()
    while true do
        task.wait(0.05) -- Velocidade extrema
        if isRipaActive then
            pcall(function()
                local char = player.Character
                local ripa = char:FindFirstChild("Ripa") or player.Backpack:FindFirstChild("Ripa")
                
                if ripa then
                    for _, target in pairs(game.Players:GetPlayers()) do
                        if target ~= player and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                            local dist = (char.HumanoidRootPart.Position - target.Character.HumanoidRootPart.Position).Magnitude
                            
                            if dist <= attackRange and target.Character.Humanoid.Health > 0 then
                                -- Forçar o ataque da Ripa
                                ripa:Activate()
                                
                                -- Tenta disparar o evento de dano que o jogo usa
                                -- Isso simula que você acertou o cara perfeitamente
                                local remote = ripa:FindFirstChildOfClass("RemoteEvent")
                                if remote then
                                    remote:FireServer(target.Character.Humanoid)
                                end
                            end
                        end
                    end
                end
            end)
        end
    end
end)

btn.MouseButton1Click:Connect(function()
    isRipaActive = not isRipaActive
    btn.Text = isRipaActive and "RIPA: ATIVA ✅" or "Ripa: OFF"
    btn.BackgroundColor3 = isRipaActive and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(100, 40, 0)
end)
