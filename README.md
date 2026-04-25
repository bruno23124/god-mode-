--[[ 
    REACH DIRECIONAL V2 (15 STUDS)
    Focado apenas na frente para ser indetectável.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

if coreGui:FindFirstChild("ReachGUI") then coreGui:FindFirstChild("ReachGUI"):Destroy() end

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "ReachGUI"
screenGui.Parent = coreGui
screenGui.ResetOnSpawn = false 

local reachActive = false
local reachDistance = 15 -- Distância exata que você pediu

-- Interface
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 160, 0, 40)
mainFrame.Position = UDim2.new(1, -180, 1, -60)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.Parent = screenGui

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(1, -10, 1, -10)
btn.Position = UDim2.new(0, 5, 0, 5)
btn.Text = "Alcance: 15 (Frente)"
btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Parent = mainFrame

-- Função para checar se o inimigo está na frente (Cálculo de Dot Product)
local function isFacing(targetPos)
    local char = player.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then return false end
    
    local lookVec = char.HumanoidRootPart.CFrame.LookVector
    local targetVec = (targetPos - char.HumanoidRootPart.Position).Unit
    return lookVec:Dot(targetVec) > 0.5 -- 0.5 garante um ângulo frontal de 90°
end

-- Loop de execução
task.spawn(function()
    while true do
        task.wait(0.1)
        if reachActive then
            pcall(function()
                local char = player.Character
                local tool = char and char:FindFirstChildOfClass("Tool")
                
                if tool and tool:FindFirstChild("Handle") then
                    for _, target in pairs(game.Players:GetPlayers()) do
                        if target ~= player and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                            local hrp = target.Character.HumanoidRootPart
                            local dist = (char.HumanoidRootPart.Position - hrp.Position).Magnitude
                            
                            -- Verifica distância de 15 E se está na frente
                            if dist <= reachDistance and isFacing(hrp.Position) then
                                -- Teleporta o hit da ferramenta pro cara e volta (Invisível)
                                local originalCFrame = tool.Handle.CFrame
                                tool.Handle.CFrame = hrp.CFrame
                                tool:Activate()
                                task.wait()
                                tool.Handle.CFrame = originalCFrame
                            end
                        end
                    end
                end
            end)
        end
    end
end)

btn.MouseButton1Click:Connect(function()
    reachActive = not reachActive
    btn.Text = reachActive and "Reach: ATIVO ✅" or "Alcance: 15 (Frente)"
    btn.BackgroundColor3 = reachActive and Color3.fromRGB(0, 120, 0) or Color3.fromRGB(50, 50, 50)
end)
