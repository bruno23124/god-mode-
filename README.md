-- CONFIGURAÇÃO DE PERSISTÊNCIA
local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

-- Verificar se já existe para não duplicar
if coreGui:FindFirstChild("GodModeGUI") then
    coreGui:FindFirstChild("GodModeGUI"):Destroy()
end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui -- Parent no CoreGui para não sumir
screenGui.ResetOnSpawn = false -- Não reseta ao morrer

-- Variáveis de controle
local godModeActive = false

-- FRAME PRINCIPAL
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 200, 0, 60)
mainFrame.Position = UDim2.new(1, -220, 1, -80)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 8)
uiCorner.Parent = mainFrame

-- BOTÃO
local godModeButton = Instance.new("TextButton")
godModeButton.Name = "GodModeButton"
godModeButton.Size = UDim2.new(0, 180, 0, 40)
godModeButton.Position = UDim2.new(0.5, -90, 0.5, -20)
godModeButton.Text = "Ativar God Mode"
godModeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
godModeButton.TextSize = 14
godModeButton.Font = Enum.Font.GothamBold
godModeButton.BackgroundColor3 = Color3.fromRGB(220, 20, 60)
godModeButton.Parent = mainFrame

local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0, 6)
buttonCorner.Parent = godModeButton

-- LOOP DE IMORTALIDADE (Roda em segundo plano)
task.spawn(function()
    while true do
        task.wait()
        if godModeActive then
            local char = player.Character
            if char and char:FindFirstChild("Humanoid") then
                -- Força a vida ao máximo continuamente
                char.Humanoid.MaxHealth = math.huge
                char.Humanoid.Health = math.huge
                
                -- Tenta remover scripts de dano comuns
                for _, v in pairs(char:GetChildren()) do
                    if v:IsA("Script") and (v.Name:lower():find("damage") or v.Name:lower():find("kill")) then
                        v:Destroy()
                    end
                end
            end
        end
    end
end)

-- FUNÇÃO TOGGLE
local function toggleGodMode()
    godModeActive = not godModeActive
    if godModeActive then
        godModeButton.Text = "God Mode: ON ✓"
        godModeButton.BackgroundColor3 = Color3.fromRGB(34, 139, 34)
    else
        godModeActive = false
        godModeButton.Text = "Ativar God Mode"
        godModeButton.BackgroundColor3 = Color3.fromRGB(220, 20, 60)
        if player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.MaxHealth = 100
            player.Character.Humanoid.Health = 100
        end
    end
end

godModeButton.MouseButton1Click:Connect(toggleGodMode)

-- Atalho Tecla G
game:GetService("UserInputService").InputBegan:Connect(function(input, gp)
    if not gp and input.KeyCode == Enum.KeyCode.G then
        toggleGodMode()
    end
end)

print("✅ God Mode Carregado no CoreGui!")
