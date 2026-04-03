--[[
    DAVI HUB - Night 2
    Com sistema de key, notificações e toggles On/Off
]]

-- ========== SISTEMA DE NOTIFICAÇÕES ==========
local function notificar(titulo, texto, cor, duracao)
    duracao = duracao or 3
    cor = cor or Color3.fromRGB(255, 140, 0)
    
    local gui = Instance.new("ScreenGui")
    gui.Name = "Notificacao"
    gui.Parent = game:GetService("CoreGui")
    gui.ResetOnSpawn = false
    
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 300, 0, 60)
    frame.Position = UDim2.new(1, -310, 0, 10)
    frame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    frame.BackgroundTransparency = 0.1
    frame.BorderSizePixel = 0
    frame.Parent = gui
    
    local titleLbl = Instance.new("TextLabel")
    titleLbl.Size = UDim2.new(1, -10, 0, 25)
    titleLbl.Position = UDim2.new(0, 5, 0, 5)
    titleLbl.Text = titulo
    titleLbl.TextColor3 = cor
    titleLbl.BackgroundTransparency = 1
    titleLbl.Font = Enum.Font.GothamBold
    titleLbl.TextSize = 14
    titleLbl.TextXAlignment = Enum.TextXAlignment.Left
    titleLbl.Parent = frame
    
    local msgLbl = Instance.new("TextLabel")
    msgLbl.Size = UDim2.new(1, -10, 0, 25)
    msgLbl.Position = UDim2.new(0, 5, 0, 30)
    msgLbl.Text = texto
    msgLbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    msgLbl.BackgroundTransparency = 1
    msgLbl.Font = Enum.Font.Gotham
    msgLbl.TextSize = 12
    msgLbl.TextXAlignment = Enum.TextXAlignment.Left
    msgLbl.Parent = frame
    
    -- Animação de entrada
    frame:TweenPosition(UDim2.new(1, -10, 0, 10), Enum.TweenDirection.Out, Enum.TweenInfo.new(0.3), true)
    
    task.wait(duracao)
    
    -- Animação de saída
    frame:TweenPosition(UDim2.new(1, 10, 0, 10), Enum.TweenDirection.Out, Enum.TweenInfo.new(0.3), true)
    task.wait(0.3)
    gui:Destroy()
end

-- ========== SISTEMA DE KEY ==========
local key = "DAVI2024"

local keyGui = Instance.new("ScreenGui")
keyGui.Name = "KeySystem"
keyGui.Parent = game:GetService("CoreGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 300, 0, 150)
frame.Position = UDim2.new(0.5, -150, 0.5, -75)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
frame.Parent = keyGui

local label = Instance.new("TextLabel")
label.Size = UDim2.new(1, 0, 0, 30)
label.Text = "Digite a key:"
label.TextColor3 = Color3.fromRGB(255, 255, 255)
label.BackgroundTransparency = 1
label.Parent = frame

local box = Instance.new("TextBox")
box.Size = UDim2.new(0.8, 0, 0, 40)
box.Position = UDim2.new(0.1, 0, 0.4, 0)
box.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
box.TextColor3 = Color3.fromRGB(255, 255, 255)
box.Parent = frame

local btn = Instance.new("TextButton")
btn.Size = UDim2.new(0.4, 0, 0, 40)
btn.Position = UDim2.new(0.3, 0, 0.7, 0)
btn.Text = "Ativar"
btn.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Parent = frame

local errorLabel = Instance.new("TextLabel")
errorLabel.Size = UDim2.new(0.8, 0, 0, 25)
errorLabel.Position = UDim2.new(0.1, 0, 0.85, 0)
errorLabel.Text = ""
errorLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
errorLabel.BackgroundTransparency = 1
errorLabel.TextSize = 11
errorLabel.Parent = frame

btn.MouseButton1Click:Connect(function()
    if box.Text == key then
        keyGui:Destroy()
        notificar("✅ KEY VÁLIDA", "Hub carregando...", Color3.fromRGB(100, 255, 100), 2)
        carregarHub()
    else
        box.Text = ""
        errorLabel.Text = "❌ Key inválida! Tente novamente."
        notificar("❌ KEY INVÁLIDA", "A key digitada está incorreta", Color3.fromRGB(255, 100, 100), 3)
    end
end)

-- ========== HUB PRINCIPAL ==========
function carregarHub()
    local player = game.Players.LocalPlayer
    local coreGui = game:GetService("CoreGui")
    local TweenService = game:GetService("TweenService")
    local RunService = game:GetService("RunService")
    local Lighting = game:GetService("Lighting")

    -- GUI
    local gui = Instance.new("ScreenGui")
    gui.Name = "DaviHubNight2"
    gui.Parent = coreGui

    local f = Instance.new("Frame")
    f.Size = UDim2.new(0, 420, 0, 520)
    f.Position = UDim2.new(0.5, -210, 0.3, 0)
    f.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
    f.Parent = gui

    -- Barra título
    local b = Instance.new("Frame")
    b.Size = UDim2.new(1, 0, 0, 35)
    b.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    b.Parent = f

    local t = Instance.new("TextLabel")
    t.Size = UDim2.new(1, -90, 1, 0)
    t.Position = UDim2.new(0, 10, 0, 0)
    t.Text = "DAVI HUB - Night 2"
    t.TextColor3 = Color3.fromRGB(255, 255, 255)
    t.BackgroundTransparency = 1
    t.Font = Enum.Font.GothamBold
    t.TextSize = 16
    t.Parent = b

    -- Minimizar
    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 30, 0, 30)
    minBtn.Position = UDim2.new(1, -68, 0, 2.5)
    minBtn.Text = "−"
    minBtn.TextSize = 20
    minBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
    minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    minBtn.Parent = b

    -- Fechar
    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 30, 0, 30)
    closeBtn.Position = UDim2.new(1, -35, 0, 2.5)
    closeBtn.Text = "X"
    closeBtn.TextSize = 18
    closeBtn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
    closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    closeBtn.Parent = b
    closeBtn.MouseButton1Click:Connect(function() gui:Destroy() end)

    -- Arrastar
    local drag = false
    local dragStart, startPos
    b.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 then
            drag = true
            dragStart = i.Position
            startPos = f.Position
        end
    end)
    b.InputEnded:Connect(function() drag = false end)
    game:GetService("UserInputService").InputChanged:Connect(function(i)
        if drag and i.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = i.Position - dragStart
            f.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)

    -- Minimizar funcionalidade
    local minimized = false
    local scrollArea = nil
    local function getScrollArea()
        for _, child in pairs(f:GetChildren()) do
            if child:IsA("ScrollingFrame") then return child end
        end
        return nil
    end
    minBtn.MouseButton1Click:Connect(function()
        minimized = not minimized
        scrollArea = scrollArea or getScrollArea()
        if minimized then
            if scrollArea then scrollArea.Visible = false end
            TweenService:Create(f, TweenInfo.new(0.2), {Size = UDim2.new(0, 420, 0, 35)}):Play()
            minBtn.Text = "+"
        else
            TweenService:Create(f, TweenInfo.new(0.2), {Size = UDim2.new(0, 420, 0, 520)}):Play()
            task.wait(0.2)
            if scrollArea then scrollArea.Visible = true end
            minBtn.Text = "−"
        end
    end)

    -- Scroll
    local s = Instance.new("ScrollingFrame")
    s.Size = UDim2.new(1, 0, 1, -35)
    s.Position = UDim2.new(0, 0, 0, 35)
    s.BackgroundTransparency = 1
    s.ScrollBarThickness = 6
    s.Parent = f

    local layout = Instance.new("UIListLayout")
    layout.Padding = UDim.new(0, 8)
    layout.Parent = s

    -- Funções auxiliares
    function btn(txt, cb)
        local bt = Instance.new("TextButton")
        bt.Size = UDim2.new(0.9, 0, 0, 40)
        bt.Text = txt
        bt.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
        bt.TextColor3 = Color3.fromRGB(255, 255, 255)
        bt.Parent = s
        bt.MouseButton1Click:Connect(function()
            cb()
            notificar("📌 TELEPORT", "Teleportado para " .. txt, Color3.fromRGB(255, 200, 100), 2)
        end)
        bt.MouseEnter:Connect(function() bt.BackgroundColor3 = Color3.fromRGB(255, 140, 0) end)
        bt.MouseLeave:Connect(function() bt.BackgroundColor3 = Color3.fromRGB(45, 45, 60) end)
    end

    function tog(txt, def, cb)
        local fr = Instance.new("Frame")
        fr.Size = UDim2.new(0.9, 0, 0, 40)
        fr.BackgroundTransparency = 1
        fr.Parent = s
        local lb = Instance.new("TextLabel")
        lb.Size = UDim2.new(0.65, 0, 1, 0)
        lb.Text = txt
        lb.TextColor3 = Color3.fromRGB(220, 220, 220)
        lb.BackgroundTransparency = 1
        lb.Parent = fr
        local bt = Instance.new("TextButton")
        bt.Size = UDim2.new(0.25, 0, 0.8, 0)
        bt.Position = UDim2.new(0.72, 0, 0.1, 0)
        bt.Text = def and "On" or "Off"
        bt.BackgroundColor3 = def and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
        bt.TextColor3 = def and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
        bt.Parent = fr
        local st = def
        bt.MouseButton1Click:Connect(function()
            st = not st
            bt.Text = st and "On" or "Off"
            bt.BackgroundColor3 = st and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
            bt.TextColor3 = st and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
            cb(st)
            notificar("⚙️ CONFIG", txt .. " " .. (st and "ativado" or "desativado"), Color3.fromRGB(100, 200, 255), 2)
        end)
    end

    -- ========== FUNCIONALIDADES ==========
    local function teleport(pos)
        local char = player.Character
        if char and char:FindFirstChild("HumanoidRootPart") then
            char.HumanoidRootPart.CFrame = CFrame.new(pos)
        end
    end

    -- Stamina
    local staminaLoop = nil
    local function setStamina(state)
        if staminaLoop then staminaLoop:Disconnect() end
        if state then
            staminaLoop = RunService.RenderStepped:Connect(function()
                local char = player.Character
                if char and char:FindFirstChild("Sprint") then
                    local sp = char.Sprint
                    if sp.Overdrive then sp.Overdrive.Value = 1e9 end
                    if sp.Stamina then sp.Stamina.Value = 100 end
                end
            end)
        end
    end

    -- ESP Players
    local espObjs = {}
    local function setESP(state)
        for _, h in pairs(espObjs) do if h then h:Destroy() end end
        espObjs = {}
        if state then
            for _, p in pairs(game.Players:GetPlayers()) do
                if p ~= player and p.Character then
                    local h = Instance.new("Highlight")
                    h.FillColor = Color3.fromRGB(255, 0, 0)
                    h.OutlineColor = Color3.fromRGB(200, 0, 0)
                    h.FillTransparency = 0.5
                    h.Adornee = p.Character
                    h.Parent = p.Character
                    espObjs[p] = h
                end
            end
        end
    end

    -- Fullbright
    local origLight = {}
    local function setFullbright(state)
        if state then
            origLight = {Ambient = Lighting.Ambient, OutdoorAmbient = Lighting.OutdoorAmbient, Brightness = Lighting.Brightness, GlobalShadows = Lighting.GlobalShadows, FogEnd = Lighting.FogEnd}
            Lighting.Ambient = Color3.fromRGB(255, 255, 255)
            Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
            Lighting.Brightness = 2
            Lighting.GlobalShadows = false
            Lighting.FogEnd = 1e6
        else
            Lighting.Ambient = origLight.Ambient or Color3.fromRGB(128, 128, 128)
            Lighting.OutdoorAmbient = origLight.OutdoorAmbient or Color3.fromRGB(128, 128, 128)
            Lighting.Brightness = origLight.Brightness or 1
            Lighting.GlobalShadows = origLight.GlobalShadows or true
            Lighting.FogEnd = origLight.FogEnd or 1000
        end
    end

    -- ESP Mutant/Stalker persistente
    local function addNPCESP(npc, cor)
        if not npc or npc:FindFirstChild("ESP_NPC") then return end
        local h = Instance.new("Highlight")
        h.Name = "ESP_NPC"
        h.FillColor = cor or Color3.fromRGB(255, 50, 50)
        h.OutlineColor = Color3.fromRGB(255, 150, 0)
        h.FillTransparency = 0.3
        h.Adornee = npc
        h.Parent = npc
    end

    local function refreshNPCs()
        local mutant = workspace:FindFirstChild("Mutant") or workspace:FindFirstChild("Larry")
        if mutant and not mutant:FindFirstChild("ESP_NPC") then
            addNPCESP(mutant, Color3.fromRGB(255, 0, 0))
        end
        local stalker = workspace:FindFirstChild("Stalker")
        if stalker and not stalker:FindFirstChild("ESP_NPC") then
            addNPCESP(stalker, Color3.fromRGB(255, 100, 0))
        end
    end

    RunService.Heartbeat:Connect(function()
        if tick() % 3 < 0.1 then refreshNPCs() end
    end)
    workspace.ChildAdded:Connect(function(child)
        if child.Name == "Mutant" or child.Name == "Larry" or child.Name == "Stalker" then
            task.wait(0.5); refreshNPCs()
        end
    end)

    -- Invencibilidade
    local invConn = nil
    local invState = false
    local function setInvencivel(state)
        invState = state
        if invConn then invConn:Disconnect() end
        local char = player.Character
        if not char then return end
        local hum = char:FindFirstChild("Humanoid")
        if not hum then return end
        if state then
            hum.Health = hum.MaxHealth
            invConn = hum:GetPropertyChangedSignal("Health"):Connect(function()
                if invState and hum.Health < hum.MaxHealth then hum.Health = hum.MaxHealth end
            end)
            local old = hum.TakeDamage
            hum.TakeDamage = function(self, amt)
                if invState then return end
                return old(self, amt)
            end
        else
            if invConn then invConn:Disconnect() end
        end
    end

    -- Auto Scare
    local scareActive = false
    local scareTask = nil
    local lastScare = 0
    local DIST = 40
    local COOLDOWN = 8
    local janelas = {
        {nome="Jan1", posJan=Vector3.new(-292.1, 82.4, -38.8), posBot=Vector3.new(-288.5, 82.4, -39.6)},
        {nome="Jan2", posJan=Vector3.new(-321.9, 82.4, -37.4), posBot=Vector3.new(-318.1, 82.4, -40.1)},
        {nome="Jan3", posJan=Vector3.new(-311.1, 82.4, 38.7), posBot=Vector3.new(-313.0, 82.6, 35.4)},
        {nome="Jan4", posJan=Vector3.new(-282.0, 82.4, -111.9), posBot=Vector3.new(-278.4, 82.4, -113.3)},
        {nome="Jan5", posJan=Vector3.new(-309.6, 82.4, -111.9), posBot=Vector3.new(-307.9, 82.4, -113.4)}
    }
    local function ativarLuz(jan)
        teleport(jan.posBot)
        task.wait(0.2)
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("ClickDetector") and (obj.Parent.Position - jan.posBot).Magnitude < 8 then
                fireclickdetector(obj)
                break
            end
        end
        local flash = Instance.new("Frame")
        flash.Size = UDim2.new(1,0,1,0)
        flash.BackgroundColor3 = Color3.fromRGB(255,255,255)
        flash.BackgroundTransparency = 0.5
        flash.Parent = player.PlayerGui
        task.wait(0.2)
        flash:Destroy()
        notificar("🤖 AUTO SCARE", "Mutante detectado na " .. jan.nome .. "! Luz ativada.", Color3.fromRGB(255, 100, 100), 3)
    end
    local function scareLoop()
        while scareActive do
            task.wait(1)
            local mutant = nil
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj.Name == "Mutant" or obj.Name == "Larry" then mutant = obj; break end
            end
            if mutant then
                local mpos = mutant:FindFirstChild("HumanoidRootPart") and mutant.HumanoidRootPart.Position or mutant.Position
                local alvo, menor = nil, DIST+1
                for _, j in ipairs(janelas) do
                    local d = (mpos - j.posJan).Magnitude
                    if d < menor then menor = d; alvo = j end
                end
                if alvo and menor <= DIST and tick() - lastScare >= COOLDOWN then
                    lastScare = tick()
                    ativarLuz(alvo)
                end
            end
        end
    end
    local function setAutoScare(state)
        scareActive = state
        if scareTask then coroutine.close(scareTask); scareTask = nil end
        if state then scareTask = coroutine.create(scareLoop); coroutine.resume(scareTask) end
    end

    -- Auto Reparo
    local repairActive = false
    local repairTask = nil
    local function repairLoop()
        while repairActive do
            task.wait(10)
            local gen = nil
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj.Name:lower():find("generator") then gen = obj; break end
            end
            if gen then
                local pos = gen.Position or (gen:FindFirstChildWhichIsA("BasePart") and gen:FindFirstChildWhichIsA("BasePart").Position)
                if pos then
                    teleport(pos + Vector3.new(0,3,0))
                    task.wait(0.5)
                    for _, det in pairs(gen:GetDescendants()) do
                        if det:IsA("ClickDetector") then fireclickdetector(det); notificar("🔧 AUTO REPARO", "Gerador reparado!", Color3.fromRGB(100, 200, 100), 2); break end
                    end
                end
            end
            task.wait(2)
            local fus = nil
            for _, obj in pairs(workspace:GetDescendants()) do
                if obj.Name:lower():find("fuse") or obj.Name:lower():find("caixa") then fus = obj; break end
            end
            if fus then
                local pos = fus.Position or (fus:FindFirstChildWhichIsA("BasePart") and fus:FindFirstChildWhichIsA("BasePart").Position)
                if pos then
                    teleport(pos + Vector3.new(0,2,0))
                    task.wait(0.5)
                    for _, det in pairs(fus:GetDescendants()) do
                        if det:IsA("ClickDetector") then fireclickdetector(det); notificar("🔧 AUTO REPARO", "Caixa de fusíveis reparada!", Color3.fromRGB(100, 200, 100), 2); break end
                    end
                end
            end
        end
    end
    local function setAutoReparo(state)
        repairActive = state
        if repairTask then coroutine.close(repairTask); repairTask = nil end
        if state then repairTask = coroutine.create(repairLoop); coroutine.resume(repairTask) end
    end

    -- ========== TELEPORTS ==========
    btn("📍 Rádio 1", function() teleport(Vector3.new(-306.4, 82.4, -17.3)) end)
    btn("📍 Rádio 2", function() teleport(Vector3.new(-246.5, 82.4, -16.5)) end)
    btn("📍 Delivery Screen", function() teleport(Vector3.new(-283.3, 82.4, 13.0)) end)
    btn("📍 Torre de Rádio", function() teleport(Vector3.new(-418.3, 82.4, -16.2)) end)
    btn("📍 Boiler", function() teleport(Vector3.new(-291.7, 82.4, 102.9)) end)
    btn("📍 Power Storage", function() teleport(Vector3.new(-333.8, 82.4, 96.5)) end)
    btn("📍 Power", function() teleport(Vector3.new(-269.1, 82.4, 111.2)) end)
    btn("📍 Salão Principal", function() teleport(Vector3.new(-294.1, 82.4, 8.4)) end)
    btn("📍 Outside", function() teleport(Vector3.new(-157.5, 82.4, 61.4)) end)
    btn("📍 Câmeras", function() teleport(Vector3.new(-245.9, 82.4, -15.7)) end)

    -- ========== TOGGLES ==========
    tog("⚡ Infinite Stamina", false, setStamina)
    tog("👁️ ESP Players", false, setESP)
    tog("💡 Fullbright", false, setFullbright)
    tog("🛡️ Invencível (Zumbis)", false, setInvencivel)
    tog("🤖 Auto Scare (Mutante)", false, setAutoScare)
    tog("🔧 Auto Reparo (Gerador/Fusível)", false, setAutoReparo)

    -- Ajustar scroll
    task.wait(0.1)
    local totalH = 0
    for _, child in pairs(s:GetChildren()) do
        if child:IsA("TextButton") or child:IsA("Frame") then totalH = totalH + 48 end
    end
    s.CanvasSize = UDim2.new(0, 0, 0, totalH + 30)

    notificar("🎉 HUB CARREGADO", "DAVI HUB - Night 2 pronto para uso!", Color3.from
