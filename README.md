local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("GodModeGUI") then coreGui:FindFirstChild("GodModeGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local godModeActive = false

-- Interface super discreta
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 150, 0, 40)
mainFrame.Position = UDim2.new(1, -170, 1, -60)
mainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
mainFrame.Parent = screenGui

local godModeButton = Instance.new("TextButton")
godModeButton.Size = UDim2.new(1, -10, 1, -10)
godModeButton.Position = UDim2.new(0, 5, 0, 5)
godModeButton.Text = "Modo: NORMAL"
godModeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
godModeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
godModeButton.Parent = mainFrame

-- FUNÇÃO PARA TENTAR IGNORAR TIROS
local function applyDodge()
    local char = player.Character
    if char then
        -- Desativa a detecção de toque em partes que costumam levar dano
        for _, part in pairs(char:GetChildren()) do
            if part:IsA("BasePart") then
                part.CanTouch = not godModeActive -- Se ativo, CanTouch = false
            end
        end
        
        -- Se o jogo for R15, tenta desativar nas partes internas
        if char:FindFirstChild("UpperTorso") then
            char.UpperTorso.CanTouch = not godModeActive
            char.LowerTorso.CanTouch = not godModeActive
            char.Head.CanTouch = not godModeActive
        end
    end
end

godModeButton.MouseButton1Click:Connect(function()
    godModeActive = not godModeActive
    godModeButton.Text = godModeActive and "Modo: EVASÃO" or "Modo: NORMAL"
    godModeButton.BackgroundColor3 = godModeActive and Color3.fromRGB(0, 120, 0) or Color3.fromRGB(50, 50, 50)
    
    applyDodge()
end)

-- Reaplicar ao renascer
player.CharacterAdded:Connect(function()
    task.wait(1)
    if godModeActive then applyDodge() end
end)
