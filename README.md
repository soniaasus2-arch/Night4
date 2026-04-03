local plr=game.Players.LocalPlayer
local gui=Instance.new("ScreenGui")
gui.Parent=plr:WaitForChild("PlayerGui")
local f=Instance.new("Frame")
f.Size=UDim2.new(0,420,0,540)
f.Position=UDim2.new(0.5,-210,0.3,0)
f.BackgroundColor3=Color3.fromRGB(20,20,25)
f.Parent=gui
local t=Instance.new("Frame")
t.Size=UDim2.new(1,0,0,35)
t.BackgroundColor3=Color3.fromRGB(255,140,0)
t.Parent=f
local l=Instance.new("TextLabel")
l.Size=UDim2.new(1,-90,1,0)
l.Position=UDim2.new(0,10,0,0)
l.Text="DAVI HUB - Noite 2"
l.TextColor3=Color3.fromRGB(255,255,255)
l.BackgroundTransparency=1
l.Font=Enum.Font.GothamBold
l.TextSize=16
l.Parent=t
local min=Instance.new("TextButton")
min.Size=UDim2.new(0,30,0,30)
min.Position=UDim2.new(1,-68,0,2.5)
min.Text="−"
min.TextSize=20
min.BackgroundColor3=Color3.fromRGB(80,80,100)
min.TextColor3=Color3.fromRGB(255,255,255)
min.Parent=t
local close=Instance.new("TextButton")
close.Size=UDim2.new(0,30,0,30)
close.Position=UDim2.new(1,-35,0,2.5)
close.Text="X"
close.TextSize=18
close.BackgroundColor3=Color3.fromRGB(200,60,60)
close.TextColor3=Color3.fromRGB(255,255,255)
close.Parent=t
close.MouseButton1Click:Connect(function()gui:Destroy()end)
local drag=false
local ds,sp
t.InputBegan:Connect(function(i)
if i.UserInputType==Enum.UserInputType.MouseButton1 then drag=true ds=i.Position sp=f.Position end
end)
t.InputEnded:Connect(function()drag=false end)
game:GetService("UserInputService").InputChanged:Connect(function(i)
if drag and i.UserInputType==Enum.UserInputType.MouseMovement then
local d=i.Position-ds
f.Position=UDim2.new(sp.X.Scale,sp.X.Offset+d.X,sp.Y.Scale,sp.Y.Offset+d.Y)
end
end)
local mini=false
local sa=nil
local function getSA()
for _,c in pairs(f:GetChildren())do if c:IsA("ScrollingFrame")then return c end end
return nil
end
min.MouseButton1Click:Connect(function()
mini=not mini
sa=sa or getSA()
if mini then
if sa then sa.Visible=false end
game:GetService("TweenService"):Create(f,TweenInfo.new(0.2),{Size=UDim2.new(0,420,0,35)}):Play()
min.Text="+"
else
game:GetService("TweenService"):Create(f,TweenInfo.new(0.2),{Size=UDim2.new(0,420,0,540)}):Play()
task.wait(0.2)
if sa then sa.Visible=true end
min.Text="−"
end
end)
local s=Instance.new("ScrollingFrame")
s.Size=UDim2.new(1,0,1,-35)
s.Position=UDim2.new(0,0,0,35)
s.BackgroundTransparency=1
s.ScrollBarThickness=6
s.Parent=f
local la=Instance.new("UIListLayout")
la.Padding=UDim.new(0,8)
la.Parent=s
function bt(txt,cb)
local b=Instance.new("TextButton")
b.Size=UDim2.new(0.9,0,0,40)
b.Text=txt
b.BackgroundColor3=Color3.fromRGB(45,45,60)
b.TextColor3=Color3.fromRGB(255,255,255)
b.Parent=s
b.MouseButton1Click:Connect(cb)
b.MouseEnter:Connect(function()b.BackgroundColor3=Color3.fromRGB(255,140,0)end)
b.MouseLeave:Connect(function()b.BackgroundColor3=Color3.fromRGB(45,45,60)end)
end
function tg(txt,def,cb)
local fr=Instance.new("Frame")
fr.Size=UDim2.new(0.9,0,0,40)
fr.BackgroundTransparency=1
fr.Parent=s
local lb=Instance.new("TextLabel")
lb.Size=UDim2.new(0.65,0,1,0)
lb.Text=txt
lb.TextColor3=Color3.fromRGB(220,220,220)
lb.BackgroundTransparency=1
lb.Parent=fr
local b=Instance.new("TextButton")
b.Size=UDim2.new(0.25,0,0.8,0)
b.Position=UDim2.new(0.72,0,0.1,0)
b.Text=def and"On"or"Off"
b.BackgroundColor3=def and Color3.fromRGB(255,140,0)or Color3.fromRGB(80,80,100)
b.TextColor3=def and Color3.fromRGB(255,255,255)or Color3.fromRGB(255,100,100)
b.Parent=fr
local st=def
b.MouseButton1Click:Connect(function()
st=not st
b.Text=st and"On"or"Off"
b.BackgroundColor3=st and Color3.fromRGB(255,140,0)or Color3.fromRGB(80,80,100)
b.TextColor3=st and Color3.fromRGB(255,255,255)or Color3.fromRGB(255,100,100)
cb(st)
end)
end
-- TELEPORTS
bt("Rádio 1",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-306.4,82.4,-17.3)end)
bt("Rádio 2",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-246.5,82.4,-16.5)end)
bt("Delivery Screen",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-283.3,82.4,13.0)end)
bt("Torre Rádio",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-418.3,82.4,-16.2)end)
bt("Boiler",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-291.7,82.4,102.9)end)
bt("Power Storage",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-333.8,82.4,96.5)end)
bt("Power",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-269.1,82.4,111.2)end)
bt("Salão Principal",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-294.1,82.4,8.4)end)
bt("Outside",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-157.5,82.4,61.4)end)
bt("Câmeras",function()plr.Character.HumanoidRootPart.CFrame=CFrame.new(-245.9,82.4,-15.7)end)
-- STAMINA
local staminaLoop=nil
tg("⚡ Infinite Stamina",false,function(s)
if staminaLoop then staminaLoop:Disconnect()end
if s then
staminaLoop=game:GetService("RunService").RenderStepped:Connect(function()
local ch=plr.Character
if ch and ch.Sprint then
if ch.Sprint.Overdrive then ch.Sprint.Overdrive.Value=1e9 end
if ch.Sprint.Stamina then ch.Sprint.Stamina.Value=100 end
end
end)
end
end)
-- ESP PLAYERS
local espObjs={}
tg("👁️ ESP Players",false,function(s)
for _,h in pairs(espObjs)do if h then h:Destroy()end end
espObjs={}
if s then
for _,p in pairs(game.Players:GetPlayers())do
if p~=plr and p.Character then
local h=Instance.new("Highlight")
h.FillColor=Color3.fromRGB(255,0,0)
h.OutlineColor=Color3.fromRGB(200,0,0)
h.FillTransparency=0.5
h.Adornee=p.Character
h.Parent=p.Character
espObjs[p]=h
end
end
end
end)
-- FULLBRIGHT
local origLight={}
tg("💡 Fullbright",false,function(s)
local lighting=game:GetService("Lighting")
if s then
origLight={Ambient=lighting.Ambient,OutdoorAmbient=lighting.OutdoorAmbient,Brightness=lighting.Brightness,GlobalShadows=lighting.GlobalShadows,FogEnd=lighting.FogEnd}
lighting.Ambient=Color3.fromRGB(255,255,255)
lighting.OutdoorAmbient=Color3.fromRGB(255,255,255)
lighting.Brightness=2
lighting.GlobalShadows=false
lighting.FogEnd=1e6
else
lighting.Ambient=origLight.Ambient or Color3.fromRGB(128,128,128)
lighting.OutdoorAmbient=origLight.OutdoorAmbient or Color3.fromRGB(128,128,128)
lighting.Brightness=origLight.Brightness or 1
lighting.GlobalShadows=origLight.GlobalShadows or true
lighting.FogEnd=origLight.FogEnd or 1000
end
end)
-- INVENCIBILIDADE
local invConn=nil
local invState=false
tg("🛡️ Invencível",false,function(s)
invState=s
if invConn then invConn:Disconnect()end
local ch=plr.Character
if not ch then return end
local hum=ch:FindFirstChild("Humanoid")
if not hum then return end
if s then
hum.Health=hum.MaxHealth
invConn=hum:GetPropertyChangedSignal("Health"):Connect(function()
if invState and hum.Health<hum.MaxHealth then hum.Health=hum.MaxHealth end
end)
local old=hum.TakeDamage
hum.TakeDamage=function(self,amt) if invState then return end return old(self,amt) end
else
if invConn then invConn:Disconnect()end
end
end)
-- ESP MUTANT/STALKER
local function addESP(npc,col)
if not npc or npc:FindFirstChild("ESP_NPC")then return end
local h=Instance.new("Highlight")
h.Name="ESP_NPC"
h.FillColor=col or Color3.fromRGB(255,50,50)
h.OutlineColor=Color3.fromRGB(255,150,0)
h.FillTransparency=0.3
h.Adornee=npc
h.Parent=npc
end
local function refreshNPC()
local m=workspace:FindFirstChild("Mutant")or workspace:FindFirstChild("Larry")
if m and not m:FindFirstChild("ESP_NPC")then addESP(m,Color3.fromRGB(255,0,0))end
local st=workspace:FindFirstChild("Stalker")
if st and not st:FindFirstChild("ESP_NPC")then addESP(st,Color3.fromRGB(255,100,0))end
end
game:GetService("RunService").Heartbeat:Connect(function()
if tick()%3<0.1 then refreshNPC()end
end)
workspace.ChildAdded:Connect(function(c)
if c.Name=="Mutant"or c.Name=="Larry"or c.Name=="Stalker"then task.wait(0.5)refreshNPC()end
end)
-- AUTO SCARE
local scareActive=false
local scareTask=nil
local lastScare=0
local DIST=40
local COOLDOWN=8
local janelas={
{posJan=Vector3.new(-292.1,82.4,-38.8),posBot=Vector3.new(-288.5,82.4,-39.6)},
{posJan=Vector3.new(-321.9,82.4,-37.4),posBot=Vector3.new(-318.1,82.4,-40.1)},
{posJan=Vector3.new(-311.1,82.4,38.7),posBot=Vector3.new(-313.0,82.6,35.4)},
{posJan=Vector3.new(-282.0,82.4,-111.9),posBot=Vector3.new(-278.4,82.4,-113.3)},
{posJan=Vector3.new(-309.6,82.4,-111.9),posBot=Vector3.new(-307.9,82.4,-113.4)}}
local function ativarLuz(jan)
local ch=plr.Character
if ch and ch.HumanoidRootPart then ch.HumanoidRootPart.CFrame=CFrame.new(jan.posBot)end
task.wait(0.2)
for _,obj in pairs(workspace:GetDescendants())do
if obj:IsA("ClickDetector")and(obj.Parent.Position-jan.posBot).Magnitude<8 then fireclickdetector(obj)break end
end
local fl=Instance.new("Frame")
fl.Size=UDim2.new(1,0,1,0)
fl.BackgroundColor3=Color3.fromRGB(255,255,255)
fl.BackgroundTransparency=0.5
fl.Parent=plr.PlayerGui
task.wait(0.2)
fl:Destroy()
end
local function scareLoop()
while scareActive do
task.wait(1)
local mutant=nil
for _,obj in pairs(workspace:GetDescendants())do if obj.Name=="Mutant"or obj.Name=="Larry"then mutant=obj break end end
if mutant then
local mpos=mutant:FindFirstChild("HumanoidRootPart")and mutant.HumanoidRootPart.Position or mutant.Position
local alvo,menor=nil,DIST+1
for _,j in pairs(janelas)do
local d=(mpos-j.posJan).Magnitude
if d<menor then menor=d alvo=j end
end
if alvo and menor<=DIST and tick()-lastScare>=COOLDOWN then
lastScare=tick()
ativarLuz(alvo)
end
end
end
end
tg("🤖 Auto Scare",false,function(s)
scareActive=s
if scareTask then coroutine.close(scareTask)scareTask=nil end
if s then scareTask=coroutine.create(scareLoop)coroutine.resume(scareTask)end
end)
-- AUTO REPARO
local repairActive=false
local repairTask=nil
local function repairLoop()
while repairActive do
task.wait(10)
local gen=nil
for _,obj in pairs(workspace:GetDescendants())do if obj.Name:lower():find("generator")then gen=obj break end end
if gen then
local pos=gen.Position or(gen:FindFirstChildWhichIsA("BasePart")and gen:FindFirstChildWhichIsA("BasePart").Position)
if pos then
local ch=plr.Character
if ch and ch.HumanoidRootPart then ch.HumanoidRootPart.CFrame=CFrame.new(pos+Vector3.new(0,3,0))end
task.wait(0.5)
for _,det in pairs(gen:GetDescendants())do if det:IsA("ClickDetector")then fireclickdetector(det)break end end
end
end
task.wait(2)
local fus=nil
for _,obj in pairs(workspace:GetDescendants())do if obj.Name:lower():find("fuse")or obj.Name:lower():find("caixa")then fus=obj break end end
if fus then
local pos=fus.Position or(fus:FindFirstChildWhichIsA("BasePart")and fus:FindFirstChildWhichIsA("BasePart").Position)
if pos then
local ch=plr.Character
if ch and ch.HumanoidRootPart then ch.HumanoidRootPart.CFrame=CFrame.new(pos+Vector3.new(0,2,0))end
task.wait(0.5)
for _,det in pairs(fus:GetDescendants())do if det:IsA("ClickDetector")then fireclickdetector(det)break end end
end
end
end
end
tg("🔧 Auto Reparo",false,function(s)
repairActive=s
if repairTask then coroutine.close(repairTask)repairTask=nil end
if s then repairTask=coroutine.create(repairLoop)coroutine.resume(repairTask)end
end)
-- AJUSTE SCROLL
task.wait(0.1)
local totalH=0
for _,c in pairs(s:GetChildren())do if c:IsA("TextButton")or c:IsA("Frame")then totalH=totalH+48 end end
s.CanvasSize=UDim2.new(0,0,0,totalH+30)
print("✅ DAVI HUB - Noite 2 completo carregado!")

