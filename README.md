local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local RS = game.ReplicatedStorage
local HttpService = game:GetService("HttpService")
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")

-- ============================================================
-- SISTEMA DE FEEDBACK (COM WEBHOOK)
-- ============================================================

local WEBHOOK_URL = "https://discord.com/api/webhooks/1524546983799427194/WyTosfrV6Opc1MPOpmTJmYNlCzBu0gpRSJ89dUnqcNVYbqJ373-tCfTLUMBgOTidUEh3"

local function enviarFeedback(categoria, mensagem)
    if not mensagem or mensagem == "" then
        showNotification("❌ Digite uma mensagem!", Color3.fromRGB(200, 0, 0))
        return
    end

    local data = {
        content = string.format(
            "**📝 NOVO FEEDBACK DO DAVI HUB**\n" ..
            "**👤 Usuário:** %s (%s)\n" ..
            "**📂 Categoria:** %s\n" ..
            "**💬 Mensagem:** %s\n" ..
            "**🕐 Data:** %s",
            player.Name,
            player.UserId,
            categoria or "Sem categoria",
            mensagem,
            os.date("%d/%m/%Y %H:%M:%S")
        )
    }

    local jsonData = HttpService:JSONEncode(data)
    local headers = {
        ["Content-Type"] = "application/json"
    }

    pcall(function()
        local response = request({
            Url = WEBHOOK_URL,
            Method = "POST",
            Headers = headers,
            Body = jsonData
        })
        if response and response.StatusCode == 204 then
            showNotification("✅ Feedback enviado com sucesso!", Color3.fromRGB(0, 200, 0))
        else
            showNotification("❌ Erro ao enviar feedback!", Color3.fromRGB(200, 0, 0))
        end
    end)
end

-- ============================================================
-- SISTEMA DE IDIOMAS
-- ============================================================

local IDIOMAS = {
    pt = {
        nome = "Português",
        main = "PRINCIPAL",
        menu = "MENU",
        tele = "TELEPORTES",
        n1 = "NOITE 1",
        n2 = "NOITE 2",
        n3 = "NOITE 3",
        esp = "ESP",
        geral = "GERAL",
        config = "CONFIGURAÇÕES",
        fullbright = "Fullbright",
        stamina = "Stamina Infinita",
        antifrost = "Anti-Frosted",
        o2 = "O2 Infinito",
        noclip = "Noclip",
        sprint = "Velocidade da Sprint",
        feedback = "Enviar Feedback",
        usuarios = "Ver Usuários do HUB",
        autoscare = "Auto Scare",
        antivent = "Anti Vent",
        revive = "Reviver",
        escapesnatch = "Escape Snatch",
        refillpower = "Recarregar Energia",
        antistalker = "Anti Stalker",
        aimbot = "Ativar Aimbot",
        aimspeed = "Velocidade do Aimbot",
        aimdist = "Distância do Aimbot",
        municao = "Auto Coletar Munição",
        esp_players = "ESP Players",
        esp_larry = "ESP Larry",
        esp_stalker = "ESP Stalker",
        esp_zombie = "ESP Zumbis & Esqueletos",
        bypass = "Bypass Anti-Cheat",
        static = "Desabilitar Estática",
        notifier = "Notificador",
        salvar = "Salvar Configurações",
        carregar = "Carregar Configurações",
        autoload = "Auto Load",
        idioma = "Idioma",
        config_salvas = "As configurações são salvas em:",
        arquivo_config = "DAVI_HUB_Config.json",
        bemvindo = "Bem-vindo ao DAVI HUB",
        selecione = "Selecione uma aba ao lado",
        versao = "Versão 2.0",
        idioma_alterado = "Idioma alterado para: ",
    },
    en = {
        nome = "English",
        main = "MAIN",
        menu = "MENU",
        tele = "TELEPORTS",
        n1 = "NIGHT 1",
        n2 = "NIGHT 2",
        n3 = "NIGHT 3",
        esp = "ESP",
        geral = "GENERAL",
        config = "SETTINGS",
        fullbright = "Fullbright",
        stamina = "Infinite Stamina",
        antifrost = "Anti-Frosted",
        o2 = "Infinite O2",
        noclip = "Noclip",
        sprint = "Sprint Speed",
        feedback = "Send Feedback",
        usuarios = "View HUB Users",
        autoscare = "Auto Scare",
        antivent = "Anti Vent",
        revive = "Revive",
        escapesnatch = "Escape Snatch",
        refillpower = "Refill Power",
        antistalker = "Anti Stalker",
        aimbot = "Enable Aimbot",
        aimspeed = "Aimbot Speed",
        aimdist = "Aimbot Distance",
        municao = "Auto Collect Ammo",
        esp_players = "ESP Players",
        esp_larry = "ESP Larry",
        esp_stalker = "ESP Stalker",
        esp_zombie = "ESP Zombies & Skeletons",
        bypass = "Bypass Anti-Cheat",
        static = "Disable Static",
        notifier = "Notifier",
        salvar = "Save Settings",
        carregar = "Load Settings",
        autoload = "Auto Load",
        idioma = "Language",
        config_salvas = "Settings are saved at:",
        arquivo_config = "DAVI_HUB_Config.json",
        bemvindo = "Welcome to DAVI HUB",
        selecione = "Select a tab on the side",
        versao = "Version 2.0",
        idioma_alterado = "Language changed to: ",
    }
}

local idiomaAtual = "pt"
local listaIdiomas = {
    {nome = "Português (BR)", codigo = "pt"},
    {nome = "English", codigo = "en"},
}

local function t(chave)
    if IDIOMAS[idiomaAtual] and IDIOMAS[idiomaAtual][chave] then
        return IDIOMAS[idiomaAtual][chave]
    end
    return chave
end

-- ============================================================
-- SISTEMA DE CONFIGURAÇÕES
-- ============================================================

local configs = {
    idioma = "pt",
    noclip = false,
    fullbright = false,
    stamina = false,
    antifrost = false,
    o2 = false,
    aimbot = false,
    autoscare = false,
    esp_players = false,
    esp_larry = false,
    esp_stalker = false,
    esp_zombie = false,
    notifier = false,
    sprint_speed = 17,
    aim_speed = 80,
    aim_distance = 80,
    autoload = true,
    antivent = false,
    revive = false,
    escapesnatch = false,
    refillpower = false,
    antistalker = false,
    municao = false,
    bypass = false,
    static = false,
}

local function salvarConfiguracoes()
    local dados = {
        idioma = idiomaAtual,
        noclip = configs.noclip,
        fullbright = configs.fullbright,
        stamina = configs.stamina,
        antifrost = configs.antifrost,
        o2 = configs.o2,
        aimbot = configs.aimbot,
        autoscare = configs.autoscare,
        esp_players = configs.esp_players,
        esp_larry = configs.esp_larry,
        esp_stalker = configs.esp_stalker,
        esp_zombie = configs.esp_zombie,
        notifier = configs.notifier,
        sprint_speed = configs.sprint_speed,
        aim_speed = configs.aim_speed,
        aim_distance = configs.aim_distance,
        autoload = configs.autoload,
        antivent = configs.antivent,
        revive = configs.revive,
        escapesnatch = configs.escapesnatch,
        refillpower = configs.refillpower,
        antistalker = configs.antistalker,
        municao = configs.municao,
        bypass = configs.bypass,
        static = configs.static,
    }
    local json = HttpService:JSONEncode(dados)
    pcall(function()
        writefile("DAVI_HUB_Config.json", json)
        print("💾 Configurações salvas!")
    end)
end

local function carregarConfiguracoes()
    local sucesso, dados = pcall(function()
        return readfile("DAVI_HUB_Config.json")
    end)
    if sucesso and dados then
        local config = HttpService:JSONDecode(dados)
        idiomaAtual = config.idioma or "pt"
        configs.noclip = config.noclip or false
        configs.fullbright = config.fullbright or false
        configs.stamina = config.stamina or false
        configs.antifrost = config.antifrost or false
        configs.o2 = config.o2 or false
        configs.aimbot = config.aimbot or false
        configs.autoscare = config.autoscare or false
        configs.esp_players = config.esp_players or false
        configs.esp_larry = config.esp_larry or false
        configs.esp_stalker = config.esp_stalker or false
        configs.esp_zombie = config.esp_zombie or false
        configs.notifier = config.notifier or false
        configs.sprint_speed = config.sprint_speed or 17
        configs.aim_speed = config.aim_speed or 80
        configs.aim_distance = config.aim_distance or 80
        configs.autoload = (config.autoload == nil and true) or config.autoload
        configs.antivent = config.antivent or false
        configs.revive = config.revive or false
        configs.escapesnatch = config.escapesnatch or false
        configs.refillpower = config.refillpower or false
        configs.antistalker = config.antistalker or false
        configs.municao = config.municao or false
        configs.bypass = config.bypass or false
        configs.static = config.static or false
        print("📂 Configurações carregadas!")
        return true
    else
        return false
    end
end

if configs.autoload then
    carregarConfiguracoes()
end

-- ============================================================
-- SISTEMA DE IDENTIFICAÇÃO DE USUÁRIOS
-- ============================================================

local function identificarUsuarios()
    local usuarios = {}
    for _, jogador in pairs(game.Players:GetPlayers()) do
        if jogador ~= player then
            pcall(function()
                local playerGui = jogador:FindFirstChild("PlayerGui")
                if playerGui then
                    for _, gui in pairs(playerGui:GetChildren()) do
                        if gui:IsA("ScreenGui") then
                            local nome = gui.Name:lower()
                            if nome:find("davi") or nome:find("hub") or nome:find("davihub") then
                                table.insert(usuarios, {
                                    nome = jogador.Name,
                                    userId = jogador.UserId,
                                    gui = gui.Name
                                })
                                break
                            end
                        end
                    end
                end
            end)
        end
    end
    return usuarios
end

local function mostrarUsuariosConsole()
    local usuarios = identificarUsuarios()
    print("")
    print("===== USUARIOS DO DAVI HUB NO SERVIDOR =====")
    if #usuarios == 0 then
        print("Nenhum outro usuario do DAVI HUB encontrado.")
    else
        print("Total de usuarios: " .. #usuarios)
        for i, usuario in pairs(usuarios) do
            print(i .. ". " .. usuario.nome .. " (ID: " .. usuario.userId .. ")")
        end
    end
    print("================================================")
end

local function criarGUIUsuarios()
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "UsuariosDAVI" then v:Destroy() end
    end

    local usuarios = identificarUsuarios()
    local gui = Instance.new("ScreenGui")
    gui.Name = "UsuariosDAVI"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true

    local fundo = Instance.new("Frame")
    fundo.Size = UDim2.new(1, 0, 1, 0)
    fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    fundo.BackgroundTransparency = 0.5
    fundo.Parent = gui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 350, 0, 300)
    frame.Position = UDim2.new(0.5, -175, 0.5, -150)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    frame.BackgroundTransparency = 0.05
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = fundo

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = frame

    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = frame

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 45)
    title.Text = "USUARIOS DO DAVI HUB"
    title.TextColor3 = Color3.fromRGB(255, 200, 50)
    title.TextSize = 18
    title.Font = Enum.Font.GothamBold
    title.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    title.BackgroundTransparency = 0.15
    title.Parent = frame

    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 16)
    titleCorner.Parent = title

    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 30, 0, 30)
    close.Position = UDim2.new(1, -38, 0, 7)
    close.Text = "X"
    close.TextColor3 = Color3.fromRGB(255, 255, 255)
    close.TextSize = 18
    close.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    close.BackgroundTransparency = 0.3
    close.BorderSizePixel = 0
    close.Parent = title
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = close
    close.MouseButton1Click:Connect(function() gui:Destroy() end)

    local lista = Instance.new("ScrollingFrame")
    lista.Size = UDim2.new(1, -20, 1, -80)
    lista.Position = UDim2.new(0, 10, 0, 55)
    lista.BackgroundTransparency = 1
    lista.BorderSizePixel = 0
    lista.ScrollBarThickness = 6
    lista.ScrollBarImageColor3 = Color3.fromRGB(255, 140, 0)
    lista.Parent = frame

    local listaLayout = Instance.new("UIListLayout")
    listaLayout.Padding = UDim.new(0, 4)
    listaLayout.SortOrder = Enum.SortOrder.LayoutOrder
    listaLayout.Parent = lista

    local contador = Instance.new("TextLabel")
    contador.Size = UDim2.new(1, 0, 0, 25)
    contador.Position = UDim2.new(0, 0, 1, -35)
    contador.Text = "Total: " .. #usuarios .. " usuario(s)"
    contador.TextColor3 = Color3.fromRGB(200, 200, 200)
    contador.TextSize = 14
    contador.Font = Enum.Font.Gotham
    contador.BackgroundTransparency = 1
    contador.Parent = frame

    if #usuarios == 0 then
        local nenhum = Instance.new("TextLabel")
        nenhum.Size = UDim2.new(1, 0, 0, 35)
        nenhum.Text = "Nenhum outro usuario do DAVI HUB encontrado."
        nenhum.TextColor3 = Color3.fromRGB(200, 200, 200)
        nenhum.TextSize = 14
        nenhum.Font = Enum.Font.Gotham
        nenhum.BackgroundTransparency = 1
        nenhum.Parent = lista
    else
        for _, usuario in pairs(usuarios) do
            local item = Instance.new("TextLabel")
            item.Size = UDim2.new(1, 0, 0, 30)
            item.Text = usuario.nome
            item.TextColor3 = Color3.fromRGB(255, 255, 255)
            item.TextSize = 14
            item.Font = Enum.Font.GothamBold
            item.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
            item.BackgroundTransparency = 0.2
            item.BorderSizePixel = 0
            item.Parent = lista
            local itemCorner = Instance.new("UICorner")
            itemCorner.CornerRadius = UDim.new(0, 6)
            itemCorner.Parent = item
        end
    end

    task.wait(0.1)
    lista.CanvasSize = UDim2.new(0, 0, 0, #usuarios * 34 + 50)
end

game:GetService("Players").LocalPlayer.Chatted:Connect(function(msg)
    if msg:lower() == "/davi" or msg:lower() == "/hub" then
        criarGUIUsuarios()
        mostrarUsuariosConsole()
    end
end)
-- ============================================================
-- SISTEMA DE NOTIFICAÇÕES
-- ============================================================

local function showNotification(text, cor)
    cor = cor or Color3.fromRGB(50, 50, 50)
    local gui = Instance.new("ScreenGui")
    gui.Name = "Notificacao"
    gui.Parent = player.PlayerGui
    gui.ResetOnSpawn = false
    gui.IgnoreGuiInset = true
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 350, 0, 45)
    frame.Position = UDim2.new(1, -370, 0.05, 0)
    frame.BackgroundColor3 = cor
    frame.BackgroundTransparency = 0.15
    frame.BorderSizePixel = 0
    frame.Parent = gui
    local frameCorner = Instance.new("UICorner")
    frameCorner.CornerRadius = UDim.new(0, 12)
    frameCorner.Parent = frame
    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 1
    border.Transparency = 0.3
    border.Parent = frame
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -20, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.Text = text
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextSize = 14
    label.Font = Enum.Font.Gotham
    label.BackgroundTransparency = 1
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame
    local fadeOut = TweenService:Create(frame, TweenInfo.new(0.5, Enum.EasingStyle.Linear), {BackgroundTransparency = 1})
    local fadeLabel = TweenService:Create(label, TweenInfo.new(0.5, Enum.EasingStyle.Linear), {TextTransparency = 1})
    task.wait(3)
    fadeOut:Play()
    fadeLabel:Play()
    task.wait(0.5)
    gui:Destroy()
end

-- ============================================================
-- NOTIFICADOR (TOGGLE ON/OFF)
-- ============================================================

local notifierActive = false
local notifierConnections = {}

local function iniciarNotificador()
    if notifierActive then return end
    notifierActive = true
    print("🔔 Notificador ATIVADO!")
    local function onChildAdded(child)
        if not notifierActive then return end
        if child.Name == "Larry" then
            showNotification("Larry apareceu!", Color3.fromRGB(200, 50, 50))
        elseif child.Name == "Mutant" then
            showNotification("Mutant apareceu!", Color3.fromRGB(150, 50, 200))
        elseif child.Name == "Worker" then
            showNotification("Worker apareceu!", Color3.fromRGB(50, 150, 200))
        elseif child.Name == "WorkerHead" then
            showNotification("WorkerHead apareceu!", Color3.fromRGB(50, 200, 150))
        end
    end
    local function onChildRemoved(child)
        if not notifierActive then return end
        if child.Name == "Larry" then
            showNotification("Larry desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "Mutant" then
            showNotification("Mutant desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "Worker" then
            showNotification("Worker desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "WorkerHead" then
            showNotification("WorkerHead desapareceu!", Color3.fromRGB(50, 200, 50))
        end
    end
    local conn1 = workspace.ChildAdded:Connect(onChildAdded)
    local conn2 = workspace.ChildRemoved:Connect(onChildRemoved)
    table.insert(notifierConnections, conn1)
    table.insert(notifierConnections, conn2)
    task.wait(1)
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Model") then
            if obj.Name == "Larry" then
                showNotification("Larry esta aqui!", Color3.fromRGB(200, 50, 50))
            elseif obj.Name == "Mutant" then
                showNotification("Mutant esta aqui!", Color3.fromRGB(150, 50, 200))
            elseif obj.Name == "Worker" then
                showNotification("Worker esta aqui!", Color3.fromRGB(50, 150, 200))
            elseif obj.Name == "WorkerHead" then
                showNotification("WorkerHead esta aqui!", Color3.fromRGB(50, 200, 150))
            end
        end
    end
end

local function desativarNotificador()
    if not notifierActive then return end
    notifierActive = false
    for _, conn in pairs(notifierConnections) do
        pcall(function() conn:Disconnect() end)
    end
    notifierConnections = {}
    print("🔕 Notificador DESATIVADO!")
end

-- ============================================================
-- SISTEMA DE ESP
-- ============================================================

local espObjects = {}

local function createESP(part, color, text)
    local billboard = Instance.new("BillboardGui")
    billboard.Size = UDim2.new(0, 100, 0, 30)
    billboard.StudsOffset = Vector3.new(0, 2, 0)
    billboard.Adornee = part
    billboard.Parent = part

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = text or "ESP"
    label.TextColor3 = color or Color3.fromRGB(255, 0, 0)
    label.TextSize = 14
    label.Font = Enum.Font.GothamBold
    label.TextStrokeTransparency = 0.5
    label.Parent = billboard

    return billboard
end

local function updateESP()
    for _, obj in pairs(espObjects) do
        if obj and obj.Parent then
            obj:Destroy()
        end
    end
    espObjects = {}

    if not configs.esp_players and not configs.esp_larry and 
       not configs.esp_stalker and not configs.esp_zombie then
        return
    end

    if configs.esp_players then
        for _, jogador in ipairs(Players:GetPlayers()) do
            if jogador ~= player and jogador.Character then
                local root = jogador.Character:FindFirstChild("HumanoidRootPart")
                if root then
                    local esp = createESP(root, Color3.fromRGB(0, 255, 0), jogador.Name)
                    table.insert(espObjects, esp)
                end
            end
        end
    end

    local targets = {
        Larry = {name = "Larry", color = Color3.fromRGB(255, 0, 0), enabled = "esp_larry"},
        Stalker = {name = "Stalker", color = Color3.fromRGB(255, 165, 0), enabled = "esp_stalker"},
        Zombie = {name = "Zombie", color = Color3.fromRGB(0, 255, 0), enabled = "esp_zombie"},
        Skeleton = {name = "Skeleton", color = Color3.fromRGB(0, 255, 0), enabled = "esp_zombie"}
    }

    for _, target in pairs(targets) do
        if configs[target.enabled] then
            for _, part in ipairs(Workspace:GetDescendants()) do
                if part:IsA("Model") and part.Name == target.name then
                    local root = part:FindFirstChild("HumanoidRootPart") or part:FindFirstChild("Head")
                    if root then
                        local esp = createESP(root, target.color, target.name)
                        table.insert(espObjects, esp)
                    end
                end
            end
        end
    end
end

RunService.Heartbeat:Connect(updateESP)

-- ============================================================
-- SISTEMA DE AIMBOT
-- ============================================================

local function getClosestTarget()
    local nearest = nil
    local nearestDist = math.huge

    if not player.Character then return nil end
    local root = player.Character:FindFirstChild("HumanoidRootPart")
    if not root then return nil end

    for _, jogador in ipairs(Players:GetPlayers()) do
        if jogador ~= player and jogador.Character then
            local targetRoot = jogador.Character:FindFirstChild("HumanoidRootPart")
            if targetRoot then
                local dist = (targetRoot.Position - root.Position).Magnitude
                if dist < configs.aim_distance and dist < nearestDist then
                    nearest = jogador
                    nearestDist = dist
                end
            end
        end
    end

    return nearest
end

RunService.Heartbeat:Connect(function()
    if not configs.aimbot then return end

    local target = getClosestTarget()
    if target and target.Character then
        local head = target.Character:FindFirstChild("Head")
        if head then
            local targetPos = head.Position
            local cameraPos = Camera.CFrame.Position
            local direction = (targetPos - cameraPos).unit
            local newCFrame = CFrame.new(cameraPos, cameraPos + direction)

            local currentCFrame = Camera.CFrame
            local lerpFactor = configs.aim_speed / 100
            local smoothCFrame = currentCFrame:Lerp(newCFrame, lerpFactor)
            Camera.CFrame = smoothCFrame
        end
    end
end)

-- ============================================================
-- FUNÇÕES DO JOGO
-- ============================================================

local noclipLoop = nil
local function toggleNoclip(v)
    if noclipLoop then noclipLoop:Disconnect(); noclipLoop = nil end
    if v then
        noclipLoop = RunService.Stepped:Connect(function()
            local char = player.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
            end
        end)
    else
        local char = player.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide = true end
            end
        end
    end
end

local origLight = {}
local function toggleFullbright(v)
    local li = Lighting
    if v then
        origLight = {Ambient = li.Ambient, OutdoorAmbient = li.OutdoorAmbient, Brightness = li.Brightness, GlobalShadows = li.GlobalShadows, FogEnd = li.FogEnd}
        li.Ambient = Color3.fromRGB(255,255,255)
        li.OutdoorAmbient = Color3.fromRGB(255,255,255)
        li.Brightness = 2
        li.GlobalShadows = false
        li.FogEnd = 100000
    else
        li.Ambient = origLight.Ambient or Color3.fromRGB(128,128,128)
        li.OutdoorAmbient = origLight.OutdoorAmbient or Color3.fromRGB(128,128,128)
        li.Brightness = origLight.Brightness or 1
        li.GlobalShadows = origLight.GlobalShadows or true
        li.FogEnd = origLight.FogEnd or 1000
    end
end

local staminaLoop = nil
local function toggleStamina(v)
    if staminaLoop then staminaLoop:Disconnect(); staminaLoop = nil end
    if v then
        staminaLoop = RunService.RenderStepped:Connect(function()
            local ch = player.Character
            if ch and ch:FindFirstChild("Sprint") then
                local sprint = ch.Sprint
                if sprint:FindFirstChild("Overdrive") then sprint.Overdrive.Value = 1e9 end
                if sprint:FindFirstChild("Stamina") then sprint.Stamina.Value = 100 end
            end
        end)
    end
end

local function toggleAntiTemp(v)
    local ch = player.Character
    if ch then
        local temp = ch:FindFirstChild("Temperature")
        if temp then temp.Enabled = not v end
    end
end

local o2Loop = nil
local function toggleO2(v)
    if o2Loop then o2Loop:Disconnect(); o2Loop = nil end
    if v then
        o2Loop = RunService.RenderStepped:Connect(function()
            local ch = player.Character
            if ch then
                local breath = ch:FindFirstChild("Breath")
                if breath then breath:SetAttribute("Max",999999); breath.Value = 999999 end
                local blur = Lighting:FindFirstChild("Blur")
                if blur then blur.Enabled = false end
            end
        end)
    end
end

-- ============================================================
-- FUNÇÕES DA NOITE 1
-- ============================================================

local function RefillFireplace()
    local char = player.Character or player.CharacterAdded:Wait()
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local OC = char.HumanoidRootPart.CFrame
    
    local wp = workspace:FindFirstChild("WoodPile")
    if wp then
        local d = wp:FindFirstChild("Detector")
        if d then d.CanCollide = false end
        char.HumanoidRootPart.CFrame = CFrame.new(-27.149, 8.7, -118.611)
        task.wait(0.25)
        local cl = d and d:FindFirstChild("ClickDetector")
        if cl then fireclickdetector(cl) end
        task.wait(0.25)
        char.HumanoidRootPart.CFrame = CFrame.new(-45.113, 7.849, -60.241)
        task.wait(0.5)
        char.HumanoidRootPart.CFrame = OC
    end
end

local function GrabWood()
    local char = player.Character or player.CharacterAdded:Wait()
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local OC = char.HumanoidRootPart.CFrame
    
    local wp = workspace:FindFirstChild("WoodPile")
    if wp then
        local d = wp:FindFirstChild("Detector")
        if d then d.CanCollide = false end
        char.HumanoidRootPart.CFrame = CFrame.new(-27.149, 8.7, -118.611)
        task.wait(0.25)
        local cl = d and d:FindFirstChild("ClickDetector")
        if cl then fireclickdetector(cl) end
        task.wait(0.25)
        char.HumanoidRootPart.CFrame = OC
    end
end

local function RefillGenerator()
    local char = player.Character or player.CharacterAdded:Wait()
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local OC = char.HumanoidRootPart.CFrame
    
    char.HumanoidRootPart.CFrame = CFrame.new(-76.039, 4.675, -133.78)
    task.wait(0.25)
    
    local shack = workspace:FindFirstChild("Shack")
    if shack then
        local jc = shack:FindFirstChild("JerryCan")
        if jc then
            local cl = jc:FindFirstChild("ClickDetector")
            if cl then fireclickdetector(cl) end
        end
        task.wait(0.5)
        local gen = shack:FindFirstChild("Generator")
        if gen then
            local cl = gen:FindFirstChild("ClickDetector")
            if cl then fireclickdetector(cl) end
        end
    end
    task.wait(0.5)
    char.HumanoidRootPart.CFrame = OC
end

local function GrabJerryCan()
    local char = player.Character or player.CharacterAdded:Wait()
    if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local OC = char.HumanoidRootPart.CFrame
    
    char.HumanoidRootPart.CFrame = CFrame.new(-76.039, 4.675, -133.78)
    task.wait(0.25)
    
    local shack = workspace:FindFirstChild("Shack")
    if shack then
        local jc = shack:FindFirstChild("JerryCan")
        if jc then
            local cl = jc:FindFirstChild("ClickDetector")
            if cl then fireclickdetector(cl) end
        end
    end
    task.wait(0.25)
    char.HumanoidRootPart.CFrame = OC
end

-- ============================================================
-- TELEPORTS
-- ============================================================

local teleportsN1 = {
    ["Ladder"] = CFrame.new(-0.173,9.3,-81.32),
    ["Generator"] = CFrame.new(-79.722,4.675,-131.918),
    ["Entrance"] = CFrame.new(-11.036,7.73,-31.822),
    ["LivingRoom"] = CFrame.new(-34.962,8.05,-47.153),
    ["Bedroom"] = CFrame.new(-32.645,23.8,-72.845),
}

local teleportsN2 = {
    ["SafeSpot N2"] = CFrame.new(-339.321,82.4,-40.622),
    ["DeliveryBoard"] = CFrame.new(-282.224,82.4,14.674),
    ["Main"] = CFrame.new(-304.235,82.4,-6.777),
    ["Corridor1"] = CFrame.new(-303.846,82.4,50.169),
    ["Entrance2"] = CFrame.new(-217.417,82.4,65.412),
    ["Corridor2"] = CFrame.new(-293.11,82.4,-89.501),
}

local teleportsN3 = {
    ["Cabana 1"] = CFrame.new(99.8,4.5,-247.2),
    ["Cabana 2"] = CFrame.new(-36.9,4.5,68.7),
    ["Cabana 3"] = CFrame.new(-31.7,4.5,268.8),
    ["Cabana 4"] = CFrame.new(233.6,4.5,245.8),
    ["Cutscene Room"] = CFrame.new(-237,-22.5,107),
    ["Safe Spot N3"] = CFrame.new(194,38.7,-217.4),
    ["Lodge"] = CFrame.new(-226.8,17.4,103.7),
    ["Jeffry Canna"] = CFrame.new(177.5,4.3,197.9),
}

local function teleportar(cframe)
    local char = player.Character or player.CharacterAdded:Wait()
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = cframe
    end
end
-- ============================================================
-- LOOP PRINCIPAL PARA TODAS AS FUNÇÕES
-- ============================================================

RunService.Heartbeat:Connect(function()
    local char = player.Character
    if not char then return end
    local humanoid = char:FindFirstChild("Humanoid")
    if not humanoid then return end

    if configs.autoscare then
        local larry = workspace:FindFirstChild("Larry")
        if larry and larry:FindFirstChild("Humanoid") then
            local larryHumanoid = larry.Humanoid
            if larryHumanoid.Health > 0 then
                local scareEvent = RS:FindFirstChild("ScareEvent")
                if scareEvent then
                    pcall(function() scareEvent:FireServer() end)
                end
            end
        end
    end

    if configs.antivent then
        local wind = char:FindFirstChild("Wind")
        if wind then wind:Destroy() end
    end

    if configs.revive then
        if humanoid.Health <= 0 then
            local reviveEvent = RS:FindFirstChild("Revive")
            if reviveEvent then
                pcall(function() reviveEvent:FireServer() end)
            end
        end
    end

    if configs.escapesnatch then
        local snatch = char:FindFirstChild("Snatch")
        if snatch then snatch:Destroy() end
    end

    if configs.refillpower then
        local power = char:FindFirstChild("Power")
        if power then
            pcall(function() power.Value = 100 end)
        end
    end

    if configs.antistalker then
        for _, stalker in pairs(workspace:GetDescendants()) do
            if stalker:IsA("Model") and stalker.Name == "Stalker" then
                local stalkerPart = stalker:FindFirstChild("HumanoidRootPart")
                if stalkerPart then
                    stalkerPart:Destroy()
                end
            end
        end
    end

    if configs.municao then
        for _, ammo in pairs(workspace:GetDescendants()) do
            if ammo:IsA("Part") and (ammo.Name:lower():find("ammo") or ammo.Name:lower():find("municao")) then
                if (ammo.Position - char.HumanoidRootPart.Position).Magnitude < 10 then
                    local clickDetector = ammo:FindFirstChild("ClickDetector")
                    if clickDetector then
                        pcall(function() clickDetector:FireClick(player) end)
                    end
                end
            end
        end
    end

    if configs.bypass then
        for _, check in pairs(workspace:GetDescendants()) do
            if check:IsA("Script") and (check.Name:lower():find("anticheat") or check.Name:lower():find("anti-cheat") or check.Name:lower():find("cheat")) then
                check.Disabled = true
            end
        end
    end

    if configs.static then
        local staticEffect = Lighting:FindFirstChild("Static")
        if staticEffect then
            staticEffect.Enabled = false
        end
        local blurEffect = Lighting:FindFirstChild("Blur")
        if blurEffect then
            blurEffect.Enabled = false
        end
    end
end)

-- ============================================================
-- SPRINT SPEED
-- ============================================================

RunService.RenderStepped:Connect(function()
    local char = player.Character
    if not char then return end
    local humanoid = char:FindFirstChild("Humanoid")
    if not humanoid then return end

    local sprintSpeed = configs.sprint_speed or 17
    humanoid.WalkSpeed = sprintSpeed
end)

-- ============================================================
-- CRIAÇÃO DA GUI PRINCIPAL
-- ============================================================

local gui = Instance.new("ScreenGui")
gui.Name = "DaviHub"
gui.Parent = player:WaitForChild("PlayerGui")
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 480, 0, 500)
mainFrame.Position = UDim2.new(0.5, -240, 0.15, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 16)
corner.Parent = mainFrame

local border = Instance.new("UIStroke")
border.Color = Color3.fromRGB(255, 140, 0)
border.Thickness = 2
border.Transparency = 0.4
border.Parent = mainFrame

-- CABEÇALHO
local header = Instance.new("Frame")
header.Size = UDim2.new(1, 0, 0, 45)
header.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
header.BackgroundTransparency = 0.25
header.BorderSizePixel = 0
header.Parent = mainFrame

local headerCorner = Instance.new("UICorner")
headerCorner.CornerRadius = UDim.new(0, 16)
headerCorner.Parent = header

local titulo = Instance.new("TextLabel")
titulo.Size = UDim2.new(1, -80, 1, 0)
titulo.Position = UDim2.new(0, 15, 0, 0)
titulo.Text = "DAVI HUB"
titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
titulo.TextSize = 18
titulo.Font = Enum.Font.GothamBold
titulo.BackgroundTransparency = 1
titulo.TextXAlignment = Enum.TextXAlignment.Left
titulo.Parent = header

local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 32, 0, 32)
minBtn.Position = UDim2.new(1, -70, 0, 6.5)
minBtn.Text = "-"
minBtn.TextSize = 22
minBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
minBtn.BackgroundTransparency = 0.5
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.Font = Enum.Font.GothamBold
minBtn.BorderSizePixel = 0
minBtn.Parent = header

local minCorner = Instance.new("UICorner")
minCorner.CornerRadius = UDim.new(0, 8)
minCorner.Parent = minBtn

local minimized = false
minBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    mainFrame.Size = minimized and UDim2.new(0, 480, 0, 45) or UDim2.new(0, 480, 0, 500)
    minBtn.Text = minimized and "+" or "-"
    for _, child in pairs(mainFrame:GetChildren()) do
        if child ~= header then
            child.Visible = not minimized
        end
    end
end)

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 32, 0, 32)
closeBtn.Position = UDim2.new(1, -38, 0, 6.5)
closeBtn.Text = "X"
closeBtn.TextSize = 18
closeBtn.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
closeBtn.BackgroundTransparency = 0.4
closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
closeBtn.Font = Enum.Font.GothamBold
closeBtn.BorderSizePixel = 0
closeBtn.Parent = header

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 8)
closeCorner.Parent = closeBtn

closeBtn.MouseButton1Click:Connect(function()
    gui:Destroy()
end)

-- SISTEMA DE ARRASTE DO CABEÇALHO
local dragging = false
local dragInput, dragStart, startPos

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

header.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input == dragInput then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ============================================================
-- CONTEÚDO E ABAS
-- ============================================================

local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, 0, 1, -45)
contentFrame.Position = UDim2.new(0, 0, 0, 45)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

local sidebar = Instance.new("Frame")
sidebar.Size = UDim2.new(0, 85, 1, 0)
sidebar.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
sidebar.BackgroundTransparency = 0.3
sidebar.BorderSizePixel = 0
sidebar.Parent = contentFrame

local sidebarCorner = Instance.new("UICorner")
sidebarCorner.CornerRadius = UDim.new(0, 12)
sidebarCorner.Parent = sidebar

local sidebarLayout = Instance.new("UIListLayout")
sidebarLayout.Padding = UDim.new(0, 6)
sidebarLayout.SortOrder = Enum.SortOrder.LayoutOrder
sidebarLayout.Parent = sidebar

local abaButtons = {}
local abaFrames = {}
local abas = {
    {name = "🏠", label = "Main"},
    {name = "📋", label = "Menu"},
    {name = "📡", label = "Tele"},
    {name = "🌙", label = "N1"},
    {name = "🌙", label = "N2"},
    {name = "🌙", label = "N3"},
    {name = "👁️", label = "ESP"},
    {name = "⚙️", label = "Geral"},
    {name = "⚙️", label = "Config"},
}

local function criarAba(nome, icone)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.85, 0, 0, 45)
    btn.Position = UDim2.new(0.075, 0, 0, 0)
    btn.Text = nome
    btn.TextColor3 = Color3.fromRGB(200, 200, 200)
    btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    btn.BackgroundTransparency = 0.4
    btn.BorderSizePixel = 0
    btn.Parent = sidebar

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 10)
    btnCorner.Parent = btn

    local frame = Instance.new("ScrollingFrame")
    frame.Size = UDim2.new(1, -90, 1, 0)
    frame.Position = UDim2.new(0, 90, 0, 0)
    frame.BackgroundTransparency = 1
    frame.BorderSizePixel = 0
    frame.ScrollBarThickness = 8
    frame.ScrollBarImageColor3 = Color3.fromRGB(255, 140, 0)
    frame.ScrollBarImageTransparency = 0.3
    frame.VerticalScrollBarPosition = Enum.VerticalScrollBarPosition.Right
    frame.Visible = false
    frame.Parent = contentFrame
    frame.AutomaticCanvasSize = Enum.AutomaticSize.Y

    local flayout = Instance.new("UIListLayout")
    flayout.Padding = UDim.new(0, 6)
    flayout.SortOrder = Enum.SortOrder.LayoutOrder
    flayout.Parent = frame

    flayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
        frame.CanvasSize = UDim2.new(0, 0, 0, flayout.AbsoluteContentSize.Y + 20)
    end)

    btn.MouseButton1Click:Connect(function()
        for _, b in pairs(abaButtons) do
            b.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
            b.BackgroundTransparency = 0.4
            b.TextColor3 = Color3.fromRGB(200, 200, 200)
        end
        btn.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
        btn.BackgroundTransparency = 0.3
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        for _, f in pairs(abaFrames) do
            f.Visible = false
        end
        frame.Visible = true
        task.wait(0.05)
        frame.CanvasSize = UDim2.new(0, 0, 0, flayout.AbsoluteContentSize.Y + 20)
    end)

    table.insert(abaButtons, btn)
    table.insert(abaFrames, frame)
    return frame, btn
end

local abaFramesMap = {}
for i, aba in pairs(abas) do
    local frame, btn = criarAba(aba.label, aba.name)
    abaFramesMap[aba.label] = frame
    if i == 1 then
        btn.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
        btn.BackgroundTransparency = 0.3
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        frame.Visible = true
    end
end

-- ============================================================
-- FUNÇÕES AUXILIARES DA GUI
-- ============================================================

local function addCard(parent)
    local card = Instance.new("Frame")
    card.Size = UDim2.new(0.92, 0, 0, 0)
    card.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    card.BackgroundTransparency = 0.3
    card.BorderSizePixel = 0
    card.AutomaticSize = Enum.AutomaticSize.Y
    card.Parent = parent

    local cardCorner = Instance.new("UICorner")
    cardCorner.CornerRadius = UDim.new(0, 12)
    cardCorner.Parent = card

    local cardLayout = Instance.new("UIListLayout")
    cardLayout.Padding = UDim.new(0, 4)
    cardLayout.SortOrder = Enum.SortOrder.LayoutOrder
    cardLayout.Parent = card

    return card
end

local function addLabel(parent, text, color)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 0, 25)
    l.Text = text
    l.TextColor3 = color or Color3.fromRGB(255, 200, 100)
    l.TextSize = 14
    l.Font = Enum.Font.GothamBold
    l.BackgroundTransparency = 1
    l.Parent = parent
    return l
end

local function addButton(parent, text, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.95, 0, 0, 35)
    b.Text = text
    b.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    b.BackgroundTransparency = 0.2
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.Font = Enum.Font.Gotham
    b.TextSize = 13
    b.BorderSizePixel = 0
    b.AutomaticSize = Enum.AutomaticSize.Y
    b.Parent = parent

    local bCorner = Instance.new("UICorner")
    bCorner.CornerRadius = UDim.new(0, 8)
    bCorner.Parent = b

    b.MouseEnter:Connect(function() b.BackgroundTransparency = 0 end)
    b.MouseLeave:Connect(function() b.BackgroundTransparency = 0.2 end)
    b.MouseButton1Click:Connect(callback)
    return b
end

local function addToggle(parent, text, callback, default)
    local c = Instance.new("Frame")
    c.Size = UDim2.new(0.95, 0, 0, 35)
    c.BackgroundTransparency = 1
    c.AutomaticSize = Enum.AutomaticSize.Y
    c.Parent = parent

    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(0.65, 0, 1, 0)
    l.Text = text
    l.TextColor3 = Color3.fromRGB(220, 220, 220)
    l.TextSize = 13
    l.Font = Enum.Font.Gotham
    l.BackgroundTransparency = 1
    l.TextXAlignment = Enum.TextXAlignment.Left
    l.Parent = c

    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.25, 0, 0.8, 0)
    b.Position = UDim2.new(0.72, 0, 0.1, 0)
    b.Text = default and "ON" or "OFF"
    b.BackgroundColor3 = default and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
    b.TextColor3 = default and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 12
    b.BorderSizePixel = 0
    b.Parent = c

    local bCorner = Instance.new("UICorner")
    bCorner.CornerRadius = UDim.new(0, 6)
    bCorner.Parent = b

    local st = default or false
    b.MouseButton1Click:Connect(function()
        st = not st
        b.Text = st and "ON" or "OFF"
        b.BackgroundColor3 = st and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
        b.TextColor3 = st and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
        callback(st)
        if configs.autoload then salvarConfiguracoes() end
    end)
    return c
end

local function addSlider(parent, text, callback, min, max, default)
    local c = Instance.new("Frame")
    c.Size = UDim2.new(0.95, 0, 0, 50)
    c.BackgroundTransparency = 1
    c.Parent = parent

    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 0, 20)
    l.Text = text .. " (" .. default .. ")"
    l.TextColor3 = Color3.fromRGB(220, 220, 220)
    l.TextSize = 13
    l.Font = Enum.Font.Gotham
    l.BackgroundTransparency = 1
    l.Parent = c

    local sf = Instance.new("Frame")
    sf.Size = UDim2.new(1, 0, 0, 14)
    sf.Position = UDim2.new(0, 0, 0, 22)
    sf.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    sf.BorderSizePixel = 0
    sf.Parent = c

    local sfCorner = Instance.new("UICorner")
    sfCorner.CornerRadius = UDim.new(0, 7)
    sfCorner.Parent = sf

    local sb = Instance.new("TextButton")
    sb.Size = UDim2.new(0, 16, 1, 0)
    sb.Position = UDim2.new((default - min) / (max - min), -8, 0, 0)
    sb.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    sb.Text = ""
    sb.BorderSizePixel = 0
    sb.Parent = sf

    local sbCorner = Instance.new("UICorner")
    sbCorner.CornerRadius = UDim.new(0, 8)
    sbCorner.Parent = sb

    local vl = Instance.new("TextLabel")
    vl.Size = UDim2.new(0, 35, 1, 0)
    vl.Position = UDim2.new(1, -38, 0, 0)
    vl.Text = tostring(default)
    vl.TextColor3 = Color3.fromRGB(255, 255, 255)
    vl.TextSize = 12
    vl.Font = Enum.Font.Gotham
    vl.BackgroundTransparency = 1
    vl.Parent = sf

    local draggingSlider = false
    local function update(input)
        local pos = input.Position.X
        local fp = sf.AbsolutePosition.X
        local fs = sf.AbsoluteSize.X
        local pct = math.clamp((pos - fp) / fs, 0, 1)
        local val = math.floor(min + pct * (max - min))
        sb.Position = UDim2.new(pct, -8, 0, 0)
        vl.Text = tostring(val)
        callback(val)
        l.Text = text .. " (" .. tostring(val) .. ")"
        if configs.autoload then salvarConfiguracoes() end
    end

    sb.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingSlider = true
            update(input)
        end
    end)
    sb.InputEnded:Connect(function() draggingSlider = false end)
    UserInputService.InputChanged:Connect(function(input)
        if draggingSlider and input.UserInputType == Enum.UserInputType.MouseMovement then
            update(input)
        end
    end)
end
-- ============================================================
-- CONFIGURAR ABAS
-- ============================================================

-- ABA MAIN
local mainCard = addCard(abaFramesMap["Main"])
addLabel(mainCard, "🔧 " .. t("main"))
addToggle(mainCard, t("noclip"), function(v) configs.noclip = v; toggleNoclip(v) end, configs.noclip)
addToggle(mainCard, t("fullbright"), function(v) configs.fullbright = v; toggleFullbright(v) end, configs.fullbright)
addToggle(mainCard, t("stamina"), function(v) configs.stamina = v; toggleStamina(v) end, configs.stamina)
addToggle(mainCard, t("o2"), function(v) configs.o2 = v; toggleO2(v) end, configs.o2)
addToggle(mainCard, t("antifrost"), function(v) configs.antifrost = v; toggleAntiTemp(v) end, configs.antifrost)
addToggle(mainCard, t("aimbot"), function(v) configs.aimbot = v end, configs.aimbot)
addSlider(mainCard, t("aimspeed"), function(v) configs.aim_speed = v end, 1, 100, configs.aim_speed)
addSlider(mainCard, t("aimdist"), function(v) configs.aim_distance = v end, 10, 200, configs.aim_distance)
addToggle(mainCard, t("bypass"), function(v) configs.bypass = v end, configs.bypass)
addToggle(mainCard, t("static"), function(v) configs.static = v end, configs.static)
addSlider(mainCard, t("sprint"), function(v) configs.sprint_speed = v end, 1, 50, configs.sprint_speed)

-- ABA MENU
local menuCard = addCard(abaFramesMap["Menu"])
addLabel(menuCard, "📋 " .. t("menu"))
addButton(menuCard, t("usuarios"), function()
    criarGUIUsuarios()
    mostrarUsuariosConsole()
end)
addButton(menuCard, t("feedback"), function()
    local dialog = Instance.new("ScreenGui")
    dialog.Name = "FeedbackDialog"
    dialog.Parent = player.PlayerGui
    dialog.ResetOnSpawn = false
    dialog.IgnoreGuiInset = true

    local fundo = Instance.new("Frame")
    fundo.Size = UDim2.new(1, 0, 1, 0)
    fundo.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    fundo.BackgroundTransparency = 0.5
    fundo.Parent = dialog

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 400, 0, 250)
    frame.Position = UDim2.new(0.5, -200, 0.5, -125)
    frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
    frame.BackgroundTransparency = 0.05
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = fundo

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = frame

    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.3
    border.Parent = frame

    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, 0, 0, 40)
    title.Text = "ENVIAR FEEDBACK"
    title.TextColor3 = Color3.fromRGB(255, 200, 50)
    title.TextSize = 16
    title.Font = Enum.Font.GothamBold
    title.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    title.BackgroundTransparency = 0.15
    title.Parent = frame

    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 16)
    titleCorner.Parent = title

    local close = Instance.new("TextButton")
    close.Size = UDim2.new(0, 30, 0, 30)
    close.Position = UDim2.new(1, -38, 0, 5)
    close.Text = "X"
    close.TextColor3 = Color3.fromRGB(255, 255, 255)
    close.TextSize = 18
    close.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
    close.BackgroundTransparency = 0.3
    close.BorderSizePixel = 0
    close.Parent = title
    local closeCorner = Instance.new("UICorner")
    closeCorner.CornerRadius = UDim.new(0, 8)
    closeCorner.Parent = close
    close.MouseButton1Click:Connect(function() dialog:Destroy() end)

    local categoriaBox = Instance.new("TextBox")
    categoriaBox.Size = UDim2.new(0.8, 0, 0, 35)
    categoriaBox.Position = UDim2.new(0.1, 0, 0.2, 0)
    categoriaBox.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    categoriaBox.BackgroundTransparency = 0.2
    categoriaBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    categoriaBox.TextSize = 14
    categoriaBox.Font = Enum.Font.Gotham
    categoriaBox.Text = ""
    categoriaBox.PlaceholderText = "Categoria (ex: Bug, Sugestão, Dúvida)..."
    categoriaBox.ClearTextOnFocus = true
    categoriaBox.BorderSizePixel = 0
    categoriaBox.Parent = frame
    local catCorner = Instance.new("UICorner")
    catCorner.CornerRadius = UDim.new(0, 8)
    catCorner.Parent = categoriaBox

    local msgBox = Instance.new("TextBox")
    msgBox.Size = UDim2.new(0.8, 0, 0, 80)
    msgBox.Position = UDim2.new(0.1, 0, 0.38, 0)
    msgBox.BackgroundColor3 = Color3.fromRGB(35, 35, 50)
    msgBox.BackgroundTransparency = 0.2
    msgBox.TextColor3 = Color3.fromRGB(255, 255, 255)
    msgBox.TextSize = 14
    msgBox.Font = Enum.Font.Gotham
    msgBox.Text = ""
    msgBox.PlaceholderText = "Digite sua mensagem aqui..."
    msgBox.ClearTextOnFocus = true
    msgBox.BorderSizePixel = 0
    msgBox.TextWrapped = true
    msgBox.TextXAlignment = Enum.TextXAlignment.Left
    msgBox.TextYAlignment = Enum.TextYAlignment.Top
    msgBox.Parent = frame
    local msgCorner = Instance.new("UICorner")
    msgCorner.CornerRadius = UDim.new(0, 8)
    msgCorner.Parent = msgBox

    local enviarBtn = Instance.new("TextButton")
    enviarBtn.Size = UDim2.new(0.4, 0, 0, 40)
    enviarBtn.Position = UDim2.new(0.3, 0, 0.8, 0)
    enviarBtn.Text = "ENVIAR"
    enviarBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    enviarBtn.TextSize = 16
    enviarBtn.Font = Enum.Font.GothamBold
    enviarBtn.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
    enviarBtn.BackgroundTransparency = 0.15
    enviarBtn.BorderSizePixel = 0
    enviarBtn.Parent = frame
    local enviarCorner = Instance.new("UICorner")
    enviarCorner.CornerRadius = UDim.new(0, 10)
    enviarCorner.Parent = enviarBtn

    enviarBtn.MouseButton1Click:Connect(function()
        local categoria = categoriaBox.Text
        local mensagem = msgBox.Text
        if mensagem == "" then
            showNotification("❌ Digite uma mensagem!", Color3.fromRGB(200, 0, 0))
            return
        end
        enviarFeedback(categoria, mensagem)
        dialog:Destroy()
    end)

    msgBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            enviarBtn.MouseButton1Click:Fire()
        end
    end)
end)

-- ABA TELEPORTES
local teleCard = addCard(abaFramesMap["Tele"])
addLabel(teleCard, "📡 " .. t("tele"))

addLabel(teleCard, "🌙 " .. t("n1"), Color3.fromRGB(200, 150, 100))
for nome, cframe in pairs(teleportsN1) do
    addButton(teleCard, "📍 " .. nome, function()
        teleportar(cframe)
        showNotification("Teleportado para " .. nome, Color3.fromRGB(0, 200, 100))
    end)
end

addLabel(teleCard, "🌙 " .. t("n2"), Color3.fromRGB(200, 150, 100))
for nome, cframe in pairs(teleportsN2) do
    addButton(teleCard, "📍 " .. nome, function()
        teleportar(cframe)
        showNotification("Teleportado para " .. nome, Color3.fromRGB(0, 200, 100))
    end)
end

addLabel(teleCard, "🌙 " .. t("n3"), Color3.fromRGB(200, 150, 100))
for nome, cframe in pairs(teleportsN3) do
    addButton(teleCard, "📍 " .. nome, function()
        teleportar(cframe)
        showNotification("Teleportado para " .. nome, Color3.fromRGB(0, 200, 100))
    end)
end

-- ABA NOITE 1
local n1Card = addCard(abaFramesMap["N1"])
addLabel(n1Card, "🌙 " .. t("n1") .. " - " .. t("geral"))
addButton(n1Card, "🔥 Refill Fireplace", function()
    RefillFireplace()
    showNotification("🔥 Lareira reabastecida!", Color3.fromRGB(255, 150, 50))
end)
addButton(n1Card, "🪵 Grab Wood", function()
    GrabWood()
    showNotification("🪵 Madeira pega!", Color3.fromRGB(150, 100, 50))
end)
addButton(n1Card, "⚡ Refill Generator", function()
    RefillGenerator()
    showNotification("⚡ Gerador reabastecido!", Color3.fromRGB(255, 200, 50))
end)
addButton(n1Card, "⛽ Grab Jerry Can", function()
    GrabJerryCan()
    showNotification("⛽ Galão de gasolina pego!", Color3.fromRGB(200, 150, 50))
end)

-- ABA NOITE 2
local n2Card = addCard(abaFramesMap["N2"])
addLabel(n2Card, "🌙 " .. t("n2") .. " - " .. t("geral"))
addToggle(n2Card, t("autoscare"), function(v) configs.autoscare = v end, configs.autoscare)
addToggle(n2Card, t("antivent"), function(v) configs.antivent = v end, configs.antivent)
addToggle(n2Card, t("revive"), function(v) configs.revive = v end, configs.revive)
addToggle(n2Card, t("escapesnatch"), function(v) configs.escapesnatch = v end, configs.escapesnatch)
addToggle(n2Card, t("refillpower"), function(v) configs.refillpower = v end, configs.refillpower)
addToggle(n2Card, t("antistalker"), function(v) configs.antistalker = v end, configs.antistalker)

-- ABA NOITE 3
local n3Card = addCard(abaFramesMap["N3"])
addLabel(n3Card, "🌙 " .. t("n3") .. " - " .. t("geral"))
addToggle(n3Card, t("municao"), function(v) configs.municao = v end, configs.municao)

-- ABA ESP
local espCard = addCard(abaFramesMap["ESP"])
addLabel(espCard, "👁️ " .. t("esp"))
addToggle(espCard, t("esp_players"), function(v) configs.esp_players = v end, configs.esp_players)
addToggle(espCard, t("esp_larry"), function(v) configs.esp_larry = v end, configs.esp_larry)
addToggle(espCard, t("esp_stalker"), function(v) configs.esp_stalker = v end, configs.esp_stalker)
addToggle(espCard, t("esp_zombie"), function(v) configs.esp_zombie = v end, configs.esp_zombie)

-- ABA GERAL
local geralCard = addCard(abaFramesMap["Geral"])
addLabel(geralCard, "⚙️ " .. t("geral"))
addToggle(geralCard, t("notifier"), function(v)
    configs.notifier = v
    if v then iniciarNotificador() else desativarNotificador() end
end, configs.notifier)
addToggle(geralCard, t("autoload"), function(v)
    configs.autoload = v
    if v then showNotification("💾 Auto Load ATIVADO!", Color3.fromRGB(100, 200, 100))
    else showNotification("💾 Auto Load DESATIVADO!", Color3.fromRGB(100, 200, 100)) end
end, configs.autoload)

-- ABA CONFIG
local configCard = addCard(abaFramesMap["Config"])
addLabel(configCard, "⚙️ " .. t("config"))
addButton(configCard, "💾 " .. t("salvar"), function()
    salvarConfiguracoes()
    showNotification("✅ Configurações salvas!", Color3.fromRGB(0, 200, 0))
end)
addButton(configCard, "📂 " .. t("carregar"), function()
    if carregarConfiguracoes() then
        showNotification("✅ Configurações carregadas!", Color3.fromRGB(0, 200, 0))
        task.wait(0.5)
        gui:Destroy()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/soniaasus2-arch/Night4/refs/heads/main/README.md"))()
    else
        showNotification("❌ Erro ao carregar configurações!", Color3.fromRGB(200, 0, 0))
    end
end)
addLabel(configCard, "🌐 " .. t("idioma"), Color3.fromRGB(100, 200, 255))
for _, lang in pairs(listaIdiomas) do
    addButton(configCard, lang.nome, function()
        idiomaAtual = lang.codigo
        showNotification(t("idioma_alterado") .. lang.nome, Color3.fromRGB(100, 200, 255))
        salvarConfiguracoes()
        task.wait(0.5)
        gui:Destroy()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/soniaasus2-arch/Night4/refs/heads/main/README.md"))()
    end)
end


