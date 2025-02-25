-- Crie uma tela principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PlayerESP"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Frame transparente com a opção "ON/OFF"
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.2, 0, 0.1, 0)
Frame.Position = UDim2.new(0.4, 0, 0.85, 0)
Frame.BackgroundTransparency = 0.5
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(1, 0, 1, 0)
ToggleButton.Position = UDim2.new(0, 0, 0, 0)
ToggleButton.BackgroundTransparency = 1
ToggleButton.TextScaled = true
ToggleButton.Text = "ON"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Parent = Frame

-- Lista expandida de cores para até 50 jogadores
local nameColors = {
    Color3.fromRGB(255, 0, 0), Color3.fromRGB(0, 255, 0), Color3.fromRGB(0, 0, 255),
    Color3.fromRGB(255, 255, 0), Color3.fromRGB(255, 165, 0), Color3.fromRGB(128, 0, 128),
    Color3.fromRGB(0, 255, 255), Color3.fromRGB(255, 192, 203), Color3.fromRGB(128, 128, 0),
    Color3.fromRGB(75, 0, 130), Color3.fromRGB(255, 105, 180), Color3.fromRGB(0, 128, 128),
    Color3.fromRGB(139, 69, 19), Color3.fromRGB(255, 140, 0), Color3.fromRGB(34, 139, 34),
    Color3.fromRGB(47, 79, 79), Color3.fromRGB(123, 104, 238), Color3.fromRGB(220, 20, 60),
    Color3.fromRGB(30, 144, 255), Color3.fromRGB(173, 216, 230), Color3.fromRGB(238, 130, 238),
    Color3.fromRGB(250, 128, 114), Color3.fromRGB(210, 105, 30), Color3.fromRGB(0, 0, 139),
    Color3.fromRGB(144, 238, 144),
}

-- Função para criar ESP (Extra Sensory Perception) para jogadores
local function createESP(player, color)
    if player.Character and player.Character:FindFirstChild("Head") then
        -- Criar um BillboardGui para o nome do jogador
        local billboardGui = Instance.new("BillboardGui")
        billboardGui.Name = "ESP"
        billboardGui.Adornee = player.Character:FindFirstChild("Head")
        billboardGui.Size = UDim2.new(2, 0, 1, 0)
        billboardGui.AlwaysOnTop = true

        -- Criar um rótulo de texto para o nome do jogador
        local nameLabel = Instance.new("TextLabel")
        nameLabel.Parent = billboardGui
        nameLabel.BackgroundTransparency = 1
        nameLabel.Size = UDim2.new(1, 0, 1, 0)
        nameLabel.Text = player.Name
        nameLabel.TextColor3 = color -- Cor do nome
        nameLabel.TextScaled = true
        nameLabel.Font = Enum.Font.SourceSansBold -- Texto grande e em negrito

        billboardGui.Parent = player.Character:FindFirstChild("Head")

        -- Criar um Highlight para contornar o personagem
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP_Highlight"
        highlight.Parent = player.Character
        highlight.FillTransparency = 1 -- Deixar o preenchimento invisível
        highlight.OutlineColor = color -- Cor da linha em volta do jogador
        highlight.OutlineTransparency = 0 -- Deixar a linha visível
    end
end

-- Função para adicionar ESP a todos os jogadores
local function addESPToPlayers()
    local assignedColors = {} -- Lista de cores já atribuídas
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and #assignedColors < #nameColors then
            local colorIndex
            repeat
                colorIndex = math.random(1, #nameColors) -- Escolher cor aleatória
            until not table.find(assignedColors, colorIndex) -- Garantir que não se repete
            table.insert(assignedColors, colorIndex) -- Registrar a cor usada
            createESP(player, nameColors[colorIndex]) -- Criar ESP com a cor escolhida
        end
    end
end

-- Conectar a função ao evento de jogadores adicionados
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        wait(1) -- Aguardar o personagem carregar completamente
        if #game.Players:GetPlayers() <= 50 then -- Agora suporta até 50 jogadores
            local colorIndex
            repeat
                colorIndex = math.random(1, #nameColors) -- Escolher cor aleatória
            until not table.find(assignedColors, colorIndex) -- Garantir que não se repete
            table.insert(assignedColors, colorIndex) -- Registrar a cor usada
            createESP(player, nameColors[colorIndex]) -- Criar ESP com a cor escolhida
        end
    end)
end)

-- Adicionar ESP aos jogadores já presentes
addESPToPlayers()

-- Variável para controlar a visualização dos jogadores através das paredes
local showESP = true

-- Função para alternar a visualização dos jogadores através das paredes
local function toggleESP()
    showESP = not showESP
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            local character = player.Character
            if character then
                local esp = character:FindFirstChild("Head"):FindFirstChild("ESP")
                if esp then
                    esp.Enabled = showESP
                end

                -- Ativar/desativar a linha em torno do personagem
                local highlight = character:FindFirstChild("ESP_Highlight")
                if highlight then
                    highlight.Enabled = showESP
                end
            end
        end
    end
    ToggleButton.Text = showESP and "ON" or "OFF"
end

-- Conectar a função ao evento de clique do botão
ToggleButton.MouseButton1Click:Connect(toggleESP)
