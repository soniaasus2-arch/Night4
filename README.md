--[[
    DAVIROBLOX HUB | Night 2
    - Animação de carregamento
    - 10 Teleports
    - Infinite Stamina
    - ESP Players
    - Fullbright
    - ESP Mutant + Stalker
    - Assustar Mutant (manual)
    - Auto reparos (gerador + fusíveis)
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")

-- ========== ANIMAÇÃO DE CARREGAMENTO ==========
local function mostrarAnimacao()
    local animGui = Instance.new("ScreenGui")
    animGui.Name = "AnimacaoCarregando"
    animGui.Parent = coreGui

    local bg = Instance.new("Frame")
    bg.Size = UDim2.new(1, 0, 1, 0)
    bg.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    bg.BackgroundTransparency = 0.3
    bg.Parent = animGui

    local card = Instance.new("Frame")
    card.Size = UDim2.new(0, 350, 0, 150)
    card.Position = UDim2.new(0.5, -175, 0.5, -75)
    card.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    card.BorderSizePixel = 0
    card.Parent = animGui

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Position = UDim2.new(0, 0, 0, 15)
    title.Text = "DAVIROBLOX HUB"
    title.TextSize = 22
    title.TextColor3 = Color3.fromRGB(255, 200, 100)
    title.BackgroundTransparency = 1
    title.Font = Enum.Font.GothamBold
    title.Parent = card

    local barBg = Instance.new("Frame")
    barBg.Size = UDim2.new(0.8, 0, 0, 8)
    barBg.Position = UDim2.new(0.1, 0, 0.55, 0)
    barBg.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    barBg.BorderSizePixel = 0
    barBg.Parent = card

    local barFill = Instance.new("Frame")
    barFill.Size = UDim2.new(0, 0, 1, 0)
    barFill.BackgroundColor3 = Color3.fromRGB(255, 100, 100)
    barFill.BorderSizePixel = 0
    barFill.Parent = barBg

    local percent = Instance.new("TextLabel")
    percent.Size = UDim2.new(1, 0, 0, 25)
    percent.Position = UDim2.new(0, 0, 0, 75)
    percent.Text = "0%"
    percent.TextSize = 14
    percent.TextColor3 = Color3.fromRGB(200, 200, 200)
    percent.BackgroundTransparency = 1
    percent.Parent = card

    for i = 0, 100 do
        barFill.Size = UDim2.new(i / 100, 0, 1, 0)
        percent.Text = i .. "%"
        task.wait(0.015)
    end

    task.wait(0.3)
    for i = 0, 10 do
        card.BackgroundTransparency = i / 10
        bg.BackgroundTransparency = 0.3 + (i / 10) * 0.7
        title.TextTransparency = i / 10
        percent.TextTransparency = i / 10
        barBg.BackgroundTransparency = i / 10
        barFill.BackgroundTransparency = i / 10
        task.wait(0.04)
    end
    animGui:Destroy()
end

-- Executa animação
mostrarAnimacao()
task.wait(1.8)  -- Aguarda a animação terminar

-- ========== HUB PRINCIPAL ==========
local gui = Instance.new("ScreenGui")
gui.Name = "DaviHubFinal"
gui.ResetOnSpawn = false
gui.Parent = coreGui

-- Botão abrir
local toggleBtn = Instance.new("TextButton")
toggleBtn.Size = UDim2.new(0, 60, 0, 60)
toggleBtn.Position = UDim2.new(0.02, 0, 0.5, -30)
toggleBtn.Text = "📱"
toggleBtn.TextSize = 30
toggleBtn.BackgroundColor3 = Color3.fromRGB(25,25,35)
toggleBtn.TextColor3 = Color3.fromRGB(255,255,255)
toggleBtn.BorderSizePixel = 0
toggleBtn.Parent = gui

-- Menu
local menu = Instance.new("Frame")
menu.Size = UDim2.new(0, 340, 0, 580)
menu.Position = UDim2.new(0.1, 0, 0.3, 0)
menu.BackgroundColor3 = Color3.fromRGB(20,20,30)
menu.BackgroundTransparency = 0.05
menu.Visible = false
menu.Parent = gui

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "DAVIROBLOX HUB | Night 2"
title.TextColor3 = Color3.fromRGB(255,200,100)
title.BackgroundColor3 = Color3.fromRGB(30,30,45)
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.Parent = menu

-- Fechar
local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 40, 0, 30)
closeBtn.Position = UDim2.new(1, -45, 0, 5)
closeBtn.Text = "X"
closeBtn.TextSize = 14
closeBtn.BackgroundColor3 = Color3.fromRGB(150,50,50)
closeBtn.TextColor3 = Color3.fromRGB(255,255,255)
closeBtn.Parent = menu
closeBtn.MouseButton1Click:Connect(function()
    menu.Visible = false
end)

-- ScrollFrame
local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, 0, 1, -45)
scroll.Position = UDim2.new(0, 0, 0, 45)
scroll.BackgroundTransparency = 1
scroll.ScrollBarThickness = 6
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.Parent = menu

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0, 6)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = scroll

-- ========== FUNÇÕES AUXILIARES ==========
local function addButton(text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 36)
    btn.Text = text
    btn.TextSize = 14
    btn.BackgroundColor3 = Color3.fromRGB(45,45,60)
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BorderSizePixel = 0
    btn.MouseButton1Click:Connect(callback)
    btn.Parent = scroll
    return btn
end

local function addToggle(text, onChange)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 36)
    frame.BackgroundTransparency = 1
    frame.Parent = scroll
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.65, 0, 1, 0)
    label.Text = text
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextColor3 = Color3.fromRGB(220,220,220)
    label.BackgroundTransparency = 1
    label.TextSize = 14
    label.Parent = frame
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.25, 0, 0.8, 0)
    btn.Position = UDim2.new(0.72, 0, 0.1, 0)
    btn.Text = "Off"
    btn.TextSize = 13
    btn.BackgroundColor3 = Color3.fromRGB(80,80,100)
    btn.TextColor3 = Color3.fromRGB(255,100,100)
    btn.BorderSizePixel = 0
    btn.Parent = frame
    local state = false
    btn.MouseButton1Click:Connect(function()
        state = not state
        btn.Text = state and "On" or "Off"
        btn.BackgroundColor3 = state and Color3.fromRGB(60,150,60) or Color3.fromRGB(80,80,100)
        btn.TextColor3 = state and Color3.fromRGB(100,255,100) or Color3.fromRGB(255,100,100)
        onChange(state)
    end)
    return frame
end

-- ========== TELEPORT ==========
local function teleportTo(pos)
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(pos)
        print("✅ Teleportado!")
    end
end

-- ========== INFINITE STAMINA ==========
local staminaLoop = nil
local function setInfiniteStamina(state)
    if staminaLoop then staminaLoop:Disconnect() end
    if state then
        staminaLoop = game:GetService("RunService").RenderStepped:Connect(function()
            local char = player.Character
            if char then
                local sprint = char:FindFirstChild("Sprint")
                if sprint then
                    if sprint:FindFirstChild("Overdrive") then sprint.Overdrive.Value = 99999999 end
                    if sprint:FindFirstChild("Stamina") then sprint.Stamina.Value = 100 end
                end
            end
        end)
        print("✅ Stamina Infinita ON")
    else
        print("❌ Stamina Infinita OFF")
    end
end

-- ========== ESP PLAYERS ==========
local espHighlights = {}
local function clearESP()
    for _, h in pairs(espHighlights) do
        if h and h.Parent then h:Destroy() end
    end
    espHighlights = {}
end
local function addESPToPlayer(plr)
    if plr == player then return end
    local function createESP(character)
        if not character or character:FindFirstChild("ESP_Player") then return end
        local h = Instance.new("Highlight")
        h.Name = "ESP_Player"
        h.FillColor = Color3.fromRGB(255,0,0)
        h.OutlineColor = Color3.fromRGB(200,0,0)
        h.FillTransparency = 0.5
        h.Adornee = character
        h.Parent = character
        espHighlights[plr] = h
    end
    if plr.Character then createESP(plr.Character) end
    plr.CharacterAdded:Connect(createESP)
end
local function setESP(state)
    if state then
        clearESP()
        for _, p in pairs(game.Players:GetPlayers()) do addESPToPlayer(p) end
        game.Players.PlayerAdded:Connect(addESPToPlayer)
        print("✅ ESP Players ON")
    else
        clearESP()
        print("❌ ESP Players OFF")
    end
end

-- ========== FULLBRIGHT ==========
local originalLight = {}
local function setFullbright(state)
    local lighting = game:GetService("Lighting")
    if state then
        originalLight = {
            Ambient = lighting.Ambient,
            OutdoorAmbient = lighting.OutdoorAmbient,
            Brightness = lighting.Brightness,
            GlobalShadows = lighting.GlobalShadows,
            FogEnd = lighting.FogEnd
        }
        lighting.Ambient = Color3.fromRGB(255,255,255)
        lighting.OutdoorAmbient = Color3.fromRGB(255,255,255)
        lighting.Brightness = 2
        lighting.GlobalShadows = false
        lighting.FogEnd = 100000
        print("✅ Fullbright ON")
    else
        lighting.Ambient = originalLight.Ambient or Color3.fromRGB(128,128,128)
        lighting.OutdoorAmbient = originalLight.OutdoorAmbient or Color3.fromRGB(128,128,128)
        lighting.Brightness = originalLight.Brightness or 1
        lighting.GlobalShadows = originalLight.GlobalShadows or true
        lighting.FogEnd = originalLight.FogEnd or 1000
        print("❌ Fullbright OFF")
    end
end

-- ========== ESP MUTANT + STALKER ==========
local function addNPCESP(npc)
    if not npc or npc:FindFirstChild("ESP_NPC") then return end
    local h = Instance.new("Highlight")
    h.Name = "ESP_NPC"
    h.FillColor = Color3.fromRGB(255,50,50)
    h.OutlineColor = Color3.fromRGB(255,150,0)
    h.FillTransparency = 0.3
    h.Adornee = npc
    h.Parent = npc
end
local function setupNPCESP()
    local mutant = workspace:FindFirstChild("Mutant") or workspace:FindFirstChild("Larry")
    if mutant then addNPCESP(mutant) end
    local stalker = workspace:FindFirstChild("Stalker")
    if stalker then addNPCESP(stalker) end
    workspace.ChildAdded:Connect(function(child)
        if child.Name == "Mutant" or child.Name == "Larry" or child.Name == "Stalker" then
            addNPCESP(child)
        end
    end)
end
setupNPCESP()

-- ========== ASSUSTAR MUTANT (MANUAL) ==========
local function scareMutant()
    local mutant = nil
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name == "Mutant" or obj.Name == "Larry" then
            mutant = obj
            break
        end
    end
    if not mutant then print("❌ Mutant não encontrado"); return end
    local hum = mutant:FindFirstChild("Humanoid")
    if hum then hum.Jump = true end
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://9120386438"
    sound.Volume = 0.8
    sound.Parent = mutant
    sound:Play()
    local flash = Instance.new("Frame")
    flash.Size = UDim2.new(1,0,1,0)
    flash.BackgroundColor3 = Color3.fromRGB(255,255,255)
    flash.BackgroundTransparency = 0.5
    flash.Parent = gui
    task.wait(0.15)
    flash:Destroy()
    local stalker = workspace:FindFirstChild("Stalker")
    if stalker and stalker:FindFirstChild("Humanoid") then
        local orig = stalker.Humanoid.WalkSpeed
        stalker.Humanoid.WalkSpeed = 25
        task.wait(4)
        stalker.Humanoid.WalkSpeed = orig or 16
    end
    print("💀 Mutant assustado!")
end

-- ========== AUTO REPAROS ==========
local function clickDetector(detector)
    if detector and detector:IsA("ClickDetector") then
        fireclickdetector(detector)
        return true
    end
    return false
end

local function autoRepairGenerator()
    local char = player.Character
    if not char then return end
    local generator = nil
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name:lower():find("generator") or obj.Name:lower():find("gerador") then
            generator = obj
            break
        end
    end
    if not generator then print("❌ Gerador não encontrado"); return end
    local pos = generator:FindFirstChild("Position") and generator.Position.Value 
                or (generator:FindFirstChildWhichIsA("BasePart") and generator:FindFirstChildWhichIsA("BasePart").Position)
    if pos then
        char.HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0,3,0))
        task.wait(0.5)
    end
    local clicked = false
    for _, det in pairs(generator:GetDescendants()) do
        if det:IsA("ClickDetector") then
            fireclickdetector(det)
            clicked = true
            print("🔧 Gerador reparado")
            task.wait(0.3)
        end
    end
    if not clicked then print("⚠️ Nenhum ClickDetector no gerador") end
end

local function autoRepairFuseBox()
    local char = player.Character
    if not char then return end
    local fuseBox = nil
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name:lower():find("fuse") or obj.Name:lower():find("fusivel") or obj.Name:lower():find("caixa") then
            fuseBox = obj
            break
        end
    end
    if not fuseBox then print("❌ Caixa de fusíveis não encontrada"); return end
    local pos = fuseBox:FindFirstChild("Position") and fuseBox.Position.Value 
                or (fuseBox:FindFirstChildWhichIsA("BasePart") and fuseBox:FindFirstChildWhichIsA("BasePart").Position)
    if pos then
        char.HumanoidRootPart.CFrame = CFrame.new(pos + Vector3.new(0,2,0))
        task.wait(0.5)
    end
    local clicked = false
    for _, det in pairs(fuseBox:GetDescendants()) do
        if det:IsA("ClickDetector") then
            fireclickdetector(det)
            clicked = true
            print("🔧 Caixa de fusíveis reparada")
            task.wait(0.3)
        end
    end
    if not clicked then print("⚠️ Nenhum ClickDetector na caixa de fusíveis") end
end

local function autoRepairAll()
    print("🔧 Iniciando reparo completo...")
    autoRepairGenerator()
    task.wait(1)
    autoRepairFuseBox()
    print("✅ Reparo completo finalizado")
end

-- ========== MONTAR MENU ==========
-- Teleports
addButton("📍 Rádio 1", function() teleportTo(Vector3.new(-306.4, 82.4, -17.3)) end)
addButton("📍 Rádio 2", function() teleportTo(Vector3.new(-246.5, 82.4, -16.5)) end)
addButton("📍 Delivery Screen", function() teleportTo(Vector3.new(-283.3, 82.4, 13.0)) end)
addButton("📍 Torre de Rádio", function() teleportTo(Vector3.new(-418.3, 82.4, -16.2)) end)
addButton("📍 Boiler", function() teleportTo(Vector3.new(-291.7, 82.4, 102.9)) end)
addButton("📍 Power Storage", function() teleportTo(Vector3.new(-333.8, 82.4, 96.5)) end)
addButton("📍 Power", function() teleportTo(Vector3.new(-269.1, 82.4, 111.2)) end)
addButton("📍 Salão Principal", function() teleportTo(Vector3.new(-294.1, 82.4, 8.4)) end)
addButton("📍 Outside", function() teleportTo(Vector3.new(-157.5, 82.4, 61.4)) end)
addButton("📍 Câmeras", function() teleportTo(Vector3.new(-245.9, 82.4, -15.7)) end)

-- Toggles
addToggle("⚡ Infinite Stamina", setInfiniteStamina)
addToggle("👁️ ESP Players", setESP)
addToggle("💡 Fullbright", setFullbright)

-- Ações
addButton("💀 ASSUSTAR MUTANT (Manual)", scareMutant)

-- Auto reparos
addButton("🔧 Reparar Gerador", autoRepairGenerator)
addButton("🔧 Reparar Fusíveis", autoRepairFuseBox)
addButton("🔧 Reparar Tudo", autoRepairAll)

-- Ajustar scroll
local function updateCanvas()
    local total = 0
    for _, child in pairs(scroll:GetChildren()) do
        if child:IsA("TextButton") or child:IsA("Frame") then
            total = total + 42
        end
    end
    scroll.CanvasSize = UDim2.new(0, 0, 0, total + 30)
end
task.wait(0.1)
updateCanvas()

-- Abrir/fechar menu
local menuOpen = false
toggleBtn.MouseButton1Click:Connect(function()
    menuOpen = not menuOpen
    menu.Visible = menuOpen
    if menuOpen then updateCanvas() end
end)

print("✅ DAVIROBLOX HUB FINAL carregado! Animação + Auto reparos OK.")
