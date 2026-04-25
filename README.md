--[[ 
    RIPA GOD MODE: FAST ATTACK + REACH
    Como usar: Equipe a Ripa e ative o botão.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("RipaGUI") then coreGui:FindFirstChild("RipaGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RipaGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local ripaMasterActive = false

-- Interface
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 180, 0, 50)
mainFrame.Position = UDim2.new(1, -200, 1, -70)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.Parent = screenGui

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(1, -10, 1, -10)
btn.Position = UDim2.new(0, 5, 0, 5)
btn.Text = "Ripa Master: OFF"
btn.BackgroundColor3 = Color3.fromRGB(100, 50, 0) -- Cor de madeira
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Parent = mainFrame

-- LOOP DE ATAQUE E ALCANCE
task.spawn(function()
    while true do
        task.wait(0.1)
        if ripaMasterActive then
            pcall(function()
                local char = player.Character
                local tool = char:FindFirstChild("Ripa") or player.Backpack:FindFirstChild("Ripa")
                
                if tool and char:FindFirstChild("HumanoidRootPart") then
                    -- Aumenta o alcance da Ripa (REACH)
                    if tool:FindFirstChild("Handle") then
                        tool.Handle.Size = Vector3.new(10, 10, 10) -- Aumenta a área de hit
                        tool.Handle.Transparency = 0.8 -- Deixa quase invisível para não suspeitarem
                        tool.Handle.CanCollide = false
                    end

                    -- Procura alvos próximos
                    for _, target in pairs(game.Players:GetPlayers()) do
                        if target ~= player and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                            local dist = (char.HumanoidRootPart.Position - target.Character.HumanoidRootPart.Position).Magnitude
                            
                            if dist <= 20 then -- Alcance de 20 studs
                                -- Ataca rápido (Fast Attack)
                                tool:Activate()
                                -- Se o jogo usar RemoteEvent na Ripa, o Velocity vai disparar:
                                local event = tool:FindFirstChildOfClass("RemoteEvent")
                                if event then
                                    event:FireServer(target.Character.Humanoid)
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
    ripaMasterActive = not ripaMasterActive
    btn.Text = ripaMasterActive and "RIPA ATIVA ✅" or "Ripa Master: OFF"
    btn.BackgroundColor3 = ripaMasterActive and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(100, 50, 0)
    
    -- Volta o tamanho normal se desligar
    if not ripaMasterActive then
        pcall(function()
            local tool = player.Character:FindFirstChild("Ripa") or player.Backpack:FindFirstChild("Ripa")
            if tool and tool:FindFirstChild("Handle") then
                tool.Handle.Size = Vector3.new(1, 5, 1) -- Tamanho original aproximado
                tool.Handle.Transparency = 0
            end
        end)
    end
end)
