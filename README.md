--[[ 
    REACH SCRIPT (Alcance Estendido)
    Aumenta a área de acerto da sua Ripa sem ninguém perceber.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("ReachGUI") then coreGui:FindFirstChild("ReachGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ReachGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local reachActive = false
local reachSize = Vector3.new(15, 15, 15) -- Tamanho do alcance (Pode aumentar se quiser)

-- Interface Discreta
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 160, 0, 40)
mainFrame.Position = UDim2.new(1, -180, 1, -60)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.Parent = screenGui

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(1, -10, 1, -10)
btn.Position = UDim2.new(0, 5, 0, 5)
btn.Text = "Alcance: OFF"
btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Parent = mainFrame

-- LOOP DO REACH
task.spawn(function()
    while true do
        task.wait(0.5)
        if reachActive then
            pcall(function()
                local char = player.Character
                if char then
                    -- Pega qualquer ferramenta (Ripa, Faca, Soco) que estiver na mão
                    local tool = char:FindFirstChildOfClass("Tool")
                    
                    if tool and tool:FindFirstChild("Handle") then
                        -- Aumenta o tamanho da área de acerto
                        tool.Handle.Size = reachSize
                        
                        -- Deixa invisível e tira a colisão com paredes para não bugar
                        tool.Handle.Transparency = 1 
                        tool.Handle.Massless = true
                        tool.Handle.CanCollide = false
                    end
                end
            end)
        end
    end
end)

btn.MouseButton1Click:Connect(function()
    reachActive = not reachActive
    btn.Text = reachActive and "Alcance: MAXIMO" or "Alcance: OFF"
    btn.BackgroundColor3 = reachActive and Color3.fromRGB(0, 120, 0) or Color3.fromRGB(50, 50, 50)
end)
