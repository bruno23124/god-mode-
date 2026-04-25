--[[ 
    GHOST MODE INDETECTÁVEL V4
    Este script tenta bugar o sistema de dano do servidor 
    sem usar efeitos visuais.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("GodModeGUI") then coreGui:FindFirstChild("GodModeGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local godModeActive = false

-- Interface Minimalista
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 180, 0, 50)
mainFrame.Position = UDim2.new(1, -200, 1, -70)
mainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
mainFrame.Parent = screenGui

local godModeButton = Instance.new("TextButton")
godModeButton.Size = UDim2.new(0, 160, 0, 30)
godModeButton.Position = UDim2.new(0.5, -80, 0.5, -15)
godModeButton.Text = "Ghost Mode: OFF"
godModeButton.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
godModeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
godModeButton.Parent = mainFrame

-- FUNÇÃO GHOST (Buga o Servidor)
local function applyGhost()
    local char = player.Character
    if char and char:FindFirstChild("Humanoid") then
        -- Método do Humanoid Fake
        local oldHum = char.Humanoid
        local newHum = oldHum:Clone()
        newHum.Parent = char
        newHum.Name = "Humanoid"
        
        oldHum:Destroy() -- Deleta o original que o servidor vigia
        
        -- Garante vida infinita no novo (Localmente)
        task.spawn(function()
            while godModeActive do
                newHum.Health = newHum.MaxHealth
                task.wait(0.1)
            end
        end)
    end
end

godModeButton.MouseButton1Click:Connect(function()
    godModeActive = not godModeActive
    godModeButton.Text = godModeActive and "Ghost Mode: ON" or "Ghost Mode: OFF"
    godModeButton.BackgroundColor3 = godModeActive and Color3.fromRGB(0, 100, 0) or Color3.fromRGB(100, 0, 0)
    
    if godModeActive then
        applyGhost()
    else
        -- Para voltar ao normal geralmente precisa dar Reset no boneco
        print("Para desativar o Ghost totalmente, resete o personagem.")
    end
end)
