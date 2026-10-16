local Players = game:GetService("Players")
local client = Players.LocalPlayer

local FIGURE_RADIUS = 15
local LINE_WIDTH    = 3
local LINE_HEIGHT   = 2

local lineFolder = Instance.new("Folder")
lineFolder.Name = "AutoDoor50_Line"
lineFolder.Parent = workspace

local function clearLine()
    for _, child in ipairs(lineFolder:GetChildren()) do
        child:Destroy()
    end
end

local function createSegment(posA, posB)
    local distance = (posB - posA).Magnitude
    if distance < 0.2 then return end

    local mid = (posA + posB) / 2
    local segment = Instance.new("Part")
    segment.Size        = Vector3.new(LINE_WIDTH, LINE_HEIGHT, distance + 1)
    segment.CFrame      = CFrame.lookAt(mid, posB)
    segment.Anchored    = true
    segment.CanCollide  = false
    segment.Color       = Color3.fromRGB(0, 255, 0)
    segment.Material    = Enum.Material.Neon
    segment.Transparency = 0.2
    segment.Parent      = lineFolder
end

local function drawLineTo(hrp, destPos)
    clearLine()
    local a = hrp.Position + Vector3.new(0, LINE_HEIGHT / 2, 0)
    local b = destPos + Vector3.new(0, LINE_HEIGHT / 2, 0)
    createSegment(a, b)
end

local function getCurrentRoom()
    local gameData = game:GetService("ReplicatedStorage"):FindFirstChild("GameData")
    if gameData and gameData:FindFirstChild("LatestRoom") then
        return workspace.CurrentRooms:FindFirstChild(tostring(gameData.LatestRoom.Value))
    end
    return nil
end

local function getBookPositions(room)
    if not room then return {} end
    local assets = room:FindFirstChild("Assets")
    if not assets then return {} end

    local books = {}
    for _, shelf in ipairs(assets:GetDescendants()) do
        if shelf.Name == "Shelf" then
            local book = shelf:FindFirstChild("Book", true)
            if book then
                local pos = (book:IsA("BasePart") and book.Position) or book:GetPivot().Position
                table.insert(books, {Obj = book, Pos = pos})
            end
        end
    end
    return books
end

local function getFigure(room)
    if not room then return nil end
    local setup = room:FindFirstChild("FigureSetup")
    if setup then return setup:FindFirstChild("Figure", true) end
    return nil
end

local function figureDistance(hrp, room)
    local figure = getFigure(room)
    if figure then
        local part = figure.PrimaryPart or figure:FindFirstChildWhichIsA("BasePart", true)
        if part then return (hrp.Position - part.Position).Magnitude end
    end
    return math.huge
end

task.spawn(function()
    while task.wait(0.5) do
        local enabled = Toggles and Toggles.AutoDoor50_MainToggle
        if not enabled then
            clearLine()
            continue
        end

        local char = client.Character
        if not char then continue end

        local humanoid = char:FindFirstChildOfClass("Humanoid")
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not humanoid or not hrp then continue end

        local room = getCurrentRoom()
        if not room then continue end

        local books = getBookPositions(room)
        if #books == 0 then continue end

        for _, entry in ipairs(books) do
            if not (Toggles and Toggles.AutoDoor50_MainToggle) then break end

            drawLineTo(hrp, entry.Pos)

            local finished = false
            humanoid:MoveTo(entry.Pos)
            humanoid.MoveToFinished:Once(function() finished = true end)

            while not finished do
                if not (Toggles and Toggles.AutoDoor50_MainToggle) then break end

                if figureDistance(hrp, room) <= FIGURE_RADIUS then
                    humanoid:MoveTo(hrp.Position)
                    repeat task.wait(0.2) until figureDistance(hrp, room) > FIGURE_RADIUS
                    humanoid:MoveTo(entry.Pos)
                end

                drawLineTo(hrp, entry.Pos)
                task.wait(0.1)
            end

            clearLine()
            task.wait(0.5)
        end
    end
end)

return {
    Name = "AutoDoor50",
    Title = "Auto Door 50",
    Description = "Automatically passes door 50",
    Author = "TITAN DAVE",
    Version = "1.0",
    Game = 6839171747,

    Elements = {
        {
            Type = "Toggle",
            Name = "MainToggle",
            Arguments = {
                Text = "Auto Door 50",
                Default = false
            }
        }
    }
}
