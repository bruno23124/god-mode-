local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("GodModeGUI") then coreGui:FindFirstChild("GodModeGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local godModeActive = false

-- Interface
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

-- FUNÇÃO DE IMORTALIDADE AVANÇADA
local function applyGod()
    local char = player.Character
    if char then
        -- Tenta o método do ForceField
        local ff = Instance.new("ForceField")
        ff.Parent = char
        ff.Visible = true
        
        -- Tenta deletar scripts de dano locais
        for _, v in pairs(char:GetDescendants()) do
            if v:IsA("Script") and (v.Name:lower():find("damage") or v.Name:lower():find("kill")) then
                v:Destroy()
            end
        end
        
        -- Loop de cura forçada (Brute Force)
        task.spawn(function()
            while godModeActive do
                if char:FindFirstChild("Humanoid") then
                    char.Humanoid.Health = char.Humanoid.MaxHealth
                end
                task.wait(0.1)
            end
        end)
    end
end

godModeButton.MouseButton1Click:Connect(function()
    godModeActive = not godModeActive
    godModeButton.Text = godModeActive and "God Mode: ON ✓" or "Ativar God Mode"
    godModeButton.BackgroundColor3 = godModeActive and Color3.fromRGB(0, 200, 0) or Color3.fromRGB(200, 0, 0)
    
    if godModeActive then
        applyGod()
    else
        -- Remove o ForceField ao desligar
        if player.Character and player.Character:FindFirstChildOfClass("ForceField") then
            player.Character:FindFirstChildOfClass("ForceField"):Destroy()
        end
    end
end)
