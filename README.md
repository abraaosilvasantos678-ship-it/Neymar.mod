local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- ==================== CONFIGURAÇÕES (LIGADAS POR PADRÃO) ====================
local Settings = {
    ESP = true,
    Tracers = true,
    Aimbot = true,
    Hitbox = true,
    FOV = 120,
    HitboxSize = 15,
    MenuOpen = true,
    ShowBtn = true -- Toggle para o ícone do Neymar
}

-- ==================== UI MOBILE COMPACTA ====================
local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
screenGui.Name = "NeymarV3_MobileFinal"
screenGui.ResetOnSpawn = false

-- Botão Flutuante (Ícone Neymar)
local toggleBtn = Instance.new("ImageButton", screenGui)
toggleBtn.Size = UDim2.new(0, 45, 0, 45)
toggleBtn.Position = UDim2.new(0.05, 0, 0.2, 0)
toggleBtn.Image = "rbxassetid://16377708304"
toggleBtn.BackgroundColor3 = Color3.new(0,0,0)
toggleBtn.Visible = Settings.ShowBtn
Instance.new("UICorner", toggleBtn).CornerRadius = UDim.new(1, 0)

-- Painel Principal Compacto
local main = Instance.new("Frame", screenGui)
main.Size = UDim2.new(0, 145, 0, 300) 
main.Position = UDim2.new(0.5, -72, 0.4, 0)
main.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
main.Visible = Settings.MenuOpen
Instance.new("UICorner", main)
local stroke = Instance.new("UIStroke", main)
stroke.Color = Color3.fromRGB(200, 0, 0)

local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "NEYMAR V3 FINAL"
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.GothamBold
title.TextSize = 11
title.BackgroundTransparency = 1

local container = Instance.new("ScrollingFrame", main)
container.Size = UDim2.new(1, -10, 1, -40)
container.Position = UDim2.new(0, 5, 0, 35)
container.BackgroundTransparency = 1
container.ScrollBarThickness = 0
local layout = Instance.new("UIListLayout", container)
layout.Padding = UDim.new(0, 4)

-- ==================== SISTEMA DE TRACERS ====================
local tracerLines = {}
local function getTracer()
    local line = Instance.new("Frame")
    line.BorderSizePixel = 0
    line.BackgroundColor3 = Color3.new(1, 0, 0)
    line.AnchorPoint = Vector2.new(0.5, 0.5)
    line.Visible = false
    line.Parent = screenGui
    return line
end

-- ==================== FUNÇÕES AUXILIARES ====================
local function makeDraggable(obj)
    local dragStart, startPos, dragging
    obj.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true; dragStart = input.Position; startPos = obj.Position
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart
            obj.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    obj.InputEnded:Connect(function() dragging = false end)
end

local function createToggle(text, key, callback)
    local b = Instance.new("TextButton", container)
    b.Size = UDim2.new(1, 0, 0, 30)
    b.BackgroundColor3 = Settings[key] and Color3.fromRGB(150, 0, 0) or Color3.fromRGB(40, 40, 40)
    b.Text = text .. (Settings[key] and ": ON" or ": OFF")
    b.TextColor3 = Color3.new(1,1,1)
    b.Font = Enum.Font.Gotham; b.TextSize = 10
    Instance.new("UICorner", b)

    b.MouseButton1Click:Connect(function()
        Settings[key] = not Settings[key]
        b.Text = text .. (Settings[key] and ": ON" or ": OFF")
        b.BackgroundColor3 = Settings[key] and Color3.fromRGB(150, 0, 0) or Color3.fromRGB(40, 40, 40)
        if callback then callback(Settings[key]) end
    end)
end

local function createAdjuster(name, key)
    local f = Instance.new("Frame", container)
    f.Size = UDim2.new(1, 0, 0, 40); f.BackgroundTransparency = 1
    local l = Instance.new("TextLabel", f); l.Size = UDim2.new(1,0,0,15); l.Text = name..": "..Settings[key]; l.TextColor3 = Color3.new(1,1,1); l.TextSize = 9; l.BackgroundTransparency = 1
    local m = Instance.new("TextButton", f); m.Size = UDim2.new(0.48,0,0,20); m.Position = UDim2.new(0,0,0,15); m.Text = "-"; m.BackgroundColor3 = Color3.fromRGB(50,50,50); m.TextColor3 = Color3.new(1,1,1)
    local p = Instance.new("TextButton", f); p.Size = UDim2.new(0.5,0,0,20); p.Position = UDim2.new(0.5,0,0,15); p.Text = "+"; p.BackgroundColor3 = Color3.fromRGB(50,50,50); p.TextColor3 = Color3.new(1,1,1)
    Instance.new("UICorner", m); Instance.new("UICorner", p)
    p.MouseButton1Click:Connect(function() Settings[key] = Settings[key] + 5; l.Text = name..": "..Settings[key] end)
    m.MouseButton1Click:Connect(function() Settings[key] = math.max(0, Settings[key] - 5); l.Text = name..": "..Settings[key] end)
end

-- Adicionar Itens ao Menu
createToggle("👁️ ESP", "ESP")
createToggle("📏 TRACERS", "Tracers")
createToggle("🎯 AIMBOT", "Aimbot")
createToggle("📦 HITBOX", "Hitbox")
createToggle("🔘 BTN FLUTUANTE", "ShowBtn", function(v) toggleBtn.Visible = v end)
createAdjuster("TAM. HITBOX", "HitboxSize")
createAdjuster("RAIO FOV", "FOV")

makeDraggable(main); makeDraggable(toggleBtn)

toggleBtn.MouseButton1Click:Connect(function()
    Settings.MenuOpen = not Settings.MenuOpen
    main.Visible = Settings.MenuOpen
end)

local circle = Instance.new("Frame", screenGui)
circle.BackgroundColor3 = Color3.new(1,1,1); circle.BackgroundTransparency = 0.92; circle.AnchorPoint = Vector2.new(0.5,0.5); circle.Position = UDim2.new(0.5,0,0.5,0)
Instance.new("UICorner", circle).CornerRadius = UDim.new(1,0)

-- ==================== LOOP DE EXECUÇÃO ====================
RunService.RenderStepped:Connect(function()
    circle.Visible = Settings.Aimbot
    circle.Size = UDim2.new(0, Settings.FOV * 2, 0, Settings.FOV * 2)

    local playerList = Players:GetPlayers()
    for i = #tracerLines + 1, #playerList do table.insert(tracerLines, getTracer()) end

    local tracerIdx = 1
    for _, p in pairs(playerList) do
        if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = p.Character.HumanoidRootPart
            
            -- ESP / Hitbox
            if Settings.Hitbox then
                hrp.Size = Vector3.new(Settings.HitboxSize, Settings.HitboxSize, Settings.HitboxSize)
                hrp.Transparency = 0.8; hrp.CanCollide = false
            else
                hrp.Size = Vector3.new(2,2,1); hrp.Transparency = 1; hrp.CanCollide = true
            end
            
            if Settings.ESP then
                if not p.Character:FindFirstChild("Highlight") then
                    Instance.new("Highlight", p.Character).FillColor = Color3.new(1,0,0)
                end
            elseif p.Character:FindFirstChild("Highlight") then p.Character.Highlight:Destroy() end

            -- Tracers
            local tracer = tracerLines[tracerIdx]
            if Settings.Tracers then
                local pos, vis = Camera:WorldToViewportPoint(hrp.Position)
                if vis then
                    local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
                    local target = Vector2.new(pos.X, pos.Y)
                    local dist = (target - center).Magnitude
                    tracer.Size = UDim2.new(0, dist, 0, 1)
                    tracer.Position = UDim2.new(0, (center.X + target.X)/2, 0, (center.Y + target.Y)/2)
                    tracer.Rotation = math.atan2(target.Y - center.Y, target.X - center.X) * (180/math.pi)
                    tracer.Visible = true
                else tracer.Visible = false end
            else tracer.Visible = false end
            tracerIdx = tracerIdx + 1
        end
    end

    for i = tracerIdx, #tracerLines do tracerLines[i].Visible = false end

    -- Aimbot
    if Settings.Aimbot then
        local target, dist = nil, Settings.FOV
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= player and p.Character and p.Character:FindFirstChild("Head") and p.Character.Humanoid.Health > 0 then
                local pos, vis = Camera:WorldToViewportPoint(p.Character.Head.Position)
                if vis then
                    local mag = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                    if mag < dist then dist = mag; target = p.Character.Head end
                end
            end
        end
        if target then Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position) end
    end
end)
