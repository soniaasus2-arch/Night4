local room = workspace.CurrentRooms["50"]
local assets = room.Assets
local character = game.Players.LocalPlayer.Character
local humanoid = character:FindFirstChildOfClass("Humanoid")
local hrp = character:WaitForChild("HumanoidRootPart")

local FIGURE_RADIUS = 15


local LINE_WIDTH     = 3      -- largura da linha
local LINE_HEIGHT    = 2      -- altura (parede fina)
local LINE_THICKNESS = 0.2

local lineFolder = Instance.new("Folder")
lineFolder.Name = "AutoDoor50_Line"
lineFolder.Parent = workspace

local function clearLine()
    for _, child in ipairs(lineFolder:GetChildren()) do
        child:Destroy()
    end
end


local function createSegment(posA, posB)
    local diff = posB - posA
    local distance = diff.Magnitude
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


local function drawLineTo(destPos)
    clearLine()

    local startPos = hrp.Position
    local a = startPos + Vector3.new(0, LINE_HEIGHT / 2, 0)
    local b = destPos  + Vector3.new(0, LINE_HEIGHT / 2, 0)

    createSegment(a, b)
end


local function getBookPositions()
    local books = {}
    for _, shelf in ipairs(assets:GetDescendants()) do
        if shelf.Name == "Shelf" then
            local book = shelf:FindFirstChild("Book", true)
            if book then
                local pos = (book:IsA("BasePart") and book.Position)
                            or book:GetPivot().Position
                table.insert(books, {Obj = book, Pos = pos})
            end
        end
    end
    return books
end


local function getFigure()
    return workspace.CurrentRooms["50"].FigureSetup:FindFirstChild("Figure", true)
end

local function figureDistance()
    local figure = getFigure()
    if figure then
        local part = figure.PrimaryPart
                     or figure:FindFirstChildWhichIsA("BasePart", true)
        if part then
            return (hrp.Position - part.Position).Magnitude
        end
    end
    return math.huge
end


local function walkToBooks()
    local books = getBookPositions()

    for _, entry in ipairs(books) do
        
        drawLineTo(entry.Pos)

        local finished = false
        humanoid:MoveTo(entry.Pos)

        humanoid.MoveToFinished:Once(function()
            finished = true
        end)

        while not finished do
            -- Se o Figure estiver perto, para
            if figureDistance() <= FIGURE_RADIUS then
                humanoid:MoveTo(hrp.Position)  -- para no lugar
                repeat
                    task.wait(0.2)
                until figureDistance() > FIGURE_RADIUS

                humanoid:MoveTo(entry.Pos)     -- volta a ir a o livro
            end

            drawLineTo(entry.Pos)

            task.wait(0.1)
        end

        
        clearLine()
        task.wait(0.5)
    end
end

walkToBooks()
