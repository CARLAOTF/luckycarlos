local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local SoundService = game:GetService("SoundService")

local player = Players.LocalPlayer

local notificationSound = Instance.new("Sound")
notificationSound.SoundId = "rbxassetid://4590662766"
notificationSound.Volume = 2
notificationSound.Parent = SoundService

local knownBlocks = {}
local isFirstCheck = true

task.spawn(function()
    while task.wait(0.5) do
        local pasta = Workspace:FindFirstChild("LuckyBlocksActive")
        if pasta then
            for _, bloco in pairs(pasta:GetChildren()) do
                if not knownBlocks[bloco] then
                    knownBlocks[bloco] = true
                    if not isFirstCheck then
                        notificationSound:Play()
                    end
                end
            end
            isFirstCheck = false
        end
    end
end)

local ScreenGui = Instance.new("ScreenGui")
local success = pcall(function() ScreenGui.Parent = CoreGui end)
if not success then ScreenGui.Parent = player:WaitForChild("PlayerGui") end
ScreenGui.Name = "LuckyBlockPanel"
ScreenGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 250, 0, 200)
MainFrame.Position = UDim2.new(0.5, -125, 0.5, -100)
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local TopBar = Instance.new("Frame", MainFrame)
TopBar.Size = UDim2.new(1, 0, 0, 30)
TopBar.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
TopBar.BorderSizePixel = 0

local Title = Instance.new("TextLabel", TopBar)
Title.Size = UDim2.new(1, -60, 1, 0)
Title.Position = UDim2.new(0, 10, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Painel Lucky Block"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 16

local MinButton = Instance.new("TextButton", TopBar)
MinButton.Size = UDim2.new(0, 30, 1, 0)
MinButton.Position = UDim2.new(1, -60, 0, 0)
MinButton.BackgroundTransparency = 1
MinButton.Text = "-"
MinButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinButton.TextSize = 20
MinButton.Font = Enum.Font.SourceSansBold

local CloseButton = Instance.new("TextButton", TopBar)
CloseButton.Size = UDim2.new(0, 30, 1, 0)
CloseButton.Position = UDim2.new(1, -30, 0, 0)
CloseButton.BackgroundTransparency = 1
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 100, 100)
CloseButton.TextSize = 18
CloseButton.Font = Enum.Font.SourceSansBold

local ContentFrame = Instance.new("Frame", MainFrame)
ContentFrame.Size = UDim2.new(1, 0, 1, -30)
ContentFrame.Position = UDim2.new(0, 0, 0, 30)
ContentFrame.BackgroundTransparency = 1

local EspButton = Instance.new("TextButton", ContentFrame)
EspButton.Size = UDim2.new(0.9, 0, 0, 35)
EspButton.Position = UDim2.new(0.05, 0, 0, 10)
EspButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
EspButton.Text = "ESP Lucky Block: OFF"
EspButton.TextColor3 = Color3.fromRGB(255, 255, 255)
EspButton.Font = Enum.Font.SourceSansSemibold
EspButton.TextSize = 16

local TpButton = Instance.new("TextButton", ContentFrame)
TpButton.Size = UDim2.new(0.9, 0, 0, 35)
TpButton.Position = UDim2.new(0.05, 0, 0, 55)
TpButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
TpButton.Text = "Teleportar para Lucky Block"
TpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TpButton.Font = Enum.Font.SourceSansSemibold
TpButton.TextSize = 16

local AutoClickButton = Instance.new("TextButton", ContentFrame)
AutoClickButton.Size = UDim2.new(0.9, 0, 0, 35)
AutoClickButton.Position = UDim2.new(0.05, 0, 0, 100)
AutoClickButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
AutoClickButton.Text = "Auto Pegar/Clicar: OFF"
AutoClickButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoClickButton.Font = Enum.Font.SourceSansSemibold
AutoClickButton.TextSize = 16

local minimized = false
MinButton.MouseButton1Click:Connect(function()
    minimized = not minimized
    ContentFrame.Visible = not minimized
    if minimized then
        MainFrame.Size = UDim2.new(0, 250, 0, 30)
    else
        MainFrame.Size = UDim2.new(0, 250, 0, 200)
    end
end)

local pointerPart = Instance.new("Part")
pointerPart.Size = Vector3.new(0.3, 0.3, 3)
pointerPart.Anchored = true
pointerPart.CanCollide = false
pointerPart.Material = Enum.Material.Neon
pointerPart.Color = Color3.fromRGB(0, 255, 255)

local surfaceGui = Instance.new("SurfaceGui", pointerPart)
surfaceGui.Face = Enum.NormalId.Front
local tipFrame = Instance.new("Frame", surfaceGui)
tipFrame.Size = UDim2.new(1, 0, 1, 0)
tipFrame.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

local function getBlocoInfo(bloco)
    local cube = bloco:FindFirstChild("Cube")
    if cube and cube:IsA("BasePart") then
        return cube, cube.Size, cube.Color
    end

    if bloco:IsA("BasePart") then
        return bloco, bloco.Size, bloco.Color
    elseif bloco:IsA("Model") then
        local primary = bloco.PrimaryPart or bloco:FindFirstChildWhichIsA("BasePart", true)
        if primary then
            local _, size = bloco:GetBoundingBox()
            return primary, size, primary.Color
        end
    end
    return nil, Vector3.new(3, 3, 3), Color3.fromRGB(255, 215, 0)
end

local function getNearestLuckyBlock()
    local character = player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return nil end
    local hrp = character.HumanoidRootPart
    
    local pasta = Workspace:FindFirstChild("LuckyBlocksActive")
    if not pasta then return nil end
    
    local nearestPos = nil
    local nearestColor = Color3.fromRGB(0, 255, 255)
    local minDistance = math.huge
    
    for _, bloco in pairs(pasta:GetChildren()) do
        local mainPart, _, color = getBlocoInfo(bloco)
        local pos = mainPart and mainPart.Position or (bloco:IsA("BasePart") and bloco.Position)
        
        if pos then
            local dist = (hrp.Position - pos).Magnitude
            if dist < minDistance then
                minDistance = dist
                nearestPos = pos
                nearestColor = color
            end
        end
    end
    
    return nearestPos, nearestColor
end

local espAtivo = false
local espObjects = {}
local espRenderConnection = nil

local function limparESP()
    for _, obj in pairs(espObjects) do
        if obj then obj:Destroy() end
    end
    table.clear(espObjects)
end

local function atualizarESP()
    limparESP()
    if not espAtivo then return end
    
    local pasta = Workspace:FindFirstChild("LuckyBlocksActive")
    if pasta then
        for _, bloco in pairs(pasta:GetChildren()) do
            local mainPart, size, blockColor = getBlocoInfo(bloco)
            
            if mainPart then
                local box = Instance.new("BoxHandleAdornment")
                box.Size = size
                box.Adornee = mainPart
                box.AlwaysOnTop = true
                box.Color3 = blockColor
                box.Transparency = 0.2
                box.ZIndex = 10
                box.Parent = ScreenGui
                table.insert(espObjects, box)

                local sel = Instance.new("SelectionBox")
                sel.Adornee = bloco
                sel.Color3 = blockColor
                sel.LineThickness = 0.08
                sel.AlwaysOnTop = true
                sel.Parent = ScreenGui
                table.insert(espObjects, sel)

                local bgui = Instance.new("BillboardGui")
                bgui.Adornee = mainPart
                bgui.Size = UDim2.new(0, 150, 0, 40)
                bgui.AlwaysOnTop = true
                bgui.ExtentsOffset = Vector3.new(0, (size.Y/2) + 1.5, 0)
                
                local texto = Instance.new("TextLabel", bgui)
                texto.Size = UDim2.new(1, 0, 1, 0)
                texto.BackgroundTransparency = 1
                texto.Text = "LUCKY BLOCK AQUI!"
                texto.TextColor3 = blockColor
                texto.TextStrokeTransparency = 0
                texto.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
                texto.Font = Enum.Font.GothamBlack
                texto.TextSize = 16
                
                bgui.Parent = ScreenGui
                table.insert(espObjects, bgui)
            end
        end
    end
end

EspButton.MouseButton1Click:Connect(function()
    espAtivo = not espAtivo
    if espAtivo then
        EspButton.Text = "ESP Lucky Block: ON"
        EspButton.TextColor3 = Color3.fromRGB(100, 255, 100)
        pointerPart.Parent = Workspace
        
        espRenderConnection = RunService.RenderStepped:Connect(function()
            local char = player.Character
            if char and char:FindFirstChild("Head") then
                local targetPos, targetColor = getNearestLuckyBlock()
                if targetPos then
                    pointerPart.Transparency = 0
                    pointerPart.Color = targetColor
                    pointerPart.CFrame = CFrame.lookAt(char.Head.Position + Vector3.new(0, 4, 0), targetPos)
                else
                    pointerPart.Transparency = 1
                end
            end
        end)
    else
        EspButton.Text = "ESP Lucky Block: OFF"
        EspButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        limparESP()
        pointerPart.Parent = nil
        if espRenderConnection then
            espRenderConnection:Disconnect()
            espRenderConnection = nil
        end
    end
end)

CloseButton.MouseButton1Click:Connect(function()
    espAtivo = false
    limparESP()
    pointerPart:Destroy()
    if espRenderConnection then espRenderConnection:Disconnect() end
    ScreenGui:Destroy()
end)

task.spawn(function()
    while task.wait(1) do
        if espAtivo then atualizarESP() end
    end
end)

TpButton.MouseButton1Click:Connect(function()
    local targetPos, _ = getNearestLuckyBlock()
    if targetPos then
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            character.HumanoidRootPart.CFrame = CFrame.new(targetPos + Vector3.new(0, 3, 0))
        end
    end
end)

local autoClickAtivo = false

AutoClickButton.MouseButton1Click:Connect(function()
    autoClickAtivo = not autoClickAtivo
    if autoClickAtivo then
        AutoClickButton.Text = "Auto Pegar/Clicar: ON"
        AutoClickButton.TextColor3 = Color3.fromRGB(100, 255, 100)
    else
        AutoClickButton.Text = "Auto Pegar/Clicar: OFF"
        AutoClickButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
end)

task.spawn(function()
    while task.wait(0.2) do
        if autoClickAtivo then
            local pasta = Workspace:FindFirstChild("LuckyBlocksActive")
            if pasta then
                for _, bloco in pairs(pasta:GetChildren()) do
                    local clickDetector = bloco:FindFirstChildWhichIsA("ClickDetector", true)
                    if clickDetector then
                        fireclickdetector(clickDetector, 50)
                    end
                end
            end
        end
    end
end)

