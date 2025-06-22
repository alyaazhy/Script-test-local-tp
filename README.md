# Script-test-local-tp
--[[
    RiotScripts Hub - LocalPlayer Click to Teleport (PC/Mobile)
    Autor: Copilot
    Fundo: Itachi
    Minimizar: Sim (ícone X)
]]

-- Configurações iniciais
local itachiBackground = "rbxassetid://14251186843" -- Substitua por outro ID se quiser outro fundo
local hubName = "RiotScripts"
local user = game.Players.LocalPlayer

-- Criando a GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = hubName.."Gui"
screenGui.ResetOnSpawn = false
screenGui.Parent = game.CoreGui

-- Frame principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 400, 0, 250)
mainFrame.Position = UDim2.new(0.5, -200, 0.4, -125)
mainFrame.BackgroundTransparency = 0
mainFrame.BackgroundColor3 = Color3.fromRGB(30,30,30)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

-- Imagem de fundo do Itachi
local bgImage = Instance.new("ImageLabel")
bgImage.Name = "Background"
bgImage.Size = UDim2.new(1, 0, 1, 0)
bgImage.Position = UDim2.new(0,0,0,0)
bgImage.Image = itachiBackground
bgImage.BackgroundTransparency = 1
bgImage.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Name = "Title"
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.Text = hubName
title.Font = Enum.Font.GothamBold
title.TextSize = 28
title.TextColor3 = Color3.fromRGB(255, 0, 0)
title.BackgroundTransparency = 1
title.Parent = mainFrame

-- Botão Minimizar (X)
local closeButton = Instance.new("TextButton")
closeButton.Name = "CloseButton"
closeButton.Size = UDim2.new(0, 40, 0, 40)
closeButton.Position = UDim2.new(1, -45, 0, 5)
closeButton.Text = "✕"
closeButton.TextColor3 = Color3.fromRGB(255,255,255)
closeButton.TextSize = 28
closeButton.BackgroundColor3 = Color3.fromRGB(230, 38, 38)
closeButton.BorderSizePixel = 0
closeButton.AutoButtonColor = true
closeButton.Parent = mainFrame

-- Área para instrução
local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, -30, 0, 40)
infoLabel.Position = UDim2.new(0, 15, 0, 50)
infoLabel.Text = "Clique (PC) ou toque (Mobile) pelo mapa para teleportar!"
infoLabel.Font = Enum.Font.Gotham
infoLabel.TextSize = 18
infoLabel.TextColor3 = Color3.fromRGB(255,255,255)
infoLabel.BackgroundTransparency = 0.6
infoLabel.BackgroundColor3 = Color3.fromRGB(0,0,0)
infoLabel.Parent = mainFrame

-- Minimizar função
local minimized = false
closeButton.MouseButton1Click:Connect(function()
    minimized = not minimized
    mainFrame.Visible = not minimized
    -- Botão para reabrir aparece no canto
    if minimized then
        if not screenGui:FindFirstChild("RestoreButton") then
            local restore = Instance.new("TextButton")
            restore.Name = "RestoreButton"
            restore.Size = UDim2.new(0, 60, 0, 30)
            restore.Position = UDim2.new(0, 10, 0, 10)
            restore.Text = hubName
            restore.TextColor3 = Color3.fromRGB(255,255,255)
            restore.TextSize = 16
            restore.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
            restore.Parent = screenGui
            restore.MouseButton1Click:Connect(function()
                mainFrame.Visible = true
                minimized = false
                restore:Destroy()
            end)
        end
    else
        local restore = screenGui:FindFirstChild("RestoreButton")
        if restore then restore:Destroy() end
    end
end)

-- Função de Teleport (PC & Mobile)
local function teleportTo(pos)
    local character = user.Character or user.CharacterAdded:Wait()
    local root = character:FindFirstChild("HumanoidRootPart")
    if root then
        root.CFrame = CFrame.new(pos + Vector3.new(0, 5, 0))
    end
end

-- Detectar clique (PC)
local uis = game:GetService("UserInputService")
local mouse = user:GetMouse()
mouse.Button1Down:Connect(function()
    if not mainFrame.Visible then return end
    -- Ignora se clicar na interface
    if uis:GetFocusedTextBox() or uis:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then return end
    -- Raycast posição do mouse
    local unitRay = workspace.CurrentCamera:ScreenPointToRay(mouse.X, mouse.Y)
    local ray = Ray.new(unitRay.Origin, unitRay.Direction * 500)
    local part, pos = workspace:FindPartOnRay(ray, user.Character, false, true)
    if pos then
        teleportTo(pos)
    end
end)

-- Detectar toque (Mobile)
local ts = game:GetService("TouchInputService")
ts.TouchEnded:Connect(function(touch, gp)
    if not mainFrame.Visible then return end
    local pos = touch.Position
    local camera = workspace.CurrentCamera
    local unitRay = camera:ScreenPointToRay(pos.X, pos.Y)
    local ray = Ray.new(unitRay.Origin, unitRay.Direction * 500)
    local part, hitPos = workspace:FindPartOnRay(ray, user.Character, false, true)
    if hitPos then
        teleportTo(hitPos)
    end
end)

-- Fim do script
