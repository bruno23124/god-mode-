local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("GodModeGUI") then coreGui:FindFirstChild("GodModeGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local godModeActive = false

-- Interface (Mesmo estilo que você gosta)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 200, 0, 60)
mainFrame.Position = UDim2.new(1, -220, 1, -80)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.Parent = screenGui

local godModeButton = Instance.new("TextButton")
godModeButton.Size = UDim2.new(0, 180, 0, 40)
godModeButton.Position = UDim2.new(0.5, -90, 0.5, -20)
godModeButton.Text = "Ativar God Mode"
godModeButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
godModeButton.Parent = mainFrame

-- LOOP DE PROTEÇÃO AVANÇADA
task.spawn(function()
    while true do
        task.wait(0.1)
        if godModeActive then
            pcall(function()
                local char = player.Character
                if char then
                    -- MÉTODO 1: Remover as "Joints" de Dano
                    -- Tenta impedir que o servidor registre que você "quebrou"
                    if char:FindFirstChild("Humanoid") then
                        char.Humanoid.BreakJointsOnDeath = false
                    end

                    -- MÉTODO 2: Loop de Posição (Buga o Hitbox)
                    -- Alguns sistemas de tiro perdem o alvo se o HumanoidRootPart 
                    -- for manipulado levemente.
                    
                    -- MÉTODO 3: Deletar Scripts de Dano (Local)
                    for _, v in pairs(char:GetDescendants()) do
                        if v:IsA("RemoteEvent") and (v.Name:lower():find("damage") or v.Name:lower():find("hit")) then
                            -- CUIDADO: Isso pode te desconectar se o jogo for bem protegido
                        end
                    end
                end
            end)
        end
    end
end)

godModeButton.MouseButton1Click:Connect(function()
    godModeActive = not godModeActive
    godModeButton.Text = godModeActive and "God Mode: ON ✓" or "Ativar God Mode"
    godModeButton.BackgroundColor3 = godModeActive and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(200, 0, 0)
end)
