
local key = "DAVI2024"
local keyGui = Instance.new("ScreenGui")
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
local err = Instance.new("TextLabel")
err.Size = UDim2.new(0.8, 0, 0, 25)
err.Position = UDim2.new(0.1, 0, 0.85, 0)
err.Text = ""
err.TextColor3 = Color3.fromRGB(255, 100, 100)
err.BackgroundTransparency = 1
err.TextSize = 11
err.Parent = frame
btn.MouseButton1Click:Connect(function()
    if box.Text == key then
        keyGui:Destroy()
        carregarHub()
    else
        box.Text = ""
        err.Text = "❌ Key inválida!"
    end
end)

function carregarHub()
local plr = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui")
gui.Parent = plr:WaitForChild("PlayerGui")
local f = Instance.new("Frame")
f.Size = UDim2.new(0, 380, 0, 520)
f.Position = UDim2.new(0.5, -190, 0.3, 0)
f.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
f.Parent = gui
local t = Instance.new("Frame")
t.Size = UDim2.new(1, 0, 0, 35)
t.BackgroundColor3 = Color3.fromRGB(255, 140, 0)
t.Parent = f
local l = Instance.new("TextLabel")
l.Size = UDim2.new(1, -90, 1, 0)
l.Position = UDim2.new(0, 10, 0, 0)
l.Text = "DAVI HUB - Noite 2"
l.TextColor3 = Color3.fromRGB(255, 255, 255)
l.BackgroundTransparency = 1
l.Font = Enum.Font.GothamBold
l.TextSize = 16
l.Parent = t
local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 30, 0, 30)
minBtn.Position = UDim2.new(1, -68, 0, 2.5)
minBtn.Text = "−"
minBtn.TextSize = 20
minBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.Parent = t
local c = Instance.new("TextButton")
c.Size = UDim2.new(0, 30, 0, 30)
c.Position = UDim2.new(1, -35, 0, 2.5)
c.Text = "X"
c.TextSize = 18
c.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
c.TextColor3 = Color3.fromRGB(255, 255, 255)
c.Parent = t
c.MouseButton1Click:Connect(function() gui:Destroy() end)
local drag = false
local dragStart, startPos
t.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then
        drag = true
        dragStart = i.Position
        startPos = f.Position
    end
end)
t.InputEnded:Connect(function() drag = false end)
game:GetService("UserInputService").InputChanged:Connect(function(i)
    if drag and i.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = i.Position - dragStart
        f.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
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
        f.Size = UDim2.new(0, 380, 0, 35)
        minBtn.Text = "+"
    else
        f.Size = UDim2.new(0, 380, 0, 520)
        if scrollArea then scrollArea.Visible = true end
        minBtn.Text = "−"
    end
end)
local s = Instance.new("ScrollingFrame")
s.Size = UDim2.new(1, 0, 1, -35)
s.Position = UDim2.new(0, 0, 0, 35)
s.BackgroundTransparency = 1
s.ScrollBarThickness = 6
s.Parent = f
local la = Instance.new("UIListLayout")
la.Padding = UDim.new(0, 8)
la.Parent = s
function btn(txt, cb)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.9, 0, 0, 40)
    b.Text = txt
    b.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.Parent = s
    b.MouseButton1Click:Connect(cb)
    b.MouseEnter:Connect(function() b.BackgroundColor3 = Color3.fromRGB(255, 140, 0) end)
    b.MouseLeave:Connect(function() b.BackgroundColor3 = Color3.fromRGB(45, 45, 60) end)
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
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0.25, 0, 0.8, 0)
    b.Position = UDim2.new(0.72, 0, 0.1, 0)
    b.Text = def and "On" or "Off"
    b.BackgroundColor3 = def and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
    b.TextColor3 = def and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
    b.Parent = fr
    local st = def
    b.MouseButton1Click:Connect(function()
        st = not st
        b.Text = st and "On" or "Off"
        b.BackgroundColor3 = st and Color3.fromRGB(255, 140, 0) or Color3.fromRGB(80, 80, 100)
        b.TextColor3 = st and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(255, 100, 100)
        cb(st)
    end)
end
-- Teleports (incluindo Safe Spot)
btn("Rádio 1", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-306.4, 82.4, -17.3) end)
btn("Rádio 2", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-246.5, 82.4, -16.5) end)
btn("Delivery", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-283.3, 82.4, 13.0) end)
btn("Torre Rádio", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-418.3, 82.4, -16.2) end)
btn("Boiler", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-291.7, 82.4, 102.9) end)
btn("Power Stor", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-333.8, 82.4, 96.5) end)
btn("Power", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-269.1, 82.4, 111.2) end)
btn("Salão Princ", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-294.1, 82.4, 8.4) end)
btn("Outside", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-157.5, 82.4, 61.4) end)
btn("Câmeras", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-245.9, 82.4, -15.7) end)
btn("Safe Spot", function() plr.Character.HumanoidRootPart.CFrame = CFrame.new(-29.2, 3.0, -62.8) end)
-- Stamina
local staminaLoop = nil
tog("Stamina Inf", false, function(s)
    if staminaLoop then staminaLoop:Disconnect() end
    if s then
        staminaLoop = game:GetService("RunService").RenderStepped:Connect(function()
            local ch = plr.Character
            if ch and ch.Sprint then
                if ch.Sprint.Overdrive then ch.Sprint.Overdrive.Value = 1e9 end
                if ch.Sprint.Stamina then ch.Sprint.Stamina.Value = 100 end
            end
        end)
    end
end)
-- ESP Players
local espObjs = {}
tog("ESP Players", false, function(s)
    for _, h in pairs(espObjs) do if h then h:Destroy() end end
    espObjs = {}
    if s then
        for _, p in pairs(game.Players:GetPlayers()) do
            if p ~= plr and p.Character then
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
end)
-- Fullbright
local origLight = {}
tog("Fullbright", false, function(s)
    local li = game:GetService("Lighting")
    if s then
        origLight = {Ambient = li.Ambient, OutdoorAmbient = li.OutdoorAmbient, Brightness = li.Brightness, GlobalShadows = li.GlobalShadows}
        li.Ambient = Color3.fromRGB(255, 255, 255)
        li.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
        li.Brightness = 2
        li.GlobalShadows = false
    else
        li.Ambient = origLight.Ambient or Color3.fromRGB(128, 128, 128)
        li.OutdoorAmbient = origLight.OutdoorAmbient or Color3.fromRGB(128, 128, 128)
        li.Brightness = origLight.Brightness or 1
        li.GlobalShadows = origLight.GlobalShadows or true
    end
end)
-- ESP Mutant e Stalker (persistente)
local function addNPCESP(npc, cor)
    if not npc or npc:FindFirstChild("ESP_NPC") then return end
    local h = Instance.new("Highlight")
    h.Name = "ESP_NPC"
    h.FillColor = cor or Color3.fromRGB(255, 50, 50)
    h.OutlineColor = Color3.fromRGB(255, 150, 0)
    h.FillTransparency = 0.3
    h.Adornee = npc
    h.Parent = npc
    print("✅ ESP aplicado a:", npc.Name)
end
local function refreshNPC()
    local mutant = workspace:FindFirstChild("Mutant") or workspace:FindFirstChild("Larry")
    if mutant and not mutant:FindFirstChild("ESP_NPC") then
        addNPCESP(mutant, Color3.fromRGB(255, 0, 0))
    end
    local stalker = workspace:FindFirstChild("Stalker")
    if stalker and not stalker:FindFirstChild("ESP_NPC") then
        addNPCESP(stalker, Color3.fromRGB(255, 100, 0))
    end
    for _, obj in pairs(workspace:GetDescendants()) do
        if (obj.Name == "Mutant" or obj.Name == "Larry") and not obj:FindFirstChild("ESP_NPC") then
            addNPCESP(obj, Color3.fromRGB(255, 0, 0))
        elseif obj.Name == "Stalker" and not obj:FindFirstChild("ESP_NPC") then
            addNPCESP(obj, Color3.fromRGB(255, 100, 0))
        end
    end
end
game:GetService("RunService").Stepped:Connect(function()
    if tick() % 2 < 0.1 then refreshNPC() end
end)
workspace.ChildAdded:Connect(function(child)
    if child.Name == "Mutant" or child.Name == "Larry" or child.Name == "Stalker" then
        task.wait(0.5)
        refreshNPC()
    end
end)
-- Auto Scare corrigido (distância 70, com logs)
local scareActive = false
local lastScare = 0
local DISTANCIA = 70
local COOLDOWN = 4.5
local DEBUG = true
local janelasAS = {
    {nome="Jan1", posJan=Vector3.new(-292.1,82.4,-38.8), posBot=Vector3.new(-288.5,82.4,-39.6)},
    {nome="Jan2", posJan=Vector3.new(-321.9,82.4,-37.4), posBot=Vector3.new(-318.1,82.4,-40.1)},
    {nome="Jan3", posJan=Vector3.new(-311.1,82.4,38.7), posBot=Vector3.new(-313.0,82.6,35.4)},
    {nome="Jan4", posJan=Vector3.new(-282.0,82.4,-111.9), posBot=Vector3.new(-278.4,82.4,-113.3)},
    {nome="Jan5", posJan=Vector3.new(-309.6,82.4,-111.9), posBot=Vector3.new(-307.9,82.4,-113.4)},
}
local function ativarLuz(jan)
    local ch = plr.Character
    if not ch or not ch:FindFirstChild("HumanoidRootPart") then
        if DEBUG then print("⚠️ Auto Scare: personagem não disponível") end
        return
    end
    ch.HumanoidRootPart.CFrame = CFrame.new(jan.posBot)
    if DEBUG then print("🔁 Auto Scare: teleportado para botão de " .. jan.nome) end
    task.wait(0.3)
    local clicou = false
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("ClickDetector") then
            local parentPos = obj.Parent and obj.Parent.Position
            if parentPos and (parentPos - jan.posBot).Magnitude < 10 then
                fireclickdetector(obj)
                clicou = true
                if DEBUG then print("💡 Auto Scare: ClickDetector acionado em " .. jan.nome) end
                break
            end
        end
    end
    if not clicou and DEBUG then
        print("⚠️ Auto Scare: nenhum ClickDetector encontrado para " .. jan.nome)
    end
    local fl = Instance.new("Frame")
    fl.Size = UDim2.new(1,0,1,0)
    fl.BackgroundColor3 = Color3.fromRGB(255,255,255)
    fl.BackgroundTransparency = 0.5
    fl.Parent = plr.PlayerGui
    task.wait(0.2)
    fl:Destroy()
end
task.spawn(function()
    while true do
        task.wait(1)
        if not scareActive then continue end
        local mutant = nil
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj.Name == "Mutant" or obj.Name == "Larry" then
                mutant = obj
                break
            end
        end
        if not mutant then
            if DEBUG then print("🔍 Auto Scare: Mutant não encontrado") end
            continue
        end
        local mpos = mutant:FindFirstChild("HumanoidRootPart") and mutant.HumanoidRootPart.Position or mutant.Position
        local alvo, menor = nil, DISTANCIA + 1
        for _, j in pairs(janelasAS) do
            local d = (mpos - j.posJan).Magnitude
            if d < menor then menor = d; alvo = j end
        end
        if DEBUG then
            print("📏 Auto Scare: distância do Mutante = " .. math.floor(menor))
        end
        if alvo and menor <= DISTANCIA and tick() - lastScare >= COOLDOWN then
            lastScare = tick()
            if DEBUG then print("💥 Auto Scare ACIONADO! Janela: " .. alvo.nome) end
            ativarLuz(alvo)
        end
    end
end)
tog("Auto Scare", false, function(s)
    scareActive = s
    if DEBUG then print("Auto Scare " .. (s and "ativado" or "desativado")) end
end)
-- Ajustar scroll
task.wait(0.1)
local totalH = 0
for _, child in pairs(s:GetChildren()) do
    if child:IsA("TextButton") or child:IsA("Frame") then totalH = totalH + 48 end
end
s.CanvasSize = UDim2.new(0, 0, 0, totalH + 30)
print("✅ DAVI HUB Noite 2 carregado (safe spot adicionado)")
end
