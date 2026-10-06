-- [[ FTAP & EVADE MULTI-HUB - PLAYERS TAB ADDED ]] --

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local UserInputService = game:GetService("UserInputService")
local Camera = Workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- 1. СИСТЕМА КОНФИГА
local Config = {
    Noclip = false,
    Speed = false,
    Bypass = false,
    InfJump = false,
    TpForward = false,
    EvadeGod = false,
    EvadeStamina = false,
    AutoDodge = false,
    Fullbright = false,
    NightVision = false,
    FpsBoost = false,
    UltraFps = false,
    Resolution = false
}

local ConfigFileName = "FtapHub_Config.json"
pcall(function()
    if readfile and isfile and isfile(ConfigFileName) then
        local decoded = game:GetService("HttpService"):JSONDecode(readfile(ConfigFileName))
        if decoded and type(decoded) == "table" then
            for k, v in pairs(decoded) do Config[k] = v end
        end
    end
end)

local function SaveConfig()
    pcall(function()
        if writefile then
            writefile(ConfigFileName, game:GetService("HttpService"):JSONEncode(Config))
        end
    end)
end

-- 2. ЗАЩИТА
pcall(function()
    local mt = getrawmetatable(game)
    local oldNamecall = mt.__namecall
    setreadonly(mt, false)
    mt.__namecall = newcclosure(function(self, ...)
        local method = getnamecallmethod()
        local nameStr = tostring(self):lower()
        if nameStr:find("anticheat") or nameStr:find("ban") or nameStr:find("kick") or nameStr:find("detect") or nameStr:find("report") or nameStr:find("ac") then
            if method == "FireServer" or method == "InvokeServer" then return nil end
        end
        return oldNamecall(self, ...)
    end)
    setreadonly(mt, true)
end)

-- Очистка старых окон
pcall(function()
    if PlayerGui:FindFirstChild("FtapMultiHub") then PlayerGui.FtapMultiHub:Destroy() end
    if CoreGui:FindFirstChild("FtapMultiHub") then CoreGui.FtapMultiHub:Destroy() end
end)

-- 3. СОЗДАНИЕ GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FtapMultiHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 2147483647
ScreenGui.IgnoreGuiInset = true

local success = pcall(function() ScreenGui.Parent = CoreGui end)
if not success then ScreenGui.Parent = PlayerGui end

-- Главная панель
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
MainFrame.BackgroundTransparency = 0.05
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.5, -210, 0.5, -150)
MainFrame.Size = UDim2.new(0, 420, 0, 310)
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)
local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(45, 45, 55)
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

-- Шапка
local TopBar = Instance.new("Frame")
TopBar.Parent = MainFrame
TopBar.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
TopBar.BorderSizePixel = 0
TopBar.Size = UDim2.new(1, 0, 0, 34)
Instance.new("UICorner", TopBar).CornerRadius = UDim.new(0, 10)

-- FPS и Пинг
local FpsLabel = Instance.new("TextLabel")
FpsLabel.Parent = TopBar
FpsLabel.BackgroundTransparency = 1
FpsLabel.Position = UDim2.new(0, 12, 0, 0)
FpsLabel.Size = UDim2.new(0, 240, 1, 0)
FpsLabel.Font = Enum.Font.GothamMedium
FpsLabel.Text = "FPS: 60 | Ping: 40ms"
FpsLabel.TextColor3 = Color3.fromRGB(0, 220, 130)
FpsLabel.TextSize = 10
FpsLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Кнопка закрытия
local CloseBtn = Instance.new("TextButton")
CloseBtn.Parent = TopBar
CloseBtn.BackgroundColor3 = Color3.fromRGB(235, 65, 65)
CloseBtn.Position = UDim2.new(1, -28, 0.5, -9)
CloseBtn.Size = UDim2.new(0, 18, 0, 18)
CloseBtn.Text = ""
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(1, 0)

-- Плавающая кнопка возврата
local ToggleButton = Instance.new("TextButton")
ToggleButton.Parent = ScreenGui
ToggleButton.BackgroundColor3 = Color3.fromRGB(20, 20, 26)
ToggleButton.Position = UDim2.new(0, 15, 0.3, 0)
ToggleButton.Size = UDim2.new(0, 45, 0, 45)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.Text = "FTAP"
ToggleButton.TextColor3 = Color3.fromRGB(0, 200, 255)
ToggleButton.TextSize = 10
ToggleButton.Visible = false
Instance.new("UICorner", ToggleButton).CornerRadius = UDim.new(1, 0)
Instance.new("UIStroke", ToggleButton).Color = Color3.fromRGB(0, 200, 255)

CloseBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    ToggleButton.Visible = true
end)
ToggleButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = true
    ToggleButton.Visible = false
end)

-- Панель вкладок
local TabsBar = Instance.new("Frame")
TabsBar.Parent = MainFrame
TabsBar.BackgroundTransparency = 1
TabsBar.Position = UDim2.new(0, 8, 0, 40)
TabsBar.Size = UDim2.new(1, -16, 0, 30)

local UICols = Instance.new("UIListLayout")
UICols.Parent = TabsBar
UICols.FillDirection = Enum.FillDirection.Horizontal
UICols.SortOrder = Enum.SortOrder.LayoutOrder
UICols.Padding = UDim.new(0, 3)

local PagesContainer = Instance.new("Folder")
PagesContainer.Parent = MainFrame

local pages = {}
local tabButtons = {}

local function createTab(name, index)
    local tabBtn = Instance.new("TextButton")
    tabBtn.Parent = TabsBar
    tabBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    tabBtn.Size = UDim2.new(0.2, -3, 1, 0)
    tabBtn.Font = Enum.Font.GothamBold
    tabBtn.Text = name
    tabBtn.TextColor3 = Color3.fromRGB(140, 145, 160)
    tabBtn.TextSize = 8
    Instance.new("UICorner", tabBtn).CornerRadius = UDim.new(0, 6)
    
    local page = Instance.new("ScrollingFrame")
    page.Name = "Page_" .. name
    page.Parent = PagesContainer
    page.BackgroundTransparency = 1
    page.Position = UDim2.new(0, 8, 0, 78)
    page.Size = UDim2.new(1, -16, 1, -86)
    page.CanvasSize = UDim2.new(0, 0, 0, 0)
    page.AutomaticCanvasSize = Enum.AutomaticSize.Y
    page.ScrollBarThickness = 3
    page.Visible = (index == 1)
    
    local pLayout = Instance.new("UIListLayout")
    pLayout.Parent = page
    pLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    pLayout.SortOrder = Enum.SortOrder.LayoutOrder
    pLayout.Padding = UDim.new(0, 5)
    
    tabBtn.MouseButton1Click:Connect(function()
        for _, p in pairs(pages) do p.Visible = false end
        for _, b in pairs(tabButtons) do
            b.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
            b.TextColor3 = Color3.fromRGB(140, 145, 160)
        end
        page.Visible = true
        tabBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 100)
        tabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    end)
    
    if index == 1 then
        tabBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 100)
        tabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    end
    
    table.insert(pages, page)
    table.insert(tabButtons, tabBtn)
    return page
end

local PageFtap = createTab("Ftap", 1)
local PageEvade = createTab("Evade", 2)
local PagePlayers = createTab("Players", 3)
local PageVisual = createTab("Visual", 4)
local PageTroll = createTab("Troll", 5)

-- Функция создания переключателя
local function addToggle(page, labelText, configKey, callback)
    local ToggleBox = Instance.new("Frame")
    ToggleBox.Parent = page
    ToggleBox.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
    ToggleBox.Size = UDim2.new(1, -4, 0, 35)
    Instance.new("UICorner", ToggleBox).CornerRadius = UDim.new(0, 6)
    
    local Label = Instance.new("TextLabel")
    Label.Parent = ToggleBox
    Label.BackgroundTransparency = 1
    Label.Position = UDim2.new(0, 10, 0, 0)
    Label.Size = UDim2.new(0.7, 0, 1, 0)
    Label.Font = Enum.Font.GothamMedium
    Label.Text = labelText
    Label.TextColor3 = Color3.fromRGB(200, 205, 215)
    Label.TextSize = 10
    Label.TextXAlignment = Enum.TextXAlignment.Left
    
    local SwitchBtn = Instance.new("TextButton")
    SwitchBtn.Parent = ToggleBox
    SwitchBtn.BackgroundColor3 = Config[configKey] and Color3.fromRGB(0, 170, 100) or Color3.fromRGB(40, 40, 50)
    SwitchBtn.Position = UDim2.new(1, -42, 0.5, -9)
    SwitchBtn.Size = UDim2.new(0, 32, 0, 18)
    SwitchBtn.Text = ""
    Instance.new("UICorner", SwitchBtn).CornerRadius = UDim.new(1, 0)
    
    local Circle = Instance.new("Frame")
    Circle.Parent = SwitchBtn
    Circle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    Circle.Position = Config[configKey] and UDim2.new(1, -16, 0.5, -7) or UDim2.new(0, 2, 0.5, -7)
    Circle.Size = UDim2.new(0, 14, 0, 14)
    Instance.new("UICorner", Circle).CornerRadius = UDim.new(1, 0)
    
    SwitchBtn.MouseButton1Click:Connect(function()
        Config[configKey] = not Config[configKey]
        SaveConfig()
        if Config[configKey] then
            SwitchBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 100)
            Circle.Position = UDim2.new(1, -16, 0.5, -7)
        else
            SwitchBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
            Circle.Position = UDim2.new(0, 2, 0.5, -7)
        end
        pcall(function() callback(Config[configKey]) end)
    end)
    
    if Config[configKey] then pcall(function() callback(true) end) end
end

-- 4. РЕАЛИЗАЦИЯ ФУНКЦИЙ

-- --- ВКЛАДКА FTAP ---
addToggle(PageFtap, "Noclip (Сквозь стены)", "Noclip", function(state) Config.Noclip = state end)
RunService.Stepped:Connect(function()
    if Config.Noclip and LocalPlayer.Character then
        pcall(function()
            for _, p in ipairs(LocalPlayer.Character:GetDescendants()) do
                if p:IsA("BasePart") then p.CanCollide = false end
            end
        end)
    end
end)

addToggle(PageFtap, "SpeedHack (Быстрый бег)", "Speed", function(state) Config.Speed = state end)
RunService.Stepped:Connect(function()
    if Config.Speed and LocalPlayer.Character then
        pcall(function()
            local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if hum and hum.MoveDirection.Magnitude > 0 then
                LocalPlayer.Character:TranslateBy(hum.MoveDirection * 0.45)
            end
        end)
    end
end)

addToggle(PageFtap, "Обход барьеров и ловушек", "Bypass", function(state) Config.Bypass = state end)
task.spawn(function()
    while true do
        task.wait(1)
        if Config.Bypass then
            pcall(function()
                for _, obj in ipairs(Workspace:GetChildren()) do
                    if obj.Name:lower():find("barrier") or obj.Name:lower():find("kill") or obj.Name:lower():find("trap") then
                        if obj:IsA("BasePart") then obj.CanCollide = false end
                    end
                end
            end)
        end
    end
end)

addToggle(PageFtap, "Бесконечный прыжок (Inf Jump)", "InfJump", function(state) Config.InfJump = state end)
UserInputService.JumpRequest:Connect(function()
    if Config.InfJump and LocalPlayer.Character then
        pcall(function()
            local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
        end)
    end
end)

local TpBtn = Instance.new("TextButton")
TpBtn.Parent = PageFtap
TpBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
TpBtn.Size = UDim2.new(1, -4, 0, 32)
TpBtn.Font = Enum.Font.GothamBold
TpBtn.Text = "⚡ Телепорт вперед"
TpBtn.TextColor3 = Color3.fromRGB(0, 200, 255)
TpBtn.TextSize = 10
Instance.new("UICorner", TpBtn).CornerRadius = UDim.new(0, 6)
TpBtn.MouseButton1Click:Connect(function()
    pcall(function()
        local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if hrp then
            hrp.CFrame = hrp.CFrame + (hrp.CFrame.LookVector * 15)
        end
    end)
end)


-- --- ВКЛАДКА EVADE ---
addToggle(PageEvade, "Защита от ботов", "EvadeGod", function(state) Config.EvadeGod = state end)
task.spawn(function()
    while true do
        task.wait(0.3)
        if Config.EvadeGod and LocalPlayer.Character and LocalPlayer.Character.PrimaryPart then
            pcall(function()
                local hrp = LocalPlayer.Character.PrimaryPart
                for _, v in ipairs(Workspace:GetDescendants()) do
                    if v:IsA("BasePart") and (v.Name:lower():find("nextbot") or v.Name:lower():find("bot")) then
                        if (v.Position - hrp.Position).Magnitude < 18 then v.CanCollide = false end
                    end
                end
            end)
        end
    end
end)

addToggle(PageEvade, "Бесконечная выносливость", "EvadeStamina", function(state) Config.EvadeStamina = state end)
task.spawn(function()
    while true do
        task.wait(0.5)
        if Config.EvadeStamina then
            pcall(function()
                local sf = LocalPlayer:FindFirstChild("PlayerStats") or LocalPlayer:FindFirstChild("Values")
                if sf then
                    for _, s in ipairs(sf:GetChildren()) do
                        if s.Name:lower():find("stamina") and (s:IsA("NumberValue") or s:IsA("IntValue")) then s.Value = 100 end
                    end
                end
            end)
        end
    end
end)


-- --- ВКЛАДКА PLAYERS (СПИСОК ИГРОКОВ, ТП И СЛЕЖКА) ---
local SelectedTarget = nil
local TargetLabel = Instance.new("TextLabel")
TargetLabel.Parent = PagePlayers
TargetLabel.BackgroundTransparency = 1
TargetLabel.Size = UDim2.new(1, -4, 0, 22)
TargetLabel.Font = Enum.Font.GothamMedium
TargetLabel.Text = "Цель: Никто не выбран"
TargetLabel.TextColor3 = Color3.fromRGB(200, 205, 215)
TargetLabel.TextSize = 10

-- Кнопка телепорта к цели
local TpTargetBtn = Instance.new("TextButton")
TpTargetBtn.Parent = PagePlayers
TpTargetBtn.BackgroundColor3 = Color3.fromRGB(0, 140, 90)
TpTargetBtn.Size = UDim2.new(1, -4, 0, 30)
TpTargetBtn.Font = Enum.Font.GothamBold
TpTargetBtn.Text = "🎯 Телепорт к цели"
TpTargetBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
TpTargetBtn.TextSize = 10
Instance.new("UICorner", TpTargetBtn).CornerRadius = UDim.new(0, 6)
TpTargetBtn.MouseButton1Click:Connect(function()
    pcall(function()
        if SelectedTarget and SelectedTarget.Character and SelectedTarget.Character:FindFirstChild("HumanoidRootPart") then
            local myHrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            if myHrp then
                myHrp.CFrame = SelectedTarget.Character.HumanoidRootPart.CFrame + Vector3.new(0, 3, 0)
            end
        end
    end)
end)

-- Кнопка слежки (Spectate)
local SpecBtn = Instance.new("TextButton")
SpecBtn.Parent = PagePlayers
SpecBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 55)
SpecBtn.Size = UDim2.new(1, -4, 0, 30)
SpecBtn.Font = Enum.Font.GothamBold
SpecBtn.Text = "👁️ Следить / Снять слежку"
SpecBtn.TextColor3 = Color3.fromRGB(0, 200, 255)
SpecBtn.TextSize = 10
Instance.new("UICorner", SpecBtn).CornerRadius = UDim.new(0, 6)

local isSpectating = false
SpecBtn.MouseButton1Click:Connect(function()
    isSpectating = not isSpectating
    if not isSpectating then
        Camera.CameraSubject = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    end
end)

RunService.RenderStepped:Connect(function()
    if isSpectating and SelectedTarget and SelectedTarget.Character then
        pcall(function()
            local hum = SelectedTarget.Character:FindFirstChildOfClass("Humanoid")
            if hum then Camera.CameraSubject = hum end
        end)
    end
end)

-- Контейнер со списком игроков
local PlayersListContainer = Instance.new("ScrollingFrame")
PlayersListContainer.Parent = PagePlayers
PlayersListContainer.BackgroundTransparency = 1
PlayersListContainer.Size = UDim2.new(1, -4, 0, 110)
PlayersListContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
PlayersListContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
PlayersListContainer.ScrollBarThickness = 2

local ListLayout = Instance.new("UIListLayout")
ListLayout.Parent = PlayersListContainer
ListLayout.SortOrder = Enum.SortOrder.LayoutOrder
ListLayout.Padding = UDim.new(0, 4)

local function RefreshPlayersList()
    for _, child in ipairs(PlayersListContainer:GetChildren()) do
        if child:IsA("TextButton") then child:Destroy() end
    end
    
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local pBtn = Instance.new("TextButton")
            pBtn.Parent = PlayersListContainer
            pBtn.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
            pBtn.Size = UDim2.new(1, 0, 0, 28)
            pBtn.Font = Enum.Font.GothamMedium
            pBtn.Text = plr.Name .. " (" .. plr.DisplayName .. ")"
            pBtn.TextColor3 = Color3.fromRGB(220, 220, 230)
            pBtn.TextSize = 9
            Instance.new("UICorner", pBtn).CornerRadius = UDim.new(0, 5)
            
            pBtn.MouseButton1Click:Connect(function()
                SelectedTarget = plr
                TargetLabel.Text = "Цель: " .. plr.Name
            end)
        end
    end
end

-- Кнопка обновления списка
local RefreshBtn = Instance.new("TextButton")
RefreshBtn.Parent = PagePlayers
RefreshBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 65)
RefreshBtn.Size = UDim2.new(1, -4, 0, 28)
RefreshBtn.Font = Enum.Font.GothamBold
RefreshBtn.Text = "🔄 Обновить список игроков"
RefreshBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
RefreshBtn.TextSize = 10
Instance.new("UICorner", RefreshBtn).CornerRadius = UDim.new(0, 6)
RefreshBtn.MouseButton1Click:Connect(RefreshPlayersList)

-- Авто-первичное заполнение списка
task.spawn(RefreshPlayersList)


-- --- ВКЛАДКА VISUAL ---
addToggle(PageVisual, "Fullbright (Убрать тени)", "Fullbright", function(state)
    Config.Fullbright = state
    Lighting.Brightness = state and 2 or 1
    Lighting.GlobalShadows = not state
end)

addToggle(PageVisual, "Ночное зрение", "NightVision", function(state)
    Config.NightVision = state
    Lighting.Ambient = state and Color3.fromRGB(200, 200, 200) or Color3.fromRGB(0, 0, 0)
end)

addToggle(PageVisual, "Буст ФПС (Эффекты)", "FpsBoost", function(state)
    Config.FpsBoost = state
    pcall(function()
        for _, v in ipairs(Lighting:GetChildren()) do if v:IsA("PostEffect") then v.Enabled = not state end end
    end)
end)

addToggle(PageVisual, "Ультра ФПС (Текстуры)", "UltraFps", function(state)
    Config.UltraFps = state
    pcall(function()
        for _, part in ipairs(Workspace:GetDescendants()) do
            if part:IsA("BasePart") then part.Material = state and Enum.Material.SmoothPlastic or Enum.Material.Plastic end
        end
    end)
end)

addToggle(PageVisual, "Растяг экрана (iPad)", "Resolution", function(state)
    Config.Resolution = state
    pcall(function() Camera.FieldOfView = state and 95 or 70 end)
end)


-- --- ВКЛАДКА TROLL ---
local ActionBtn = Instance.new("TextButton")
ActionBtn.Parent = PageTroll
ActionBtn.BackgroundColor3 = Color3.fromRGB(180, 45, 45)
ActionBtn.Size = UDim2.new(1, -4, 0, 35)
ActionBtn.Font = Enum.Font.GothamBold
ActionBtn.Text = "Снос сервера / Десинхронизация"
ActionBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ActionBtn.TextSize = 10
Instance.new("UICorner", ActionBtn).CornerRadius = UDim.new(0, 6)
ActionBtn.MouseButton1Click:Connect(function()
    pcall(function()
        for i = 1, 200 do task.spawn(function() pcall(function() local a = {math.random(1, 999999)} end) end) end
    end)
end)

-- Live FPS & Ping
RunService.RenderStepped:Connect(function()
    pcall(function()
        local fps = math.floor(1 / RunService.RenderStepped:Wait())
        local ping = math.floor(LocalPlayer:GetNetworkPing() * 1000)
        FpsLabel.Text = string.format("FPS: %d | Ping: %d ms", fps, ping)
    end)
end)

print("Ftap & Evade Multi-Hub with Players Tab loaded!")
