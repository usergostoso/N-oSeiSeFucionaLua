local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = game.Workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer
local AimbotEnabled = false
local EspEnabled = true -- ESP ativado por padrão

-- Criar a interface (ScreenGui)
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer:FindFirstChildOfClass("PlayerGui")

-- Função para criar botões estilizados
local function CreateButton(text, position)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 200, 0, 50)
    button.Position = position
    button.Text = text
    button.Parent = screenGui
    button.BackgroundColor3 = Color3.fromRGB(0, 0, 255) -- Azul interno
    button.TextColor3 = Color3.fromRGB(255, 255, 255) -- Texto branco
    button.BorderSizePixel = 3 -- Tamanho da borda
    button.BorderColor3 = Color3.fromRGB(0, 0, 0) -- Borda preta
    
    -- Adicionando UICorner para arredondar os botões
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10) -- Define o arredondamento
    corner.Parent = button

    return button
end

-- Criar botões
local aimbotButton = CreateButton("Ativar Aimbot", UDim2.new(0.5, -100, 0.8, 0))
local espButton = CreateButton("Desativar ESP", UDim2.new(0.5, -100, 0.9, 0))

-- Função para ativar/desativar Aimbot
aimbotButton.MouseButton1Click:Connect(function()
    AimbotEnabled = not AimbotEnabled
    aimbotButton.Text = AimbotEnabled and "Desativar Aimbot" or "Ativar Aimbot"
end)

-- Função para ativar/desativar ESP
espButton.MouseButton1Click:Connect(function()
    EspEnabled = not EspEnabled
    espButton.Text = EspEnabled and "Desativar ESP" or "Ativar ESP"
end)

-- Função para encontrar o inimigo mais próximo (Aimbot)
local function GetClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local position, onScreen = Camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            local distance = (Vector2.new(position.X, position.Y) - UserInputService:GetMouseLocation()).magnitude
            
            if onScreen and distance < shortestDistance then
                shortestDistance = distance
                closestPlayer = player
            end
        end
    end
    
    return closestPlayer
end

-- Loop do Aimbot
RunService.RenderStepped:Connect(function()
    if AimbotEnabled then
        local target = GetClosestPlayer()
        if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.HumanoidRootPart.Position)
        end
    end
end)

-- ESP (Wallhack)
local function CreateESP(player)
    if EspEnabled and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local highlight = Instance.new("Highlight")
        highlight.Parent = player.Character
        highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Vermelho
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255) -- Branco
    end
end

-- Aplicar ESP nos jogadores
for _, player in pairs(Players:GetPlayers()) do
    CreateESP(player)
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        CreateESP(player)
    end)
end)
