--[[
    DAVIROBLOX HUB | Night 2
    VERSÃO SEM BOTÃO MANUAL DE ASSUSTAR MUTANT
]]

local player = game.Players.LocalPlayer
local coreGui = game:GetService("CoreGui")
local tts = game:GetService("TextToSpeech")

-- ========== CONFIGURAÇÃO DAS JANELAS ==========
local janelas = {
    { nome = "Janela1", posJanela = Vector3.new(-292.1, 82.4, -38.8), posBotao = Vector3.new(-288.5, 82.4, -39.6) },
    { nome = "Janela2", posJanela = Vector3.new(-321.9, 82.4, -37.4), posBotao = Vector3.new(-318.1, 82.4, -40.1) },
    { nome = "Janela3", posJanela = Vector3.new(-311.1, 82.4, 38.7), posBotao = Vector3.new(-313.0, 82.6, 35.4) },
    { nome = "Janela4", posJanela = Vector3.new(-282.0, 82.4, -111.9), posBotao = Vector3.new(-278.4, 82.4, -113.3) },
    { nome = "Janela5", posJanela = Vector3.new(-309.6, 82.4, -111.9), posBotao = Vector3.new(-307.9, 82.4, -113.4) }
}
local DISTANCIA_LIMITE = 15
local COOLDOWN_AUTO_SCARE = 8

-- ========== CONFIGURAÇÃO (salvar toggles) ==========
local configFileName = "DaviHubConfig.json"
local config = {
    autoScare = false,
    autoReparo = false,
    infiniteStamina = false,
    espPlayers = false,
    fullbright = false,
}

local function loadConfig()
    local success, data = pcall(readfile, configFileName)
    if success and data then
        local ok, decoded = pcall(game:GetService("HttpService").JSONDecode, game:GetService("HttpService"), data)
        if ok then for k, v in pairs(decoded) do config[k] = v end end
    end
end

local function saveConfig()
    local encoded = game:GetService("HttpService"):JSONEncode(config)
    pcall(writefile, configFileName, encoded)
end

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
    title.TextColor3 = Color3.fromRGB(255, 140, 0)
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
    barFill.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
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

mostrarAnimacao()
task.wait(1.8)
loadConfig()

-- ========== FUNÇÃO TELEPORTE ==========
local function teleportTo(pos)
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(pos)
    end
end

-- ========== AUTO SCARE ==========
local autoScareActive = false
local autoScareTask = nil
local lastScareTime = 0

local function ativarLuzNaJanela(janela)
    teleportTo(janela.posBotao)
    task.wait(0.2)
    local hrp = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("ClickDetector") and (obj.Parent.Position - janela.posBotao).Magnitude < 5 then
                fireclickdetector(obj)
                return true
            end
        end
    end
    return false
end

local function autoScareLoop()
    while autoScareActive do
        task.wait(1)
        if not autoScareActive then break end
        local mutant = nil
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj.Name == "Mutant" or obj.Name == "Larry" then
                mutant = obj
                break
            end
        end
        if not mutant then goto continue end
        local mutantPos = mutant:FindFirstChild("HumanoidRootPart") and mutant.HumanoidRootPart.Position or mutant.Position
        local janelaAlvo = nil
        local menorDist = DISTANCIA_LIMITE + 1
        for _, jan in ipairs(janelas) do
            local dist = (mutantPos - jan.posJanela).Magnitude
            if dist < menorDist then
                menorDist = dist
                janelaAlvo = jan
            end
        end
        if janelaAlvo and menorDist <= DISTANCIA_LIMITE and (tick() - lastScareTime) >= COOLDOWN_AUTO_SCARE then
            lastScareTime = tick()
            print("🔁 Auto Scare: Mutante na " .. janelaAlvo.nome)
            ativarLuzNaJanela(janelaAlvo)
            local flash = Instance.new("Frame")
            flash.Size = UDim2.new(1,0,1,0)
            flash.BackgroundColor3 = Color3.fromRGB(255,255,255)
            flash.BackgroundTransparency = 0.5
            flash.Parent = player.PlayerGui
            task.wait(0.2)
            flash:Destroy()
        end
        ::continue::
    end
end

local function setAutoScare(state)
    autoScareActive = state
    config.autoScare = state
    saveConfig()
    if autoScareTask then coroutine.close(autoScareTask); autoScareTask = nil end
    if state then
        autoScareTask = coroutine.create(autoScareLoop)
        coroutine.resume(autoScareTask)
    end
end

-- ========== AUTO REPARO ==========
local autoReparoActive = false
local autoReparoTask = nil

local function repararGerador()
    local char = player.Character
    if not char then return end
    local generator = nil
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name:lower():find("generator") or obj.Name:lower():find("gerador") then
            generator = obj
            break
        end
    end
    if generator then
        local pos = generator:FindFirstChild("Position") and generator.Position.Value or (generator:FindFirstChildWhichIsA("BasePart") and generator:FindFirstChildWhichIsA("BasePart").Position)
        if pos then
            teleportTo(pos + Vector3.new(0,3,0))
            task.wait(0.5)
            for _, det in pairs(generator:GetDescendants()) do
                if det:IsA("ClickDetector") then fireclickdetector(det); break end
            end
        end
    end
end

local function repararFusivel()
    local char = player.Character
    if not char then return end
    local fuseBox = nil
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj.Name:lower():find("fuse") or obj.Name:lower():find("fusivel") or obj.Name:lower():find("caixa") then
            fuseBox = obj
            break
        end
    end
    if fuseBox then
        local pos = fuseBox:FindFirstChild("Position") and fuseBox.Position.Value or (fuseBox:FindFirstChildWhichIsA("BasePart") and fuseBox:FindFirstChildWhichIsA("BasePart").Position)
        if pos then
            teleportTo(pos + Vector3.new(0,2,0))
            task.wait(0.5)
            for _, det in pairs(fuseBox:GetDescendants()) do
                if det:IsA("ClickDetector") then fireclickdetector(det); break end
            end
        end
    end
end

local function autoReparoLoop()
    while autoReparoActive do
        task.wait(5)
        if not autoReparoActive then break end
        repararGerador()
        task.wait(1)
        repararFusivel()
    end
end

local function setAutoReparo(state)
    autoReparoActive = state
    config.autoReparo = state
    saveConfig()
    if autoReparoTask then coroutine.close(autoReparoTask); autoReparoTask = nil end
    if state then
        autoReparoTask = coroutine.create(autoReparoLoop)
        coroutine.resume(autoReparoTask)
    end
end

-- ========== INFINITE STAMINA ==========
local staminaLoop = nil
local function setInfiniteStamina(state)
    config.infiniteStamina = state
    saveConfig()
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
    end
end

-- ========== ESP PLAYERS ==========
local espHighlights = {}
local function clearESP()
    for _, h in pairs(espHighlights) do if h and h.Parent then h:Destroy() end end
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
    config.espPlayers = state
    saveConfig()
    if state then
        clearESP()
        for _, p in pairs(game.Players:GetPlayers()) do addESPToPlayer(p) end
        game.Players.PlayerAdded:Connect(addESPToPlayer)
    else
        clearESP()
    end
end

-- ========== FULLBRIGHT ==========
local originalLight = {}
local function setFullbright(state)
    config.fullbright = state
    saveConfig()
    local lighting = game:GetService("Lighting")
    if state then
        originalLight = {Ambient=lighting.Ambient, OutdoorAmbient=lighting.OutdoorAmbient, Brightness=lighting.Brightness, GlobalShadows=lighting.GlobalShadows, FogEnd=lighting.FogEnd}
        lighting.Ambient = Color3.fromRGB(255,255,255)
        lighting.OutdoorAmbient = Color3.fromRGB(255,255,255)
        lighting.Brightness = 2
        lighting.GlobalShadows = false
        lighting.FogEnd = 100000
    else
        lighting.Ambient = originalLight.Ambient or Color3.fromRGB(128,128,128)
        lighting.OutdoorAmbient = originalLight.OutdoorAmbient or Color3.fromRGB(128,128,128)
        lighting.Brightness = originalLight.Brightness or 1
        lighting.GlobalShadows = originalLight.GlobalShadows or true
        lighting.FogEnd = originalLight.FogEnd or 1000
    end
end

-- ========== ESP NPC (automático) ==========
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

-- ========== GUI PRINCIPAL (ARRÁSTAVEL, ABAS) ==========
local gui = Instance.new("ScreenGui")
gui.Name = "DaviHubModerno"
gui.ResetOnSpawn = false
gui.Parent = coreGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 380, 0, 560)
mainFrame.Position = UDim2.new(0.5, -190, 0.3, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = gui

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 40)
titleBar.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, -60, 1, 0)
titleLabel.Position = UDim2.new(0, 10, 0, 0)
titleLabel.Text = "DAVI HUB"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.BackgroundTransparency = 1
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextSize = 18
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = titleBar

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -35, 0, 5)
closeBtn.Text = "✕"
closeBtn.TextSize = 18
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
closeBtn.BorderSizePixel = 0
closeBtn.Parent = titleBar
closeBtn.MouseButton1Click:Connect(function()
    gui.Visible = false
end)

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 30, 0, 30)
minBtn.Position = UDim2.new(1, -70, 0, 5)
minBtn.Text = "−"
minBtn.TextSize = 20
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
minBtn.BorderSizePixel = 0
minBtn.Parent = titleBar
minBtn.MouseButton1Click:Connect(function()
    gui.Visible = false
    if not _G.reopenBtn then
        local reopen = Instance.new("TextButton")
        reopen.Size = UDim2.new(0, 100, 0, 40)
        reopen.Position = UDim2.new(0.02, 0, 0.5, -20)
        reopen.Text = "DAVI HUB"
        reopen.TextSize = 14
        reopen.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
        reopen.TextColor3 = Color3.fromRGB(255, 255, 255)
        reopen.BorderSizePixel = 0
        reopen.Parent = coreGui
        reopen.MouseButton1Click:Connect(function()
            gui.Visible = true
            reopen:Destroy()
            _G.reopenBtn = nil
        end)
        _G.reopenBtn = reopen
    end
end)

titleBar.MouseButton1Click:Connect(function()
    tts:Say("Bem vindo!", "pt-BR")
end)

local dragging = false
local dragStart, startPos
titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)
titleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)
game:GetService("UserInputService").InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)

local tabBar = Instance.new("Frame")
tabBar.Size = UDim2.new(1, 0, 0, 40)
tabBar.Position = UDim2.new(0, 0, 0, 40)
tabBar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
tabBar.BorderSizePixel = 0
tabBar.Parent = mainFrame

local contentContainer = Instance.new("Frame")
contentContainer.Size = UDim2.new(1, 0, 1, -80)
contentContainer.Position = UDim2.new(0, 0, 0, 80)
contentContainer.BackgroundTransparency = 1
contentContainer.Parent = mainFrame

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, 0, 1, 0)
scroll.BackgroundTransparency = 1
scroll.ScrollBarThickness = 6
scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
scroll.Parent = contentContainer

local layoutScroll = Instance.new("UIListLayout")
layoutScroll.Padding = UDim.new(0, 8)
layoutScroll.SortOrder = Enum.SortOrder.LayoutOrder
layoutScroll.Parent = scroll

local function limparAba()
    for _, child in pairs(scroll:GetChildren()) do
        if child:IsA("TextButton") or child:IsA("Frame") then child:Destroy() end
    end
    layoutScroll:Destroy()
    local newLayout = Instance.new("UIListLayout")
    newLayout.Padding = UDim.new(0, 8)
    newLayout.SortOrder = Enum.SortOrder.LayoutOrder
    newLayout.Parent = scroll
end

local function atualizarScroll()
    task.wait(0.1)
    local total = 0
    for _, child in pairs(scroll:GetChildren()) do
        if child:IsA("TextButton") or child:IsA("Frame") then total = total + 48 end
    end
    scroll.CanvasSize = UDim2.new(0, 0, 0, total + 30)
end

local function criarBotao(texto, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 40)
    btn.Text = texto
    btn.TextSize = 14
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.Parent = scroll
    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    end)
    btn.MouseButton1Click:Connect(callback)
    return btn
end

local function criarToggle(texto, configKey, acaoOnChange)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 40)
    frame.BackgroundTransparency = 1
    frame.Parent = scroll
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.65, 0, 1, 0)
    label.Text = texto
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextColor3 = Color3.fromRGB(220,220,220)
    label.BackgroundTransparency = 1
    label.TextSize = 14
    label.Parent = frame
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.25, 0, 0.8, 0)
    btn.Position = UDim2.new(0.72, 0, 0.1, 0)
    btn.Text = config[configKey] and "On" or "Off"
    btn.TextSize = 13
    btn.BackgroundColor3 = config[configKey] and Color3.fromRGB(255,140,0) or Color3.fromRGB(80,80,100)
    btn.TextColor3 = config[configKey] and Color3.fromRGB(255,255,255) or Color3.fromRGB(255,100,100)
    btn.BorderSizePixel = 0
    btn.Parent = frame
    btn.MouseButton1Click:Connect(function()
        local novoEstado = not config[configKey]
        config[configKey] = novoEstado
        acaoOnChange(novoEstado)
        btn.Text = novoEstado and "On" or "Off"
        btn.BackgroundColor3 = novoEstado and Color3.fromRGB(255,140,0) or Color3.fromRGB(80,80,100)
        btn.TextColor3 = novoEstado and Color3.fromRGB(255,255,255) or Color3.fromRGB(255,100,100)
        saveConfig()
    end)
    return frame
end

-- ========== ABAS ==========
local function abaTeleports()
    limparAba()
    criarBotao("📍 Rádio 1", function() teleportTo(Vector3.new(-306.4, 82.4, -17.3)) end)
    criarBotao("📍 Rádio 2", function() teleportTo(Vector3.new(-246.5, 82.4, -16.5)) end)
    criarBotao("📍 Delivery Screen", function() teleportTo(Vector3.new(-283.3, 82.4, 13.0)) end)
    criarBotao("📍 Torre de Rádio", function() teleportTo(Vector3.new(-418.3, 82.4, -16.2)) end)
    criarBotao("📍 Boiler", function() teleportTo(Vector3.new(-291.7, 82.4, 102.9)) end)
    criarBotao("📍 Power Storage", function() teleportTo(Vector3.new(-333.8, 82.4, 96.5)) end)
    criarBotao("📍 Power", function() teleportTo(Vector3.new(-
