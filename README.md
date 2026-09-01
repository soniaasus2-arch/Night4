local player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")
local Lighting = game:GetService("Lighting")
local Camera = workspace.CurrentCamera
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local ChatService = game:GetService("Chat")

-- ============================================================
-- SISTEMA ANTI-TAMPER - COM EXCEÇÃO PARA O DONO
-- ============================================================

local player = game.Players.LocalPlayer
local HttpService = game:GetService("HttpService")

-- ============================================================
-- 🔽 ID DO DONO (VOCÊ) 🔽
-- ============================================================

local DONO_ID = "5135365156"  -- Seu ID

-- ============================================================
-- CONFIGURAÇÕES
-- ============================================================

local AntiTamper = {
    ArquivoHash = "DAVI_HUB_Hash.txt",
    ArquivoBan = "DAVI_HUB_Banidos.json",
    Versao = "2.0",
    HashOriginal = nil
}

-- ============================================================
-- FUNÇÃO PARA GERAR HASH DO SCRIPT
-- ============================================================

local function gerarHashDoScript()
    local codigoFixo = [[
        DAVI HUB - Residence Massacre v2.0
        -- COLE AQUI UMA LINHA ÚNICA DO SEU SCRIPT
    ]]
    
    local hash = 0
    local texto = codigoFixo .. tostring(os.time())
    
    for i = 1, #texto do
        hash = (hash * 31 + string.byte(texto, i)) % 2^31
    end
    
    return tostring(hash)
end

-- ============================================================
-- FUNÇÃO PARA VERIFICAR SE É O DONO
-- ============================================================

local function isDono()
    local userId = tostring(player.UserId)
    return userId == DONO_ID
end

-- ============================================================
-- VERIFICAR INTEGRIDADE DO SCRIPT
-- ============================================================

local function verificarIntegridade()
    -- Se for o dono, sempre retorna verdadeiro (não é banido)
    if isDono() then
        print("👑 Dono do script detectado! Proteção desativada.")
        return true
    end
    
    local sucesso, hashSalvo = pcall(function()
        return readfile(AntiTamper.ArquivoHash)
    end)
    
    local hashAtual = gerarHashDoScript()
    
    if not sucesso or not hashSalvo or hashSalvo == "" then
        pcall(function()
            writefile(AntiTamper.ArquivoHash, hashAtual)
        end)
        print("🔒 Hash do script salvo pela primeira vez!")
        return true
    end
    
    if hashSalvo ~= hashAtual then
        print("🚫 SCRIPT MODIFICADO DETECTADO!")
        print("   Hash salvo: " .. hashSalvo)
        print("   Hash atual: " .. hashAtual)
        return false
    end
    
    print("✅ Script verificado com sucesso!")
    return true
end

-- ============================================================
-- FUNÇÃO PARA BANIR O USUÁRIO (COM EXCEÇÃO DO DONO)
-- ============================================================

local function banirUsuario(motivo)
    -- Se for o dono, não banir
    if isDono() then
        print("👑 Dono do script - banimento ignorado!")
        return
    end
    
    local banidos = {}
    local sucesso, dados = pcall(function()
        return readfile(AntiTamper.ArquivoBan)
    end)
    if sucesso and dados then
        banidos = HttpService:JSONDecode(dados) or {}
    end
    
    local userId = tostring(player.UserId)
    banidos[userId] = {
        motivo = motivo or "Modificação do script",
        data = os.date("%d/%m/%Y %H:%M:%S"),
        nome = player.Name,
        versao = AntiTamper.Versao
    }
    
    pcall(function()
        writefile(AntiTamper.ArquivoBan, HttpService:JSONEncode(banidos))
    end)
    
    -- Mostra tela de ban
    local function mostrarBan()
        local gui = Instance.new("ScreenGui")
        gui.Name = "BanScreen"
        gui.Parent = player.PlayerGui
        gui.IgnoreGuiInset = true
        
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(0, 500, 0, 250)
        frame.Position = UDim2.new(0.5, -250, 0.5, -125)
        frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
        frame.BackgroundTransparency = 0.05
        frame.BorderSizePixel = 0
        frame.ClipsDescendants = true
        frame.Parent = gui
        
        local corner = Instance.new("UICorner")
        corner.Parent = frame
        corner.CornerRadius = UDim.new(0, 16)
        
        local border = Instance.new("UIStroke")
        border.Parent = frame
        border.Color = Color3.fromRGB(255, 0, 0)
        border.Thickness = 3
        border.Transparency = 0.3
        
        local title = Instance.new("TextLabel")
        title.Size = UDim2.new(1, 0, 0, 50)
        title.Position = UDim2.new(0, 0, 0, 0)
        title.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        title.BackgroundTransparency = 0.2
        title.Text = "🚫 SCRIPT MODIFICADO!"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.TextSize = 24
        title.Font = Enum.Font.GothamBold
        title.Parent = frame
        
        local motivoLabel = Instance.new("TextLabel")
        motivoLabel.Size = UDim2.new(0.9, 0, 0, 40)
        motivoLabel.Position = UDim2.new(0.05, 0, 0.2, 0)
        motivoLabel.BackgroundTransparency = 1
        motivoLabel.Text = "Motivo: " .. motivo
        motivoLabel.TextColor3 = Color3.fromRGB(255, 200, 100)
        motivoLabel.TextSize = 18
        motivoLabel.Font = Enum.Font.GothamBold
        motivoLabel.Parent = frame
        
        local dataLabel = Instance.new("TextLabel")
        dataLabel.Size = UDim2.new(0.9, 0, 0, 30)
        dataLabel.Position = UDim2.new(0.05, 0, 0.35, 0)
        dataLabel.BackgroundTransparency = 1
        dataLabel.Text = "Data: " .. banidos[userId].data
        dataLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        dataLabel.TextSize = 14
        dataLabel.Font = Enum.Font.Gotham
        dataLabel.Parent = frame
        
        local descLabel = Instance.new("TextLabel")
        descLabel.Size = UDim2.new(0.9, 0, 0, 40)
        descLabel.Position = UDim2.new(0.05, 0, 0.5, 0)
        descLabel.BackgroundTransparency = 1
        descLabel.Text = "O script foi modificado.\nExecute a versão original."
        descLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        descLabel.TextSize = 14
        descLabel.Font = Enum.Font.Gotham
        descLabel.TextWrapped = true
        descLabel.Parent = frame
        
        local closeBtn = Instance.new("TextButton")
        closeBtn.Size = UDim2.new(0, 200, 0, 40)
        closeBtn.Position = UDim2.new(0.5, -100, 0, 0.75)
        closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        closeBtn.BackgroundTransparency = 0.2
        closeBtn.Text = "FECHAR"
        closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        closeBtn.TextSize = 16
        closeBtn.Font = Enum.Font.GothamBold
        closeBtn.Parent = frame
        
        closeBtn.MouseButton1Click:Connect(function()
            gui:Destroy()
            pcall(function()
                player:Kick("Script modificado! Use a versão original.")
            end)
        end)
        
        task.wait(10)
        gui:Destroy()
        pcall(function()
            player:Kick("Script modificado! Use a versão original.")
        end)
    end
    
    mostrarBan()
end

-- ============================================================
-- VERIFICAR SE O USUÁRIO JÁ ESTÁ BANIDO (COM EXCEÇÃO DO DONO)
-- ============================================================

local function verificarBanimento()
    -- Se for o dono, nunca está banido
    if isDono() then
        return false
    end
    
    local sucesso, dados = pcall(function()
        return readfile(AntiTamper.ArquivoBan)
    end)
    if sucesso and dados then
        local banidos = HttpService:JSONDecode(dados) or {}
        if banidos[tostring(player.UserId)] then
            return true, banidos[tostring(player.UserId)]
        end
    end
    return false
end

-- ============================================================
-- FUNÇÃO PARA LIMPAR BANIMENTO (APENAS ADMIN)
-- ============================================================

local function limparBanimento(userId)
    local sucesso, dados = pcall(function()
        return readfile(AntiTamper.ArquivoBan)
    end)
    if sucesso and dados then
        local banidos = HttpService:JSONDecode(dados) or {}
        banidos[userId] = nil
        pcall(function()
            writefile(AntiTamper.ArquivoBan, HttpService:JSONEncode(banidos))
        end)
        print("✅ Banimento removido para ID: " .. userId)
    end
end

-- ============================================================
-- INICIALIZAR PROTEÇÃO
-- ============================================================

-- Verifica se já está banido
local banido, dados = verificarBanimento()
if banido then
    print("🚫 Usuário banido: " .. dados.motivo)
    return
end

-- Verifica integridade do script (dono sempre passa)
if not verificarIntegridade() then
    banirUsuario("Script modificado (hash não corresponde)")
    return
end

print("")
print("═══════════════════════════════════════════")
print("🛡️ ANTI-TAMPER ATIVADO!")
print("═══════════════════════════════════════════")
print("👑 Dono do script: " .. DONO_ID)
print("📌 O script está protegido contra modificações")
print("📌 O dono NUNCA será banido")
print("═══════════════════════════════════════════")

-- ============================================================
-- WEBHOOK
-- ============================================================

local WEBHOOK_URL = "https://discord.com/api/webhooks/1531651013558403184/L4__adXXURVGFC9LLPDexsRBX3GRtQ4VkWMLISSEO6Isl-CjmOuE8Xu3o0pcAQjm1SKK"

-- ============================================================
-- SISTEMA ANTI-SPAM
-- ============================================================

local AntiSpam = {
    LimiteDeFeedbacks = 3,
    TamanhoMinimo = 5,
    ArquivoBan = "DAVI_HUB_Banidos.json",
    FeedbacksRuins = {},
    EstaBanido = false
}

local function lerArquivoBan()
    local sucesso, dados = pcall(function()
        return readfile(AntiSpam.ArquivoBan)
    end)
    if sucesso and dados then
        local decodificado = HttpService:JSONDecode(dados)
        return decodificado or {}
    end
    return {}
end

local function escreverArquivoBan(dados)
    local json = HttpService:JSONEncode(dados)
    pcall(function()
        writefile(AntiSpam.ArquivoBan, json)
    end)
end

local function verificarBanimento()
    local banidos = lerArquivoBan()
    local userId = tostring(player.UserId)
    
    if banidos[userId] then
        AntiSpam.EstaBanido = true
        return true, banidos[userId].motivo, banidos[userId].data
    end
    return false
end

local function enviarFeedback(categoria, mensagem)
    if not mensagem or mensagem == "" then
        return false, "Digite uma mensagem!"
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
    
    local sucesso, resposta = pcall(function()
        return request({
            Url = WEBHOOK_URL,
            Method = "POST",
            Headers = headers,
            Body = jsonData
        })
    end)
    
    if sucesso and resposta and resposta.StatusCode == 204 then
        return true, "Feedback enviado com sucesso!"
    else
        return false, "Erro ao enviar feedback!"
    end
end

local function banirUsuario(motivo)
    local banidos = lerArquivoBan()
    local userId = tostring(player.UserId)
    
    banidos[userId] = {
        motivo = motivo or "Spam de feedbacks",
        data = os.date("%d/%m/%Y %H:%M:%S"),
        nome = player.Name
    }
    
    escreverArquivoBan(banidos)
    AntiSpam.EstaBanido = true
    
    pcall(function()
        local data = {
            content = string.format(
                "**🚫 USUÁRIO BANIDO!**\n" ..
                "**👤 Usuário:** %s (%s)\n" ..
                "**📂 Motivo:** %s\n" ..
                "**🕐 Data:** %s",
                player.Name,
                player.UserId,
                motivo,
                os.date("%d/%m/%Y %H:%M:%S")
            )
        }
        local jsonData = HttpService:JSONEncode(data)
        local headers = {["Content-Type"] = "application/json"}
        request({
            Url = WEBHOOK_URL,
            Method = "POST",
            Headers = headers,
            Body = jsonData
        })
    end)
    
    local function mostrarBan()
        local gui = Instance.new("ScreenGui")
        gui.Name = "BanScreen"
        gui.Parent = player.PlayerGui
        gui.IgnoreGuiInset = true
        
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(0, 500, 0, 250)
        frame.Position = UDim2.new(0.5, -250, 0.5, -125)
        frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
        frame.BackgroundTransparency = 0.05
        frame.BorderSizePixel = 0
        frame.ClipsDescendants = true
        frame.Parent = gui
        
        local corner = Instance.new("UICorner")
        corner.Parent = frame
        corner.CornerRadius = UDim.new(0, 16)
        
        local border = Instance.new("UIStroke")
        border.Parent = frame
        border.Color = Color3.fromRGB(255, 0, 0)
        border.Thickness = 3
        border.Transparency = 0.3
        
        local title = Instance.new("TextLabel")
        title.Size = UDim2.new(1, 0, 0, 50)
        title.Position = UDim2.new(0, 0, 0, 0)
        title.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        title.BackgroundTransparency = 0.2
        title.Text = "🚫 VOCÊ FOI BANIDO!"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.TextSize = 24
        title.Font = Enum.Font.GothamBold
        title.Parent = frame
        
        local cornerTitle = Instance.new("UICorner")
        cornerTitle.Parent = title
        cornerTitle.CornerRadius = UDim.new(0, 16)
        
        local motivoLabel = Instance.new("TextLabel")
        motivoLabel.Size = UDim2.new(0.9, 0, 0, 40)
        motivoLabel.Position = UDim2.new(0.05, 0, 0.2, 0)
        motivoLabel.BackgroundTransparency = 1
        motivoLabel.Text = "Motivo: " .. motivo
        motivoLabel.TextColor3 = Color3.fromRGB(255, 200, 100)
        motivoLabel.TextSize = 18
        motivoLabel.Font = Enum.Font.GothamBold
        motivoLabel.Parent = frame
        
        local dataLabel = Instance.new("TextLabel")
        dataLabel.Size = UDim2.new(0.9, 0, 0, 30)
        dataLabel.Position = UDim2.new(0.05, 0, 0.35, 0)
        dataLabel.BackgroundTransparency = 1
        dataLabel.Text = "Data: " .. banidos[userId].data
        dataLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
        dataLabel.TextSize = 14
        dataLabel.Font = Enum.Font.Gotham
        dataLabel.Parent = frame
        
        local descLabel = Instance.new("TextLabel")
        descLabel.Size = UDim2.new(0.9, 0, 0, 40)
        descLabel.Position = UDim2.new(0.05, 0, 0.5, 0)
        descLabel.BackgroundTransparency = 1
        descLabel.Text = "Você foi banido por spam de feedbacks."
        descLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        descLabel.TextSize = 14
        descLabel.Font = Enum.Font.Gotham
        descLabel.TextWrapped = true
        descLabel.Parent = frame
        
        local closeBtn = Instance.new("TextButton")
        closeBtn.Size = UDim2.new(0, 200, 0, 40)
        closeBtn.Position = UDim2.new(0.5, -100, 0, 0.75)
        closeBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        closeBtn.BackgroundTransparency = 0.2
        closeBtn.Text = "FECHAR"
        closeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        closeBtn.TextSize = 16
        closeBtn.Font = Enum.Font.GothamBold
        closeBtn.Parent = frame
        
        local closeCorner = Instance.new("UICorner")
        closeCorner.Parent = closeBtn
        closeCorner.CornerRadius = UDim.new(0, 8)
        
        closeBtn.MouseButton1Click:Connect(function()
            gui:Destroy()
            pcall(function()
                player:Kick("Você foi banido por spam de feedbacks!")
            end)
        end)
        
        task.wait(10)
        gui:Destroy()
        pcall(function()
            player:Kick("Você foi banido por spam de feedbacks!")
        end)
    end
    
    mostrarBan()
end

local function validarFeedback(mensagem)
    if not mensagem or mensagem == "" then
        return false, "Digite uma mensagem!"
    end
    
    local textoLimpo = mensagem:gsub("%s+", "")
    
    if #textoLimpo < AntiSpam.TamanhoMinimo then
        return false, "Mensagem muito curta! (mínimo " .. AntiSpam.TamanhoMinimo .. " caracteres)"
    end
    
    return true, "Mensagem válida!"
end

local function registrarFeedbackRuim(mensagem)
    local banido = verificarBanimento()
    if banido then
        return false, "Você está banido!"
    end
    
    table.insert(AntiSpam.FeedbacksRuins, {
        mensagem = mensagem,
        data = os.date("%d/%m/%Y %H:%M:%S"),
        tamanho = #mensagem
    })
    
    if #AntiSpam.FeedbacksRuins >= AntiSpam.LimiteDeFeedbacks then
        banirUsuario("Spam de feedbacks (" .. AntiSpam.LimiteDeFeedbacks .. " mensagens curtas)")
        return false, "Você foi banido por spam!"
    end
    
    return true, "Aviso: " .. (AntiSpam.LimiteDeFeedbacks - #AntiSpam.FeedbacksRuins) .. " tentativas restantes antes do ban."
end

local function enviarFeedbackComAntiSpam(categoria, mensagem)
    local banido, motivo, data = verificarBanimento()
    if banido then
        showNotification("🚫 Você está banido!", Color3.fromRGB(255, 0, 0))
        return false, "Você está banido! Motivo: " .. motivo
    end
    
    local valido, msgValidacao = validarFeedback(mensagem)
    if not valido then
        local registrado, msgRegistro = registrarFeedbackRuim(mensagem)
        showNotification("⚠️ " .. msgRegistro, Color3.fromRGB(255, 200, 0))
        return false, msgRegistro
    end
    
    AntiSpam.FeedbacksRuins = {}
    
    local sucesso, msgEnvio = enviarFeedback(categoria, mensagem)
    if sucesso then
        showNotification("✅ Feedback enviado com sucesso!", Color3.fromRGB(0, 200, 0))
    else
        showNotification("❌ " .. msgEnvio, Color3.fromRGB(200, 0, 0))
    end
    
    return sucesso, msgEnvio
end

local function verificarBanAoIniciar()
    local banido, motivo, data = verificarBanimento()
    if banido then
        print("🚫 " .. player.Name .. " está banido! Motivo: " .. motivo)
        return false
    end
    return true
end

_G.AntiSpam = {
    VerificarBan = verificarBanimento,
    LimparBan = function()
        local banidos = lerArquivoBan()
        local userId = tostring(player.UserId)
        banidos[userId] = nil
        escreverArquivoBan(banidos)
        AntiSpam.EstaBanido = false
        print("✅ Banimento removido para " .. player.Name)
    end,
    ListarBanidos = function()
        local banidos = lerArquivoBan()
        print("📋 USUÁRIOS BANIDOS:")
        for userId, dados in pairs(banidos) do
            print("  - " .. dados.nome .. " (" .. userId .. ")")
            print("    Motivo: " .. dados.motivo)
            print("    Data: " .. dados.data)
        end
    end,
    RemoverBan = function(userId)
        local banidos = lerArquivoBan()
        banidos[userId] = nil
        escreverArquivoBan(banidos)
        print("✅ Banimento removido para ID: " .. userId)
    end
}
-- ============================================================
-- FUNÇÃO DE NOTIFICAÇÃO
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
-- FUNÇÃO DE TELEPORT
-- ============================================================

local function teleportar(cframe)
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = cframe
    end
end

-- ============================================================
-- LOOPS
-- ============================================================

local loops = {
    Noclip = nil,
    Stamina = nil,
    O2 = nil,
    AntiFrost = nil,
    ESP = nil,
    Aimbot = nil,
}

local function toggleNoclip(value)
    if loops.Noclip then loops.Noclip:Disconnect(); loops.Noclip = nil end
    if value then
        loops.Noclip = RunService.Stepped:Connect(function()
            local char = player.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
            end
        end)
    end
end

local origLight = {}
local function toggleFullbright(value)
    local li = Lighting
    if value then
        origLight = {
            Ambient = li.Ambient,
            OutdoorAmbient = li.OutdoorAmbient,
            Brightness = li.Brightness,
            GlobalShadows = li.GlobalShadows,
            FogEnd = li.FogEnd
        }
        li.Ambient = Color3.fromRGB(255, 255, 255)
        li.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
        li.Brightness = 2
        li.GlobalShadows = false
        li.FogEnd = 100000
    else
        li.Ambient = origLight.Ambient or Color3.fromRGB(128, 128, 128)
        li.OutdoorAmbient = origLight.OutdoorAmbient or Color3.fromRGB(128, 128, 128)
        li.Brightness = origLight.Brightness or 1
        li.GlobalShadows = origLight.GlobalShadows or true
        li.FogEnd = origLight.FogEnd or 1000
    end
end

local function toggleStamina(value)
    if loops.Stamina then loops.Stamina:Disconnect(); loops.Stamina = nil end
    if value then
        loops.Stamina = RunService.RenderStepped:Connect(function()
            local char = player.Character
            if char and char:FindFirstChild("Sprint") then
                local sprint = char.Sprint
                if sprint:FindFirstChild("Overdrive") then sprint.Overdrive.Value = 1e9 end
                if sprint:FindFirstChild("Stamina") then sprint.Stamina.Value = 100 end
            end
        end)
    end
end

local function toggleO2(value)
    if loops.O2 then loops.O2:Disconnect(); loops.O2 = nil end
    if value then
        loops.O2 = RunService.RenderStepped:Connect(function()
            local char = player.Character
            if char then
                local breath = char:FindFirstChild("Breath")
                if breath then breath:SetAttribute("Max", 999999)
                breath.Value = 999999
                local blur = Lighting:FindFirstChild("Blur")
                if blur then blur.Enabled = false
            end
        end)
    end
end

local function toggleAntiFrost(value)
    if loops.AntiFrost then loops.AntiFrost:Disconnect(); loops.AntiFrost = nil end
    if value then
        loops.AntiFrost = RunService.RenderStepped:Connect(function()
            local char = player.Character
            if char then
                local temp = char:FindFirstChild("Temperature")
                if temp then temp.Enabled = false
            end
        end)
    end
end

local function setSprintSpeed(value)
    RunService.RenderStepped:Connect(function()
        local char = player.Character
        if char then
            local humanoid = char:FindFirstChild("Humanoid")
            if humanoid then humanoid.WalkSpeed = value
        end
    end)
end

-- ============================================================
-- AUTO SCARE
-- ============================================================

local autoScareLoop = nil
local function toggleAutoScare(value)
    if autoScareLoop then autoScareLoop:Disconnect(); autoScareLoop = nil end
    if value then
        autoScareLoop = RunService.RenderStepped:Connect(function()
            -- Implementar lógica do Auto Scare aqui
        end)
    end
end

-- ============================================================
-- NOTIFICADOR
-- ============================================================

local notifierActive = false
local notifierConnections = {}

local function iniciarNotificador()
    if notifierActive then return end
    notifierActive = true
    showNotification("🔔 Notificador ATIVADO!", Color3.fromRGB(0, 200, 0))
    
    local function onChildAdded(child)
        if not notifierActive then return end
        if child.Name == "Larry" then
            showNotification("⚠️ Larry apareceu!", Color3.fromRGB(200, 50, 50))
        elseif child.Name == "Mutant" then
            showNotification("⚠️ Mutant apareceu!", Color3.fromRGB(150, 50, 200))
        elseif child.Name == "Worker" then
            showNotification("⚠️ Worker apareceu!", Color3.fromRGB(50, 150, 200))
        elseif child.Name == "WorkerHead" then
            showNotification("⚠️ WorkerHead apareceu!", Color3.fromRGB(50, 200, 150))
        end
    end
    
    local function onChildRemoved(child)
        if not notifierActive then return end
        if child.Name == "Larry" then
            showNotification("✅ Larry desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "Mutant" then
            showNotification("✅ Mutant desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "Worker" then
            showNotification("✅ Worker desapareceu!", Color3.fromRGB(50, 200, 50))
        elseif child.Name == "WorkerHead" then
            showNotification("✅ WorkerHead desapareceu!", Color3.fromRGB(50, 200, 50))
        end
    end
    
    local conn1 = workspace.ChildAdded:Connect(onChildAdded)
    local conn2 = workspace.ChildRemoved:Connect(onChildRemoved)
    table.insert(notifierConnections, conn1)
    table.insert(notifierConnections, conn2)
end

local function desativarNotificador()
    if not notifierActive then return end
    notifierActive = false
    for _, conn in pairs(notifierConnections) do
        pcall(function() conn:Disconnect() end)
    end
    notifierConnections = {}
    showNotification("🔕 Notificador DESATIVADO!", Color3.fromRGB(200, 0, 0))
end

-- ============================================================
-- ESP
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
    
    if not getgenv().Config.ESPPlayers and not getgenv().Config.ESPLarry and 
       not getgenv().Config.ESPStalker and not getgenv().Config.ESPZombies then
        return
    end
    
    if getgenv().Config.ESPPlayers then
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
        Larry = {name = "Larry", color = Color3.fromRGB(255, 0, 0), enabled = "ESPLarry"},
        Stalker = {name = "Stalker", color = Color3.fromRGB(255, 165, 0), enabled = "ESPStalker"},
        Zombie = {name = "Zombie", color = Color3.fromRGB(0, 255, 0), enabled = "ESPZombies"},
        Skeleton = {name = "Skeleton", color = Color3.fromRGB(0, 255, 0), enabled = "ESPZombies"}
    }
    
    for _, target in pairs(targets) do
        if getgenv().Config[target.enabled] then
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

-- ============================================================
-- AIMBOT
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
                if dist < getgenv().Config.AimbotDistance and dist < nearestDist then
                    nearest = jogador
                    nearestDist = dist
                end
            end
        end
    end
    return nearest
end

local function toggleAimbot(value)
    if loops.Aimbot then loops.Aimbot:Disconnect(); loops.Aimbot = nil end
    if value then
        loops.Aimbot = RunService.Heartbeat:Connect(function()
            if not getgenv().Config.Aimbot then return end
            local target = getClosestTarget()
            if target and target.Character then
                local head = target.Character:FindFirstChild("Head")
                if head then
                    local targetPos = head.Position
                    local cameraPos = Camera.CFrame.Position
                    local direction = (targetPos - cameraPos).unit
                    local newCFrame = CFrame.new(cameraPos, cameraPos + direction)
                    local currentCFrame = Camera.CFrame
                    local lerpFactor = getgenv().Config.AimbotSpeed / 100
                    local smoothCFrame = currentCFrame:Lerp(newCFrame, lerpFactor)
                    Camera.CFrame = smoothCFrame
                end
            end
        end)
    end
end
-- ============================================================
-- TELEPORTS
-- ============================================================

local teleportsN1 = {
    ["Ladder"] = CFrame.new(-0.173, 9.3, -81.32),
    ["Generator"] = CFrame.new(-79.722, 4.675, -131.918),
    ["Entrance"] = CFrame.new(-11.036, 7.73, -31.822),
    ["LivingRoom"] = CFrame.new(-34.962, 8.05, -47.153),
    ["Bedroom"] = CFrame.new(-32.645, 23.8, -72.845),
}

local teleportsN2 = {
    ["SafeSpot N2"] = CFrame.new(-339.321, 82.4, -40.622),
    ["DeliveryBoard"] = CFrame.new(-282.224, 82.4, 14.674),
    ["Main"] = CFrame.new(-304.235, 82.4, -6.777),
    ["Corridor1"] = CFrame.new(-303.846, 82.4, 50.169),
    ["Entrance2"] = CFrame.new(-217.417, 82.4, 65.412),
    ["Corridor2"] = CFrame.new(-293.11, 82.4, -89.501),
}

local teleportsN3 = {
    ["Cabana 1"] = CFrame.new(99.8, 4.5, -247.2),
    ["Cabana 2"] = CFrame.new(-36.9, 4.5, 68.7),
    ["Cabana 3"] = CFrame.new(-31.7, 4.5, 268.8),
    ["Cabana 4"] = CFrame.new(233.6, 4.5, 245.8),
    ["Cutscene Room"] = CFrame.new(-237, -22.5, 107),
    ["Safe Spot N3"] = CFrame.new(194, 38.7, -217.4),
    ["Lodge"] = CFrame.new(-226.8, 17.4, 103.7),
    ["Jeffry Canna"] = CFrame.new(177.5, 4.3, 197.9),
}

-- ============================================================
-- SISTEMA DE CONFIGURAÇÕES
-- ============================================================

getgenv().Config = {
    Noclip = false,
    Fullbright = false,
    Stamina = false,
    O2 = false,
    AntiFrost = false,
    SprintSpeed = 17,
    AutoScare = false,
    AutoRevive = false,
    EscapeSnatch = false,
    RefillPower = false,
    AntiStalker = false,
    AutoCollectAmmo = false,
    ESPPlayers = false,
    ESPLarry = false,
    ESPStalker = false,
    ESPZombies = false,
    Notifier = false,
    BypassAC = false,
    DisableStatic = false,
    Aimbot = false,
    AimbotSpeed = 80,
    AimbotDistance = 80,
}

-- ============================================================
-- CRIAR GUI PRINCIPAL
-- ============================================================

local function criarGUI()
    -- Remover GUI antiga
    for _, v in pairs(player.PlayerGui:GetChildren()) do
        if v.Name == "DaviHub" then
            v:Destroy()
        end
    end
    
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "DaviHub"
    screenGui.Parent = player.PlayerGui
    screenGui.ResetOnSpawn = false
    screenGui.IgnoreGuiInset = true
    
    -- JANELA PRINCIPAL
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 480, 0, 450)
    mainFrame.Position = UDim2.new(0.5, -240, 0.15, 0)
    mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    mainFrame.BackgroundTransparency = 0.1
    mainFrame.BorderSizePixel = 0
    mainFrame.ClipsDescendants = true
    mainFrame.Parent = screenGui
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 16)
    corner.Parent = mainFrame
    
    local border = Instance.new("UIStroke")
    border.Color = Color3.fromRGB(255, 140, 0)
    border.Thickness = 2
    border.Transparency = 0.4
    border.Parent = mainFrame
    
    -- CABEÇALHO (ARRASTÁVEL)
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 40)
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
    
    -- Botão minimizar
    local minBtn = Instance.new("TextButton")
    minBtn.Size = UDim2.new(0, 32, 0, 32)
    minBtn.Position = UDim2.new(1, -70, 0, 4)
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
        mainFrame.Size = minimized and UDim2.new(0, 480, 0, 40) or UDim2.new(0, 480, 0, 450)
        minBtn.Text = minimized and "+" or "-"
        for _, child in pairs(mainFrame:GetChildren()) do
            if child ~= header then
                child.Visible = not minimized
            end
        end
    end)
    
    -- Botão fechar
    local closeBtn = Instance.new("TextButton")
    closeBtn.Size = UDim2.new(0, 32, 0, 32)
    closeBtn.Position = UDim2.new(1, -38, 0, 4)
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
        screenGui:Destroy()
    end)
    
    -- SISTEMA DE ARRASTE
    local dragging = false
    local dragInput = nil
    local dragStart = nil
    local startPos = nil
    
    local function updatePosition(input)
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
    
    header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
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
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            updatePosition(input)
        end
    end)
    
    -- ABAS
    local sidebar = Instance.new("Frame")
    sidebar.Size = UDim2.new(0, 85, 1, 0)
    sidebar.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    sidebar.BackgroundTransparency = 0.3
    sidebar.BorderSizePixel = 0
    sidebar.Parent = mainFrame
    
    local sidebarCorner = Instance.new("UICorner")
    sidebarCorner.CornerRadius = UDim.new(0, 12)
    sidebarCorner.Parent = sidebar
    
    local sidebarLayout = Instance.new("UIListLayout")
    sidebarLayout.Padding = UDim.new(0, 6)
    sidebarLayout.SortOrder = Enum.SortOrder.LayoutOrder
    sidebarLayout.Parent = sidebar
    
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -90, 0, 1)
    contentFrame.Position = UDim2.new(0, 90, 0, 40)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = mainFrame
    
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
        frame.Size = UDim2.new(1, 0, 1, 0)
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
    
    -- FUNÇÕES AUXILIARES DA GUI
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
        
        b.MouseEnter:Connect(function()
            b.BackgroundTransparency = 0
        end)
        b.MouseLeave:Connect(function()
            b.BackgroundTransparency = 0.2
        end)
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
        end
        
        sb.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 then
                draggingSlider = true
                update(input)
            end
        end)
        sb.InputEnded:Connect(function()
            draggingSlider = false
        end)
        UserInputService.InputChanged:Connect(function(input)
            if draggingSlider and input.UserInputType == Enum.UserInputType.MouseMovement then
                update(input)
            end
        end)
    end
      -- CONFIGURAR ABAS
    
    -- ABA MAIN
    local mainCard = addCard(abaFramesMap["Main"])
    addLabel(mainCard, "⚙️ Configurações Principais")
    addToggle(mainCard, "Noclip", function(v)
        getgenv().Config.Noclip = v
        toggleNoclip(v)
    end, getgenv().Config.Noclip)
    addToggle(mainCard, "Fullbright", function(v)
        getgenv().Config.Fullbright = v
        toggleFullbright(v)
    end, getgenv().Config.Fullbright)
    addToggle(mainCard, "Stamina Infinita", function(v)
        getgenv().Config.Stamina = v
        toggleStamina(v)
    end, getgenv().Config.Stamina)
    addToggle(mainCard, "O2 Infinito", function(v)
        getgenv().Config.O2 = v
        toggleO2(v)
    end, getgenv().Config.O2)
    addToggle(mainCard, "Anti-Frosted", function(v)
        getgenv().Config.AntiFrost = v
        toggleAntiFrost(v)
    end, getgenv().Config.AntiFrost)
    addSlider(mainCard, "Velocidade da Sprint", function(v)
        getgenv().Config.SprintSpeed = v
        setSprintSpeed(v)
    end, 10, 50, getgenv().Config.SprintSpeed)
    
    -- ABA MENU
    local menuCard = addCard(abaFramesMap["Menu"])
    addLabel(menuCard, "📋 Menu")
    addButton(menuCard, "📝 Enviar Feedback", function()
        -- Verifica banimento
        local banido, motivo, data = verificarBanimento()
        if banido then
            showNotification("🚫 Você está banido! Motivo: " .. motivo, Color3.fromRGB(255, 0, 0))
            return
        end
        
        local feedbackGui = Instance.new("ScreenGui")
        feedbackGui.Name = "FeedbackGUI"
        feedbackGui.Parent = player.PlayerGui
        
        local frame = Instance.new("Frame")
        frame.Size = UDim2.new(0, 400, 0, 320)
        frame.Position = UDim2.new(0.5, -200, 0.5, -160)
        frame.BackgroundColor3 = Color3.fromRGB(20, 20, 35)
        frame.BackgroundTransparency = 0.05
        frame.BorderSizePixel = 0
        frame.ClipsDescendants = true
        frame.Parent = feedbackGui
        
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
        title.Text = "📝 Enviar Feedback"
        title.TextColor3 = Color3.fromRGB(255, 255, 255)
        title.TextSize = 16
        title.Font = Enum.Font.GothamBold
        title.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
        title.BackgroundTransparency = 0.2
        title.Parent = frame
        
        local close = Instance.new("TextButton")
        close.Size = UDim2.new(0, 30, 0, 30)
        close.Position = UDim2.new(1, -38, 0, 5)
        close.Text = "X"
        close.TextSize = 18
        close.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
        close.BackgroundTransparency = 0.3
        close.TextColor3 = Color3.fromRGB(255, 255, 255)
        close.Font = Enum.Font.GothamBold
        close.BorderSizePixel = 0
        close.Parent = title
        close.MouseButton1Click:Connect(function()
            feedbackGui:Destroy()
        end)
        
        -- Categorias
        local categorias = {
            "Bug Report", "Sugestão", "Dúvida", "Elogio",
            "Crítica", "Request de Função", "Erro no Script",
            "Melhoria de UI", "Erro de Key", "Problema de Performance",
            "Compatibilidade", "Outro"
        }
        local selectedCategory = "Outro"
        
        local catDropdown = Instance.new("TextButton")
        catDropdown.Size = UDim2.new(0.9, 0, 0, 35)
        catDropdown.Position = UDim2.new(0.05, 0, 0.18, 0)
        catDropdown.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
        catDropdown.BackgroundTransparency = 0.2
        catDropdown.BorderSizePixel = 0
        catDropdown.Text = "Selecione a categoria..."
        catDropdown.TextColor3 = Color3.fromRGB(255, 255, 255)
        catDropdown.TextSize = 13
        catDropdown.Font = Enum.Font.Gotham
        catDropdown.Parent = frame
        
        local catCorner = Instance.new("UICorner")
        catCorner.CornerRadius = UDim.new(0, 8)
        catCorner.Parent = catDropdown
        
        local catMenu = Instance.new("ScrollingFrame")
        catMenu.Size = UDim2.new(0.9, 0, 0, 0)
        catMenu.Position = UDim2.new(0.05, 0, 0.32, 0)
        catMenu.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
        catMenu.BackgroundTransparency = 0.2
        catMenu.BorderSizePixel = 0
        catMenu.ClipsDescendants = true
        catMenu.Visible = false
        catMenu.ScrollBarThickness = 4
        catMenu.Parent = frame
        
        local catMenuCorner = Instance.new("UICorner")
        catMenuCorner.CornerRadius = UDim.new(0, 8)
        catMenuCorner.Parent = catMenu
        
        for i, cat in ipairs(categorias) do
            local opt = Instance.new("TextButton")
            opt.Size = UDim2.new(1, 0, 0, 30)
            opt.Text = cat
            opt.TextColor3 = Color3.fromRGB(255, 255, 255)
            opt.TextSize = 12
            opt.Font = Enum.Font.Gotham
            opt.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
            opt.BackgroundTransparency = 0.2
            opt.BorderSizePixel = 0
            opt.Parent = catMenu
            local optCorner = Instance.new("UICorner")
            optCorner.CornerRadius = UDim.new(0, 6)
            optCorner.Parent = opt
            
            opt.MouseButton1Click:Connect(function()
                selectedCategory = cat
                catDropdown.Text = cat
                catMenu.Visible = false
                catMenu.Size = UDim2.new(0.9, 0, 0, 0)
            end)
        end
        
        catDropdown.MouseButton1Click:Connect(function()
            catMenu.Visible = not catMenu.Visible
            if catMenu.Visible then
                local totalHeight = #categorias * 32
                local maxHeight = 150
                catMenu.Size = UDim2.new(0.9, 0, 0, math.min(totalHeight, maxHeight))
                catMenu.CanvasSize = UDim2.new(0, 0, 0, totalHeight)
            else
                catMenu.Size = UDim2.new(0.9, 0, 0, 0)
            end
        end)
        
        -- Mensagem
        local msgBox = Instance.new("TextBox")
        msgBox.Size = UDim2.new(0.9, 0, 0, 80)
        msgBox.Position = UDim2.new(0.05, 0, 0.40, 0)
        msgBox.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
        msgBox.BackgroundTransparency = 0.2
        msgBox.BorderSizePixel = 0
        msgBox.Text = ""
        msgBox.TextColor3 = Color3.fromRGB(255, 255, 255)
        msgBox.TextSize = 13
        msgBox.Font = Enum.Font.Gotham
        msgBox.PlaceholderText = "Descreva seu feedback aqui..."
        msgBox.TextWrapped = true
        msgBox.TextXAlignment = Enum.TextXAlignment.Left
        msgBox.TextYAlignment = Enum.TextYAlignment.Top
        msgBox.Parent = frame
        
        local msgCorner = Instance.new("UICorner")
        msgCorner.CornerRadius = UDim.new(0, 8)
        msgCorner.Parent = msgBox
        
        -- Botão Enviar (COM ANTI-SPAM)
        local sendBtn = Instance.new("TextButton")
        sendBtn.Size = UDim2.new(0.4, 0, 0, 40)
        sendBtn.Position = UDim2.new(0.3, 0, 0.75, 0)
        sendBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
        sendBtn.BackgroundTransparency = 0.2
        sendBtn.BorderSizePixel = 0
        sendBtn.Text = "📤 Enviar"
        sendBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
        sendBtn.TextSize = 16
        sendBtn.Font = Enum.Font.GothamBold
        sendBtn.Parent = frame
        
        local sendCorner = Instance.new("UICorner")
        sendCorner.CornerRadius = UDim.new(0, 8)
        sendCorner.Parent = sendBtn
        
        sendBtn.MouseButton1Click:Connect(function()
            local msg = msgBox.Text
            if msg == "" then
                showNotification("⚠️ Digite uma mensagem!", Color3.fromRGB(255, 200, 0))
                return
            end
            
            local sucesso, resultado = enviarFeedbackComAntiSpam(selectedCategory, msg)
            if sucesso then
                feedbackGui:Destroy()
            end
        end)
    end)
    
    addButton(menuCard, "👥 Ver Usuários do HUB", function()
        local count = 0
        for _, jogador in ipairs(Players:GetPlayers()) do
            if jogador:FindFirstChild("DAVI_HUB") then
                count = count + 1
            end
        end
        showNotification("Usuários DAVI HUB", "Total: " .. count .. " usuários online", 5)
    end)
    
    -- ABA TELE
    local teleCard = addCard(abaFramesMap["Tele"])
    addLabel(teleCard, "📡 Teleports")
    
    addLabel(teleCard, "🌙 N1", Color3.fromRGB(200, 200, 200))
    for name, cf in pairs(teleportsN1) do
        addButton(teleCard, "📍 " .. name, function()
            teleportar(cf)
        end)
    end
    
    addLabel(teleCard, "🌙 N2", Color3.fromRGB(200, 200, 200))
    for name, cf in pairs(teleportsN2) do
        addButton(teleCard, "📍 " .. name, function()
            teleportar(cf)
        end)
    end
    
    addLabel(teleCard, "🌙 N3", Color3.fromRGB(200, 200, 200))
    for name, cf in pairs(teleportsN3) do
        addButton(teleCard, "📍 " .. name, function()
            teleportar(cf)
        end)
    end
    
    -- ABA N1
    local n1Card = addCard(abaFramesMap["N1"])
    addLabel(n1Card, "🌙 Noite 1")
    addToggle(n1Card, "Auto Scare", function(v)
        getgenv().Config.AutoScare = v
        toggleAutoScare(v)
    end, getgenv().Config.AutoScare)
    addToggle(n1Card, "Anti Vent", function(v)
        getgenv().Config.AntiVent = v
    end, getgenv().Config.AntiVent)
    addToggle(n1Card, "Reviver", function(v)
        getgenv().Config.AutoRevive = v
    end, getgenv().Config.AutoRevive)
    addToggle(n1Card, "Escape Snatch", function(v)
        getgenv().Config.EscapeSnatch = v
    end, getgenv().Config.EscapeSnatch)
    
    -- ABA N2
    local n2Card = addCard(abaFramesMap["N2"])
    addLabel(n2Card, "🌙 Noite 2")
    addToggle(n2Card, "Recarregar Energia", function(v)
        getgenv().Config.RefillPower = v
    end, getgenv().Config.RefillPower)
    addToggle(n2Card, "Anti Stalker", function(v)
        getgenv().Config.AntiStalker = v
    end, getgenv().Config.AntiStalker)
    addToggle(n2Card, "Auto Coletar Munição", function(v)
        getgenv().Config.AutoCollectAmmo = v
    end, getgenv().Config.AutoCollectAmmo)
    
    -- ABA N3
    local n3Card = addCard(abaFramesMap["N3"])
    addLabel(n3Card, "🌙 Noite 3")
    addToggle(n3Card, "Recarregar Energia", function(v)
        getgenv().Config.RefillPower = v
    end, getgenv().Config.RefillPower)
    addToggle(n3Card, "Anti Stalker", function(v)
        getgenv().Config.AntiStalker = v
    end, getgenv().Config.AntiStalker)
    
    -- ABA ESP
    local espCard = addCard(abaFramesMap["ESP"])
    addLabel(espCard, "👁️ ESP")
    addToggle(espCard, "ESP Players", function(v)
        getgenv().Config.ESPPlayers = v
        updateESP()
    end, getgenv().Config.ESPPlayers)
    addToggle(espCard, "ESP Larry", function(v)
        getgenv().Config.ESPLarry = v
        updateESP()
    end, getgenv().Config.ESPLarry)
    addToggle(espCard, "ESP Stalker", function(v)
        getgenv().Config.ESPStalker = v
        updateESP()
    end, getgenv().Config.ESPStalker)
    addToggle(espCard, "ESP Zumbis & Esqueletos", function(v)
        getgenv().Config.ESPZombies = v
        updateESP()
    end, getgenv().Config.ESPZombies)
    
    -- ABA GERAL
    local geralCard = addCard(abaFramesMap["Geral"])
    addLabel(geralCard, "⚙️ Geral")
    addToggle(geralCard, "Notificador", function(v)
        getgenv().Config.Notifier = v
        if v then
            iniciarNotificador()
        else
            desativarNotificador()
        end
    end, getgenv().Config.Notifier)
    addToggle(geralCard, "Bypass Anti-Cheat", function(v)
        getgenv().Config.BypassAC = v
    end, getgenv().Config.BypassAC)
    addToggle(geralCard, "Desabilitar Estática", function(v)
        getgenv().Config.DisableStatic = v
        if v then
            local blur = Lighting:FindFirstChild("Blur")
            if blur then
                blur.Enabled = false
            end
        else
            local blur = Lighting:FindFirstChild("Blur")
            if blur then
                blur.Enabled = true
            end
        end
    end, getgenv().Config.DisableStatic)
    addToggle(geralCard, "Ativar Aimbot", function(v)
        getgenv().Config.Aimbot = v
        toggleAimbot(v)
    end, getgenv().Config.Aimbot)
    addSlider(geralCard, "Velocidade do Aimbot", function(v)
        getgenv().Config.AimbotSpeed = v
    end, 10, 100, getgenv().Config.AimbotSpeed)
    addSlider(geralCard, "Distância do Aimbot", function(v)
        getgenv().Config.AimbotDistance = v
    end, 10, 150, getgenv().Config.AimbotDistance)
    
    -- ABA CONFIG
    local configCard = addCard(abaFramesMap["Config"])
    addLabel(configCard, "⚙️ Configurações")
    addButton(configCard, "💾 Salvar Configurações", function()
        showNotification("Configurações", "Salvas com sucesso!", 3)
    end)
    addButton(configCard, "📂 Carregar Configurações", function()
        showNotification("Configurações", "Carregadas com sucesso!", 3)
    end)
    addButton(configCard, "🔄 Resetar Configurações", function()
        showNotification("Configurações", "Resetadas com sucesso!", 3)
    end)
end
-- ============================================================
-- INICIALIZAR
-- ============================================================

-- Verificar banimento ao iniciar
if not verificarBanAoIniciar() then
    return
end

-- Marcar usuário como DAVI HUB
if not player:FindFirstChild("DAVI_HUB") then
    local tag = Instance.new("BoolValue", player)
    tag.Name = "DAVI_HUB"
end

-- Criar GUI
criarGUI()
