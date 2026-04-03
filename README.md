--[[
    DAVIROBLOX HUB - VERSÃO ESTÁVEL E LEVE
    - ESP persistente (Mutant/Stalker)
    - Sem tremor de tela
    - Auto Scare, Auto Reparo, Stamina, etc.
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

-- GUI (mais simples possível)
local gui = Instance.new("ScreenGui")
gui.Name = "DaviHub"
gui.Parent = coreGui

local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 100, 0, 40)
toggleBtn.Position = UDim2.new(0.02, 0, 0.5, -20)
toggleBtn.Text = "DAVI HUB"
toggleBtn.BackgroundColor3 = Color3.fromRGB(255,140,0)
toggleBtn.Parent = gui

local menu = Instance.new("Frame")
menu.Size = UDim2.new(0, 380, 0, 500)
menu.Position = UDim2.new(0.5, -190, 0.3, 0)
menu.BackgroundColor3 = Color3.fromRGB(18,18,24)
menu.Visible = false
menu.Parent = gui

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1,0,0,40)
titleBar.BackgroundColor3 = Color3.fromRGB(255,140,0)
titleBar.Parent = menu

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0,30,0,30)
closeBtn.Position = UDim2.new(1,-35,0,5)
closeBtn.Text = "X"
closeBtn.Parent = titleBar
closeBtn.MouseButton1Click:Connect(function() menu.Visible = false end)

-- arrastar (simples)
local dragging = false
local dragStart, startPos
titleBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = i.Position
        startPos = menu.Position
    end
end)
titleBar.InputEnded:Connect(function() dragging = false end)
game:GetService("UserInputService").InputChanged:Connect(function(i)
    if dragging and i.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = i.Position - dragStart
        menu.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset+delta.X, startPos.Y.Scale, startPos.Y.Offset+delta.Y)
    end
end)

-- Abas
local tabBar = Instance.new("Frame")
tabBar.Size = UDim2.new(1,0,0,40)
tabBar.Position = UDim2.new(0,0,0,40)
tabBar.BackgroundColor3 = Color3.fromRGB(25,25,35)
tabBar.Parent = menu

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1,0,1,-80)
scroll.Position = UDim2.new(0,0,0,80)
scroll.BackgroundTransparency = 1
scroll.ScrollBarThickness = 6
scroll.Parent = menu

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0,8)
layout.Parent = scroll

local function clear()
    for _,c in pairs(scroll:GetChildren()) do
        if c:IsA("TextButton") or c:IsA("Frame") then c:Destroy() end
    end
end

local function upd()
    task.wait(0.1)
    local h = 0
    for _,c in pairs(scroll:GetChildren()) do
        if c:IsA("TextButton") or c:IsA("Frame") then h = h + 48 end
    end
    scroll.CanvasSize = UDim2.new(0,0,0,h+30)
end

local function btn(txt, cb)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.9,0,0,40)
    b.Text = txt
    b.BackgroundColor3 = Color3.fromRGB(45,45,60)
    b.TextColor3 = Color3.fromRGB(255,255,255)
    b.Parent = scroll
    b.MouseButton1Click:Connect(cb)
    return b
end

local function toggle(txt, def, cb)
    local f = Instance.new("Frame")
    f.Size = UDim2.new(0.9,0,0,40)
    f.BackgroundTransparency = 1
    f.Parent = scroll
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(0.65,0,1,0)
    l.Text = txt
    l.TextColor3 = Color3.fromRGB(220,220,220)
    l.BackgroundTransparency = 1
    l.Parent = f
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.25,0,0.8,0)
    b.Position = UDim2.new(0.72,0,0.1,0)
    b.Text = def and "On" or "Off"
    b.BackgroundColor3 = def and Color3.fromRGB(255,140,0) or Color3.fromRGB(80,80,100)
    b.TextColor3 = def and Color3.fromRGB(255,255,255) or Color3.fromRGB(255,100,100)
    b.Parent = f
    local state = def
    b.MouseButton1Click:Connect(function()
        state = not state
        b.Text = state and "On" or "Off"
        b.BackgroundColor3 = state and Color3.fromRGB(255,140,0) or Color3.fromRGB(80,80,100)
        b.TextColor3 = state and Color3.fromRGB(255,255,255) or Color3.fromRGB(255,100,100)
        cb(state)
    end)
    return f
end

-- ========== FUNCIONALIDADES ==========
local function teleport(pos)
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(pos)
    end
end

-- Stamina
local staminaLoop
local function setStamina(state)
    if staminaLoop then staminaLoop:Disconnect() end
    if state then
        staminaLoop = game:GetService("RunService").RenderStepped:Connect(function()
            local char = player.Character
            if char and char:FindFirstChild("Sprint") then
                local s = char.Sprint
                if s.Overdrive then s.Overdrive.Value = 1e9 end
                if s.Stamina then s.Stamina.Value = 100 end
            end
        end)
    end
end

-- ESP Players (sem loop)
local espHighlights = {}
local function setESP(state)
    for _,h in pairs(espHighlights) do if h and h.Parent then h:Destroy() end end
    espHighlights = {}
    if state then
        for _,p in pairs(game.Players:GetPlayers()) do
            if p ~= player and p.Character then
                local h = Instance.new("Highlight")
                h.FillColor = Color3.fromRGB(255,0,0)
                h.OutlineColor = Color3.fromRGB(200,0,0)
                h.FillTransparency = 0.5
                h.Adornee = p.Character
                h.Parent = p.Character
                espHighlights[p] = h
            end
        end
    end
end

-- Fullbright
local originalLight = {}
local function setFullbright(state)
    local lighting = game:GetService("Lighting")
    if state then
        originalLight = {Ambient=lighting.Ambient, OutdoorAmbient=lighting.OutdoorAmbient, Brightness=lighting.Brightness, GlobalShadows=lighting.GlobalShadows, FogEnd=lighting.FogEnd}
        lighting.Ambient = Color3.fromRGB(255,255,255)
        lighting.OutdoorAmbient = Color3.fromRGB(255,255,255)
        lighting.Brightness = 2
        lighting.GlobalShadows = false
        lighting.FogEnd = 1e6
    else
        lighting.Ambient = originalLight.Ambient or Color3.fromRGB(128,128,128)
        lighting.OutdoorAmbient = originalLight.OutdoorAmbient or Color3.fromRGB(128,128,128)
        lighting.Brightness = originalLight.Brightness or 1
        lighting.GlobalShadows = originalLight.GlobalShadows or true
        lighting.FogEnd = originalLight.FogEnd or 1000
    end
end

-- ========== ESP PERSISTENTE (Mutant/Stalker) ==========
local function applyESPToNPC(npc)
    if not npc or npc:FindFirstChild("ESP_NPC") then return end
    local h = Instance.new("Highlight")
    h.Name = "ESP_NPC"
    h.FillColor = Color3.fromRGB(255,50,50)
    h.OutlineColor = Color3.fromRGB(255,150,0)
    h.FillTransparency = 0.3
    h.Adornee = npc
    h.Parent = npc
end

local function refreshNPCESP()
    local mutant = workspace:FindFirstChild("Mutant") or workspace:FindFirstChild("Larry")
    if mutant and not mutant:FindFirstChild("ESP_NPC") then applyESPToNPC(mutant) end
    local stalker = workspace:FindFirstChild("Stalker")
    if stalker and not stalker:FindFirstChild("ESP_NPC") then applyESPToNPC(stalker) end
end

-- Loop leve a cada 3 segundos (não interfere na performance)
local lastRefresh = 0
game:GetService("RunService").Heartbeat:Connect(function()
    if tick() - lastRefresh >= 3 then
        lastRefresh = tick()
        refreshNPCESP()
    end
end)

-- Também reage quando novos objetos aparecem
workspace.ChildAdded:Connect(function(child)
    if child.Name == "Mutant" or child.Name == "Larry" or child.Name == "Stalker" then
        task.wait(0.5)
        refreshNPCESP()
    end
end)

-- ========== AUTO SCARE (OTIMIZADO, SEM LAG) ==========
local scareActive = false
local scareTask = nil
local lastScare = 0
local DISTANCIA = 40
local COOLDOWN = 8

local janelas = {
    { nome = "Jan1", posJan = Vector3.new(-292.1, 82.4, -38.8), posBot = Vector3.new(-288.5, 82.4, -39.6) },
    { nome = "Jan2", posJan = Vector3.new(-321.9, 82.4, -37.4), posBot = Vector3.new(-318.1, 82.4, -40.1) },
    { nome = "Jan3", posJan = Vector3.new(-311.1, 82.4, 38.7), posBot = Vector3.new(-313.0, 82.6, 35.4) },
    { nome = "Jan4", posJan = Vector3.new(-282.0, 82.4, -111.9), posBot = Vector3.new(-278.4, 82.4, -113.3) },
    { nome = "Jan5", posJan = Vector3.new(-309.6, 82.4, -111.9), posBot = Vector3.new(-307.9, 82.4, -113.4) }
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
    -- Flash leve (opcional)
    local flash = Instance.new("Frame")
    flash.Size = UDim2.new(1,0,1,0)
    flash.BackgroundColor3 = Color3.fromRGB(255,255,255)
    flash.BackgroundTransparency = 0.5
    flash.Parent = player.PlayerGui
    task.wait(0.2)
    flash:Destroy()
end

local function scareLoop()
    while scareActive do
        task.wait(1.5)  -- verifica a cada 1.5s (antes 0.5)
        local mutant = nil
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj.Name == "Mutant" or obj.Name == "Larry" then
                mutant = obj
                break
            end
        end
        if mutant then
            local mpos = mutant:FindFirstChild("HumanoidRootPart") and mutant.HumanoidRootPart.Position or mutant.Position
            local alvo, menor = nil, DISTANCIA+1
            for _, j in ipairs(janelas) do
                local d = (mpos - j.posJan).Magnitude
                if d < menor then menor = d; alvo = j end
            end
            if alvo and menor <= DISTANCIA and tick()-lastScare >= COOLDOWN then
                lastScare = tick()
                ativarLuz(alvo)
            end
        end
    end
end

local function setScare(state)
    scareActive = state
    if scareTask then coroutine.close(scareTask) end
    if state then
        scareTask = coroutine.create(scareLoop)
        coroutine.resume(scareTask)
    end
end

-- ========== AUTO REPARO (LEVE) ==========
local repairActive = false
local repairTask = nil
local function repairLoop()
    while repairActive do
        task.wait(10)  -- verifica a cada 10s
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
                    if det:IsA("ClickDetector") then fireclickdetector(det); break end
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
                    if det:IsA("ClickDetector") then fireclickdetector(det); break end
                end
            end
        end
    end
end
local function setRepair(state)
    repairActive = state
    if repairTask then coroutine.close(repairTask) end
    if state then
        repairTask = coroutine.create(repairLoop)
        coroutine.resume(repairTask)
    end
end

-- ========== CONTEÚDO DAS ABAS ==========
local function loadTeleports()
    clear()
    btn("📍 Rádio 1", function() teleport(Vector3.new(-306.4,82.4,-17.3)) end)
    btn("📍 Rádio 2", function() teleport(Vector3.new(-246.5,82.4,-16.5)) end)
    btn("📍 Delivery Screen", function() teleport(Vector3.new(-283.3,82.4,13.0)) end)
    btn("📍 Torre de Rádio", function() teleport(Vector3.new(-418.3,82.4,-16.2)) end)
    btn("📍 Boiler", function() teleport(Vector3.new(-291.7,82.4,102.9)) end)
    btn("📍 Power Storage", function() teleport(Vector3.new(-333.8,82.4,96.5)) end)
    btn("📍 Power", function() teleport(Vector3.new(-269.1,82.4,111.2)) end)
    btn("📍 Salão Principal", function() teleport(Vector3.new(-294.1,82.4,8.4)) end)
    btn("📍 Outside", function() teleport(Vector3.new(-157.5,82.4,61.4)) end)
    btn("📍 Câmeras", function() teleport(Vector3.new(-245.9,82.4,-15.7)) end)
    upd()
end

local function loadPlayers()
    clear()
    toggle("⚡ Infinite Stamina", false, setStamina)
    toggle("👁️ ESP Players", false, setESP)
    toggle("💡 Fullbright", false, setFullbright)
    upd()
end

local function loadExtras()
    clear()
    toggle("⚡ Infinite Stamina", false, setStamina)
    toggle("👁️ ESP Players", false, setESP)
    toggle("💡 Fullbright", false, setFullbright)
    toggle("🤖 Auto Scare (Mutante)", false, setScare)
    toggle("🔧 Auto Reparo", false, setRepair)
    btn("◀ Voltar para Teleports", function()
        loadTeleports()
        for _, b in pairs(tabBar:GetChildren()) do
            if b:IsA("TextButton") and b.Text == "Teleports" then
                b.BackgroundColor3 = Color3.fromRGB(255,140,0)
            elseif b:IsA("TextButton") then
                b.BackgroundColor3 = Color3.fromRGB(80,80,100)
            end
        end
    end)
    upd()
end

-- Criar abas
local function createTab(name, color, cb)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.25,0,1,0)
    b.Text = name
    b.BackgroundColor3 = color
    b.TextColor3 = Color3.fromRGB(255,255,255)
    b.Parent = tabBar
    b.MouseButton1Click:Connect(function()
        cb()
        for _, btn in pairs(tabBar:GetChildren()) do
            if btn:IsA("TextButton") then
                btn.BackgroundColor3 = (btn == b) and Color3.fromRGB(255,140,0) or Color3.fromRGB(80,80,100)
            end
        end
    end)
    return b
end

createTab("Teleports", Color3.fromRGB(255,140,0), loadTeleports)
createTab("Players", Color3.fromRGB(80,80,100), loadPlayers)
createTab("Extras", Color3.fromRGB(80,80,100), loadExtras)
loadTeleports()

local menuOpen = false
toggleBtn.MouseButton1Click:Connect(function()
    menuOpen = not menuOpen
    menu.Visible = menuOpen
end)

print("✅ DAVI HUB (versão leve) carregado. ESP do Mutant agora é permanente.")
