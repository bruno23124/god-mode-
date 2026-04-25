--[[ 
    KILL AURA SILENCIOSA V1
    Instruções: Ative e chegue perto de alguém.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("GodModeGUI") then coreGui:FindFirstChild("GodModeGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local killAuraActive = false
local range = 15 -- Distância para o abate (Não coloque muito alto para não ser banido)

-- Interface
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 180, 0, 50)
mainFrame.Position = UDim2.new(1, -200, 1, -70)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.Parent = screenGui

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(1, -10, 1, -10)
btn.Position = UDim2.new(0, 5, 0, 5)
btn.Text = "Kill Aura: OFF"
btn.BackgroundColor3 = Color3.fromRGB(80, 0, 0)
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Parent = mainFrame

-- FUNÇÃO KILL AURA
task.spawn(function()
    while true do
        task.wait(0.5) -- Ataca a cada meio segundo para parecer "natural"
        if killAuraActive then
            pcall(function()
                for _, target in pairs(game.Players:GetPlayers()) do
                    if target ~= player and target.Character and target.Character:FindFirstChild("Humanoid") then
                        local distance = (player.Character.HumanoidRootPart.Position - target.Character.HumanoidRootPart.Position).Magnitude
                        
                        if distance <= range and target.Character.Humanoid.Health > 0 then
                            -- Tenta tirar vida (Método por dano direto local)
                            -- Nota: Se o jogo tiver proteção, precisaremos do Remote de soco/arma
                            target.Character.Humanoid.Health = 0
                            print("Alvo abatido pela Aura: "..target.Name)
                        end
                    end
                end
            end)
        end
    end
end)

btn.MouseButton1Click:Connect(function()
    killAuraActive = not killAuraActive
    btn.Text = killAuraActive and "Kill Aura: ATIVA" or "Kill Aura: OFF"
    btn.BackgroundColor3 = killAuraActive and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(80, 0, 0)
end)
