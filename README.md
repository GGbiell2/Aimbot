-- Configurações iniciais
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = game.Workspace.CurrentCamera
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Variáveis de equipe
local minhaEquipe = "Blue"
local equipeInimiga = "Red"

-- Função para identificar a equipe
local function isEnemy(player)
    return player.TeamColor == BrickColor.new(equipeInimiga)
end

-- Função para encontrar o jogador inimigo mais próximo
local function getClosestEnemy()
    local closestEnemy = nil
    local shortestDistance = math.huge

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and isEnemy(player) and player.Character and player.Character:FindFirstChild("Head") then
            local distance = (player.Character.Head.Position - LocalPlayer.Character.Head.Position).magnitude
            if distance < shortestDistance then
                closestEnemy = player
                shortestDistance = distance
            end
        end
    end

    return closestEnemy
end

-- Função para travar a câmera na cabeça do inimigo
local function lockCameraOnEnemy(enemy)
    if enemy and enemy.Character and enemy.Character:FindFirstChild("Head") then
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, enemy.Character.Head.Position)
    end
end

-- Função para adicionar FOV
local function addFOV()
    Camera.FieldOfView = 90 -- Ajuste o valor conforme necessário
end

-- Função para alternar o script
local scriptEnabled = true
local function toggleScript()
    scriptEnabled = not scriptEnabled
end

-- Botão para ativar/desativar o script
local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 100, 0, 50)
button.Position = UDim2.new(0, 10, 0, 10)
button.Text = "Toggle Script"
button.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui"):WaitForChild("ScreenGui")

button.MouseButton1Click:Connect(toggleScript)

-- Loop principal
RunService.RenderStepped:Connect(function()
    if scriptEnabled then
        local enemy = getClosestEnemy()
        if enemy then
            lockCameraOnEnemy(enemy)
        end
        addFOV()
    end
end)
