-- Referências
local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera
local userInputService = game:GetService("UserInputService")
local players = game.Players:GetPlayers()
local highlightService = game:GetService("Highlight")

-- Variável para armazenar highlights
local highlights = {}

-- Função para criar highlight em um jogador
local function createHighlight(targetPlayer)
    local character = targetPlayer.Character
    if character and not highlights[targetPlayer.UserId] then
        -- Cria um novo Highlight
        local highlight = Instance.new("Highlight")
        highlight.Parent = character
        highlight.Adornee = character
        highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Cor vermelha
        highlight.FillTransparency = 0.5 -- Meio transparente
        highlights[targetPlayer.UserId] = highlight
    end
end

-- Função para remover highlight de um jogador
local function removeHighlight(targetPlayer)
    local highlight = highlights[targetPlayer.UserId]
    if highlight then
        highlight:Destroy()
        highlights[targetPlayer.UserId] = nil
    end
end

-- Função para olhar para o jogador
local function lookAtPlayer(targetPlayer)
    local character = targetPlayer.Character
    if character then
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        if humanoidRootPart then
            camera.CFrame = CFrame.new(camera.CFrame.Position, humanoidRootPart.Position)
        end
    end
end

-- Função que lida com a entrada de teclado
local function onKeyPress(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.K then
        -- Ativa ou desativa o highlight em todos os jogadores
        for _, otherPlayer in ipairs(players) do
            if otherPlayer ~= player then
                if not highlights[otherPlayer.UserId] then
                    -- Cria highlight e olha para o jogador
                    createHighlight(otherPlayer)
                    lookAtPlayer(otherPlayer)
                else
                    -- Remove highlight
                    removeHighlight(otherPlayer)
                end
            end
        end
    end
end

-- Conectar o evento de pressionar tecla
userInputService.InputBegan:Connect(onKeyPress)

