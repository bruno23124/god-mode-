--[[ 
    KILL AURA COM FERRAMENTA V2
    Como usar: Ative o botão e EQUIPE o soco ou arma branca na mão.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("GodModeGUI") then coreGui:FindFirstChild("GodModeGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local killAuraActive = false
local range = 18 -- Distância que ele pega os inimigos

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

-- FUNÇÃO QUE TENTA ATACAR USANDO O QUE ESTÁ NA MÃO
task.spawn(function()
    while true do
        task.wait(0.1) -- Velocidade do ataque
        if killAuraActive then
            local char = player.Character
            local tool = char and char:FindFirstChildOfClass("Tool") -- Vê o que você está segurando
            
            if tool then
                pcall(function()
                    for _, target in pairs(game.Players:GetPlayers()) do
                        if target ~= player and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                            local distance = (char.HumanoidRootPart.Position - target.Character.HumanoidRootPart.Position).Magnitude
                            
                            if distance <= range and target.Character.Humanoid.Health > 0 then
                                -- Faz a ferramenta "atacar" o alvo
                                tool:Activate() -- Simula o clique
                                
                                -- Tenta enviar o dano direto via Remote se o jogo for padrão
                                local remote = tool:FindFirstChildOfClass("RemoteEvent") or tool:FindFirstChildOfClass("BindableEvent")
                                if remote then
                                    remote:FireServer(target.Character.Humanoid)
                                end
                            end
                        end
                    end
                end)
            end
        end
    end
end)

btn.MouseButton1Click:Connect(function()
    killAuraActive = not killAuraActive
    btn.Text = killAuraActive and "Aura: EQUIPE ALGO" or "Kill Aura: OFF"
    btn.BackgroundColor3 = killAuraActive and Color3.fromRGB(255, 0, 0) or Color3.fromRGB(80, 0, 0)
end)
