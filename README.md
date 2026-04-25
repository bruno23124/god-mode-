-- CRIAR INTERFACE PRINCIPAL
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Variável de controle do God Mode
local godModeActive = false

-- Criar ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GodModeGUI"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Criar Frame principal (posicionado no canto inferior direito)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 200, 0, 60)  -- Frame pequeno e elegante
mainFrame.Position = UDim2.new(1, -220, 1, -80)  -- Canto inferior direito
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui

-- Adicionar sombra/estilo ao Frame
local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 8)
uiCorner.Parent = mainFrame

-- Criar Botão
local godModeButton = Instance.new("TextButton")
godModeButton.Name = "GodModeButton"
godModeButton.Size = UDim2.new(0, 180, 0, 40)
godModeButton.Position = UDim2.new(0.5, -90, 0.5, -20)
godModeButton.Text = "Ativar God Mode"
godModeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
godModeButton.TextSize = 14
godModeButton.Font = Enum.Font.GothamBold
godModeButton.BackgroundColor3 = Color3.fromRGB(220, 20, 60)  -- Vermelho (desativado)
godModeButton.BackgroundTransparency = 0.2
godModeButton.BorderSizePixel = 0
godModeButton.Parent = mainFrame

-- Estilizar botão
local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0, 6)
buttonCorner.Parent = godModeButton

-- Efeito hover no botão
godModeButton.MouseEnter:Connect(function()
    godModeButton.BackgroundTransparency = 0.05
end)

godModeButton.MouseLeave:Connect(function()
    godModeButton.BackgroundTransparency = 0.2
end)

-- FUNÇÃO PARA APLICAR GOD MODE
local function applyGodMode()
    -- MÉTODO 1: Aumentar MaxHealth para um valor extremamente alto (infinito prático)
    -- Isso impede que o jogador morra por dano acumulado
    humanoid.MaxHealth = math.huge  -- Valor infinito
    humanoid.Health = humanoid.MaxHealth  -- Cura completa
    
    -- MÉTODO 2: Conectar evento para impedir dano (camada extra de segurança)
    -- Esse método impede qualquer tentativa de reduzir a vida
    local connection
    connection = humanoid:GetPropertyChangedSignal("Health"):Connect(function()
        if godModeActive and humanoid.Health < humanoid.MaxHealth then
            -- Se a vida for reduzida mesmo com MaxHealth infinito, restaura imediatamente
            humanoid.Health = humanoid.MaxHealth
        end
    end)
    
    -- Armazenar a conexão para remover depois (se necessário)
    humanoid:SetAttribute("GodModeConnection", connection)
    
    -- MÉTODO 3: Desativar BreakJointsOnDeath (opcional, evita que o personagem se quebre)
    -- Isso impede que o personagem seja destruído mesmo se o dano passasse
    humanoid.BreakJointsOnDeath = false
    
    -- MÉTODO 4: Remover resistência a dano de queda (opcional - mantém o jogador vivo mesmo em quedas fatais)
    -- Isso pode ser feito conectando ao evento de dano
    local damageConnection
    damageConnection = humanoid:GetPropertyChangedSignal("Health"):Connect(function()
        if godModeActive and humanoid.Health <= 0 then
            -- Se a vida chegar a 0 ou menos, revive instantaneamente
            humanoid.Health = humanoid.MaxHealth
        end
    end)
    
    humanoid:SetAttribute("DamageConnection", damageConnection)
end

-- FUNÇÃO PARA DESATIVAR GOD MODE
local function disableGodMode()
    -- Restaurar valores normais
    humanoid.MaxHealth = 100  -- Valor padrão (ajuste conforme seu jogo)
    humanoid.Health = 100
    
    -- Restaurar BreakJointsOnDeath
    humanoid.BreakJointsOnDeath = true
    
    -- Desconectar eventos de proteção
    local connection1 = humanoid:GetAttribute("GodModeConnection")
    local connection2 = humanoid:GetAttribute("DamageConnection")
    
    if connection1 then
        connection1:Disconnect()
        humanoid:SetAttribute("GodModeConnection", nil)
    end
    
    if connection2 then
        connection2:Disconnect()
        humanoid:SetAttribute("DamageConnection", nil)
    end
end

-- FUNÇÃO PARA ATUALIZAR INTERFACE
local function updateButtonUI(isActive)
    if isActive then
        godModeButton.Text = "God Mode: ON ✓"
        godModeButton.BackgroundColor3 = Color3.fromRGB(34, 139, 34)  -- Verde (ativado)
        godModeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    else
        godModeButton.Text = "Ativar God Mode"
        godModeButton.BackgroundColor3 = Color3.fromRGB(220, 20, 60)  -- Vermelho (desativado)
        godModeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
end

-- FUNÇÃO PRINCIPAL DO BOTÃO (toggle)
local function toggleGodMode()
    godModeActive = not godModeActive
    
    if godModeActive then
        applyGodMode()
        updateButtonUI(true)
        
        -- Feedback adicional: efeito visual no personagem (opcional)
        print("[God Mode] Ativado - Personagem agora é imortal!")
        
        -- Mensagem no chat (opcional)
        game:GetService("StarterGui"):SetCore("ChatMakeSystemMessage", {
            Text = "⚡ God Mode ATIVADO! Você está imortal! ⚡",
            Color = Color3.fromRGB(0, 255, 0)
        })
    else
        disableGodMode()
        updateButtonUI(false)
        
        print("[God Mode] Desativado - Personagem voltou ao normal")
        
        game:GetService("StarterGui"):SetCore("ChatMakeSystemMessage", {
            Text = "🔴 God Mode DESATIVADO! Você não está mais imortal! 🔴",
            Color = Color3.fromRGB(255, 0, 0)
        })
    end
end

-- CONECTAR BOTÃO
godModeButton.MouseButton1Click:Connect(toggleGodMode)

-- EVENTO PARA QUANDO O PERSONAGEM MORRE/RENASCE
-- IMPORTANTE: Reconectar o God Mode se ativo após renascer
local function onCharacterAdded(newCharacter)
    character = newCharacter
    humanoid = character:WaitForChild("Humanoid")
    
    -- Se o God Mode estava ativo antes de morrer, reaplica ao novo personagem
    if godModeActive then
        -- Pequeno delay para garantir que o humanoid está pronto
        task.wait(0.1)
        applyGodMode()
        print("[God Mode] Reaplicado ao novo personagem")
    end
end

-- Conectar evento de troca de personagem
player.CharacterAdded:Connect(onCharacterAdded)

-- Verificar se o personagem já existe
if player.Character then
    onCharacterAdded(player.Character)
end

-- OPÇÃO EXTRA: Hotkey (tecla G) para ativar/desativar God Mode
local userInputService = game:GetService("UserInputService")

userInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.G then
        toggleGodMode()
    end
end)

print("✅ Interface God Mode carregada! Pressione G ou clique no botão para alternar.")
