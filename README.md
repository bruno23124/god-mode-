--[[ 
    SCRIPT ATUALIZADO: GOD MODE INDETECTÁVEL V3 (BETA)
    Aviso: Este método tenta burlar hitboxes, pode falhar se o jogo 
    tiver uma verificação de posição de servidor rigorosa.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("GodModeGUI") then coreGui:FindFirstChild("GodModeGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local godModeActive = false

-- Interface Limpa (Canto inferior direito)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 180, 0, 50)
mainFrame.Position = UDim2.new(1, -200, 1, -70)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.Parent = screenGui

local godModeButton = Instance.new("TextButton")
godModeButton.Size = UDim2.new(0, 160, 0, 30)
godModeButton.Position = UDim2.new(0.5, -80, 0.5, -15)
godModeButton.Text = "God Indetectável: OFF"
godModeButton.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
godModeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
godModeButton.Parent = mainFrame

-- FUNÇÃO DE IMORTALIDADE INDETECTÁVEL
local function applySilentGod()
    local char = player.Character
    if char then
        -- MÉTODO 1: ForceField Invisível (Tenta bloquear)
        local ff = Instance.new("ForceField")
        ff.Parent = char
        ff.Visible = false -- Fica invisível
        
        -- MÉTODO 2: Manipulação de Hitbox (Local)
        -- Tenta "descolar" a caixa de colisão do corpo visual
        pcall(function()
            if char:FindFirstChild("Torso") then
                char.Torso.CanCollide = false
                char.Torso.CanTouch = false
            end
            if char:FindFirstChild("Head") then
                char.Head.CanCollide = false
                char.Head.CanTouch = false
            end
        end)
        
        -- Loop de cura forçada (Só por segurança local)
        task.spawn(function()
            while godModeActive do
                if char:FindFirstChild("Humanoid") then
                    char.Humanoid.Health = char.Humanoid.MaxHealth
                end
                task.wait(0.2)
            end
        end)
        
        print("Silent God Mode Ativado.")
    end
end

local function disableSilentGod()
    local char = player.Character
    if char then
        if char:FindFirstChildOfClass("ForceField") then
            char:FindFirstChildOfClass("ForceField"):Destroy()
        end
        pcall(function()
            if char:FindFirstChild("Torso") then
                char.Torso.CanCollide = true
                char.Torso.CanTouch = true
            end
            if char:FindFirstChild("Head") then
                char.Head.CanCollide = true
                char.Head.CanTouch = true
            end
        end)
    end
end

godModeButton.MouseButton1Click:Connect(function()
    godModeActive = not godModeActive
    godModeButton.Text = godModeActive and "God Indetectável: ON" or "God Indetectável: OFF"
    godModeButton.BackgroundColor3 = godModeActive and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    
    if godModeActive then
        applySilentGod()
    else
        disableSilentGod()
    end
end)
