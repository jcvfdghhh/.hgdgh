-- ROCKET Evade Ultimate Pro (Full Script with Weather Changer, Key System, Speed, Dash, ESP, Coin Farm & Anti-Ban)
-- Исполнитель: Delta Executor

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local CoreGui = game:GetService("CoreGui")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer

-- Настройки ключа
local CORRECT_KEY = "ROCKET2026"
local isAuthorized = false

-- Удаление старых интерфейсов
if CoreGui:FindFirstChild("RocketKeySystem") then CoreGui.RocketKeySystem:Destroy() end
if CoreGui:FindFirstChild("RocketEvadeUltimate") then CoreGui.RocketEvadeUltimate:Destroy() end

-- [ОКНО ВВОДА КЛЮЧА]
local KeyGui = Instance.new("ScreenGui")
KeyGui.Name = "RocketKeySystem"
KeyGui.Parent = CoreGui

local KeyFrame = Instance.new("Frame")
KeyFrame.Size = UDim2.new(0, 300, 0, 180)
KeyFrame.Position = UDim2.new(0.5, -150, 0.5, -90)
KeyFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
KeyFrame.BorderSizePixel = 0
KeyFrame.Active = true
KeyFrame.Draggable = true
KeyFrame.Parent = KeyGui
Instance.new("UICorner", KeyFrame).CornerRadius = UDim.new(0, 10)

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Size = UDim2.new(1, 0, 0, 40)
KeyTitle.BackgroundTransparency = 1
KeyTitle.Text = "ROCKET | Авторизация"
KeyTitle.TextColor3 = Color3.fromRGB(0, 255, 128)
KeyTitle.TextSize = 16
KeyTitle.Font = Enum.Font.SourceSansBold
KeyTitle.Parent = KeyFrame

local KeyBox = Instance.new("TextBox")
KeyBox.Size = UDim2.new(0, 260, 0, 35)
KeyBox.Position = UDim2.new(0, 20, 0, 55)
KeyBox.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
KeyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyBox.PlaceholderText = "Введите ключ..."
KeyBox.TextSize = 14
KeyBox.Font = Enum.Font.SourceSansBold
KeyBox.Parent = KeyFrame
Instance.new("UICorner", KeyBox).CornerRadius = UDim.new(0, 6)

local SubmitBtn = Instance.new("TextButton")
SubmitBtn.Size = UDim2.new(0, 260, 0, 35)
SubmitBtn.Position = UDim2.new(0, 20, 0, 100)
SubmitBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 90)
SubmitBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
SubmitBtn.TextSize = 14
SubmitBtn.Font = Enum.Font.SourceSansBold
SubmitBtn.Text = "Подтвердить ключ"
SubmitBtn.Parent = KeyFrame
Instance.new("UICorner", SubmitBtn).CornerRadius = UDim.new(0, 6)

local GetKeyBtn = Instance.new("TextButton")
GetKeyBtn.Size = UDim2.new(0, 260, 0, 25)
GetKeyBtn.Position = UDim2.new(0, 20, 0, 142)
GetKeyBtn.BackgroundTransparency = 1
GetKeyBtn.TextColor3 = Color3.fromRGB(150, 150, 170)
GetKeyBtn.TextSize = 12
GetKeyBtn.Font = Enum.Font.SourceSansBold
GetKeyBtn.Text = "Получить ключ (Telegram)"
GetKeyBtn.Parent = KeyFrame

GetKeyBtn.MouseButton1Click:Connect(function()
    setclipboard("https://t.me/RocketWay")
    GetKeyBtn.Text = "Ссылка скопирована!"
    task.wait(1.5)
    GetKeyBtn.Text = "Получить ключ (Telegram)"
end)

SubmitBtn.MouseButton1Click:Connect(function()
    if KeyBox.Text == CORRECT_KEY then
        isAuthorized = true
        KeyGui:Destroy()
        LoadMainScript()
    else
        SubmitBtn.Text = "Неверный ключ!"
        SubmitBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        task.wait(1.5)
        SubmitBtn.Text = "Подтвердить ключ"
        SubmitBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 90)
    end
end)

-- [ОСНОВНОЙ СКРИПТ (Запускается после ввода ключа)]
function LoadMainScript()
    getgenv().RocketConfig = {
        SpeedEnabled = false,
        SpeedValue = 200,
        DashMultiplier = 2.5,
        CoinFarm = false,
        ESPEnabled = false,
        PublicVisuals = true,
        AntiBanBypass = true,
        CurrentWeather = "Normal"
    }

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "RocketEvadeUltimate"
    ScreenGui.Parent = CoreGui

    local MainFrame = Instance.new("Frame")
    MainFrame.Size = UDim2.new(0, 340, 0, 570)
    MainFrame.Position = UDim2.new(0.5, -170, 0.5, -285)
    MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
    MainFrame.BorderSizePixel = 0
    MainFrame.Active = true
    MainFrame.Draggable = true
    MainFrame.Parent = ScreenGui
    Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.BackgroundTransparency = 1
    Title.Text = "ROCKET | Evade Ultimate Pro"
    Title.TextColor3 = Color3.fromRGB(0, 255, 128)
    Title.TextSize = 16
    Title.Font = Enum.Font.SourceSansBold
    Title.Parent = MainFrame

    local function createToggle(name, yPos, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 300, 0, 32)
        btn.Position = UDim2.new(0, 20, 0, yPos)
        btn.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.TextSize = 13
        btn.Font = Enum.Font.SourceSansBold
        btn.Text = name .. ": OFF"
        btn.Parent = MainFrame
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
        
        local state = false
        btn.MouseButton1Click:Connect(function()
            state = not state
            btn.BackgroundColor3 = state and Color3.fromRGB(0, 180, 90) or Color3.fromRGB(30, 30, 40)
            btn.Text = name .. (state and ": ON" or ": OFF")
            callback(state)
        end)
    end

    local function createTextBox(name, yPos, defaultVal, callback)
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(0, 160, 0, 32)
        label.Position = UDim2.new(0, 20, 0, yPos)
        label.BackgroundTransparency = 1
        label.TextColor3 = Color3.fromRGB(255, 255, 255)
        label.TextSize = 12
        label.Font = Enum.Font.SourceSansBold
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Text = name
        label.Parent = MainFrame
        
        local box = Instance.new("TextBox")
        box.Size = UDim2.new(0, 130, 0, 32)
        box.Position = UDim2.new(0, 190, 0, yPos)
        box.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        box.TextColor3 = Color3.fromRGB(0, 255, 128)
        box.TextSize = 13
        box.Font = Enum.Font.SourceSansBold
        box.Text = tostring(defaultVal)
        box.Parent = MainFrame
        Instance.new("UICorner", box).CornerRadius = UDim.new(0, 6)
        
        box.FocusLost:Connect(function()
            local num = tonumber(box.Text)
            if num then callback(num) else box.Text = tostring(defaultVal) callback(defaultVal) end
        end)
    end

    -- Элементы интерфейса
    createToggle("Спидхак (200+)", 45, function(state) getgenv().RocketConfig.SpeedEnabled = state end)
    createTextBox("Скорость:", 82, 200, function(val) getgenv().RocketConfig.SpeedValue = val end)
    createTextBox("Множитель Дэша:", 119, 2.5, function(val) getgenv().RocketConfig.DashMultiplier = val end)
    createToggle("Визуальная синхронизация", 156, function(state) getgenv().RocketConfig.PublicVisuals = state end)
    createToggle("Автосбор монет", 193, function(state) getgenv().RocketConfig.CoinFarm = state end)
    createToggle("ВХ (Игроки + Nextbots)", 230, function(state) getgenv().RocketConfig.ESPEnabled = state end)

    -- Меню выбора погоды в Evade
    local WeatherLabel = Instance.new("TextLabel")
    WeatherLabel.Size = UDim2.new(0, 300, 0, 20)
    WeatherLabel.Position = UDim2.new(0, 20, 0, 268)
    WeatherLabel.BackgroundTransparency = 1
    WeatherLabel.TextColor3 = Color3.fromRGB(150, 150, 170)
    WeatherLabel.TextSize = 12
    WeatherLabel.Font = Enum.Font.SourceSansBold
    WeatherLabel.Text = "Выбор погоды / Освещения в Evade:"
    WeatherLabel.Parent = MainFrame

    local weatherTypes = {"Normal", "Foggy", "Dark (Night)", "Sunset", "Blizzard"}
    local weatherIdx = 1

    local WeatherBtn = Instance.new("TextButton")
    WeatherBtn.Size = UDim2.new(0, 300, 0, 32)
    WeatherBtn.Position = UDim2.new(0, 20, 0, 292)
    WeatherBtn.BackgroundColor3 = Color3.fromRGB(50, 40, 70)
    WeatherBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    WeatherBtn.TextSize = 13
    WeatherBtn.Font = Enum.Font.SourceSansBold
    WeatherBtn.Text = "Погода: Normal (Нажмите для смены)"
    WeatherBtn.Parent = MainFrame
    Instance.new("UICorner", WeatherBtn).CornerRadius = UDim.new(0, 6)

    WeatherBtn.MouseButton1Click:Connect(function()
        weatherIdx = weatherIdx + 1
        if weatherIdx > #weatherTypes then weatherIdx = 1 end
        local selected = weatherTypes[weatherIdx]
        WeatherBtn.Text = "Погода: " .. selected
        
        pcall(function()
            if selected == "Normal" then
                Lighting.ClockTime = 14
                Lighting.Brightness = 2
                Lighting.FogEnd = 100000
                Lighting.Ambient = Color3.fromRGB(128, 128, 128)
            elseif selected == "Foggy" then
                Lighting.ClockTime = 10
                Lighting.Brightness = 1
                Lighting.FogEnd = 350
                Lighting.Ambient = Color3.fromRGB(90, 90, 90)
            elseif selected == "Dark (Night)" then
                Lighting.ClockTime = 0
                Lighting.Brightness = 0.2
                Lighting.FogEnd = 200
                Lighting.Ambient = Color3.fromRGB(15, 15, 20)
            elseif selected == "Sunset" then
                Lighting.ClockTime = 18.5
                Lighting.Brightness = 1.5
                Lighting.FogEnd = 5000
                Lighting.Ambient = Color3.fromRGB(200, 100, 50)
            elseif selected == "Blizzard" then
                Lighting.ClockTime = 12
                Lighting.Brightness = 1.2
                Lighting.FogEnd = 150
                Lighting.Ambient = Color3.fromRGB(200, 220, 255)
            end
        end)
    end)

    -- Блок связи
    local SocialLabel = Instance.new("TextLabel")
    SocialLabel.Size = UDim2.new(0, 300, 0, 20)
    SocialLabel.Position = UDim2.new(0, 20, 0, 332)
    SocialLabel.BackgroundTransparency = 1
    SocialLabel.TextColor3 = Color3.fromRGB(150, 150, 170)
    SocialLabel.TextSize = 12
    SocialLabel.Font = Enum.Font.SourceSansBold
    SocialLabel.Text = "Сообщество (Rocket Way):"
    SocialLabel.Parent = MainFrame

    local TgBtn = Instance.new("TextButton")
    TgBtn.Size = UDim2.new(0, 145, 0, 28)
    TgBtn.Position = UDim2.new(0, 20, 0, 355)
    TgBtn.BackgroundColor3 = Color3.fromRGB(0, 136, 204)
    TgBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    TgBtn.TextSize = 12
    TgBtn.Font = Enum.Font.SourceSansBold
    TgBtn.Text = "Telegram"
    TgBtn.Parent = MainFrame
    Instance.new("UICorner", TgBtn).CornerRadius = UDim.new(0, 6)
    TgBtn.MouseButton1Click:Connect(function() setclipboard("https://t.me/RocketWay") end)

    local DcBtn = Instance.new("TextButton")
    DcBtn.Size = UDim2.new(0, 145, 0, 28)
    DcBtn.Position = UDim2.new(0, 175, 0, 355)
    DcBtn.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
    DcBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    DcBtn.TextSize = 12
    DcBtn.Font = Enum.Font.SourceSansBold
    DcBtn.Text = "Discord"
    DcBtn.Parent = MainFrame
    Instance.new("UICorner", DcBtn).CornerRadius = UDim.new(0, 6)
    DcBtn.MouseButton1Click:Connect(function() setclipboard("https://discord.gg/RocketWay") end)

    local ToggleUIBtn = Instance.new("TextButton")
    ToggleUIBtn.Size = UDim2.new(0, 300, 0, 35)
    ToggleUIBtn.Position = UDim2.new(0, 20, 0, 520)
    ToggleUIBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    ToggleUIBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleUIBtn.TextSize = 13
    ToggleUIBtn.Font = Enum.Font.SourceSansBold
    ToggleUIBtn.Text = "Скрыть / Показать меню"
    ToggleUIBtn.Parent = MainFrame
    Instance.new("UICorner", ToggleUIBtn).CornerRadius = UDim.new(0, 6)

    local uiVisible = true
    ToggleUIBtn.MouseButton1Click:Connect(function()
        uiVisible = not uiVisible
        MainFrame.Size = uiVisible and UDim2.new(0, 340, 0, 570) or UDim2.new(0, 340, 0, 90)
        for _, child in pairs(MainFrame:GetChildren()) do
            if child ~= Title and child ~= ToggleUIBtn then child.Visible = uiVisible end
        end
        ToggleUIBtn.Visible = true
    end)

    -- Антибан обход
    pcall(function()
        local mt = getrawmetatable(game)
        setreadonly(mt, false)
        local oldIndex = mt.__index
        mt.__index = newcclosure(function(self, k)
            if not checkcaller() and (k == "WalkSpeed" or k == "JumpPower") then return 16 end
            return oldIndex(self, k)
        end)
        setreadonly(mt, true)
    end)

    -- Логика движения, спидхака, деша и анимаций
    RunService.Heartbeat:Connect(function(dt)
        pcall(function()
            local char = LocalPlayer.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChild("Humanoid")
            
            if root and hum then
                for _, track in pairs(hum:GetPlayingAnimationTracks()) do
                    if track.Speed == 0 then track:AdjustSpeed(1) end
                end
                
                if getgenv().RocketConfig.SpeedEnabled and hum.MoveDirection.Magnitude > 0 then
                    local currentSpeed = getgenv().RocketConfig.SpeedValue
                    if hum:GetState() == Enum.HumanoidStateType.Freefall or hum.Jump then
                        currentSpeed = currentSpeed * getgenv().RocketConfig.DashMultiplier
                    end

                    root.CFrame = root.CFrame + (hum.MoveDirection * (currentSpeed * dt))
                    
                    if getgenv().RocketConfig.PublicVisuals then
                        root.AssemblyLinearVelocity = hum.MoveDirection * currentSpeed
                    end
                end
            end
        end)
    end)

    -- Автосбор
    task.spawn(function()
        while task.wait(0.3) do
            if getgenv().RocketConfig.CoinFarm then
                pcall(function()
                    for _, obj in pairs(Workspace:GetDescendants()) do
                        if obj:IsA("BasePart") and (obj.Name:lower():find("coin") or obj.Name:lower():find("pickup") or obj.Name:lower():find("cash") or obj.Name:lower():find("ticket")) then
                            local char = LocalPlayer.Character
                            if char and char:FindFirstChild("HumanoidRootPart") then
                                char.HumanoidRootPart.CFrame = obj.CFrame + Vector3.new(0, 2.5, 0)
                                task.wait(0.05)
                            end
                        end
                    end
                end)
            end
        end
    end)

    -- ВХ
    local function createESP(object, color, textString)
        if object:FindFirstChild("RocketESP") then return end
        local bill = Instance.new("BillboardGui")
        bill.Name = "RocketESP"
        bill.Size = UDim2.new(0, 100, 0, 40)
        bill.AlwaysOnTop = true
        bill.StudsOffset = Vector3.new(0, 3, 0)
        
        local text = Instance.new("TextLabel")
        text.Size = UDim2.new(1, 0, 1, 0)
        text.BackgroundTransparency = 1
        text.TextColor3 = color
        text.TextScaled = true
        text.Font = Enum.Font.SourceSansBold
        text.TextStrokeTransparency = 0
        text.Text = textString
        text.Parent = bill
        bill.Parent = object
    end

    RunService.RenderStepped:Connect(function()
        if not getgenv().RocketConfig.ESPEnabled then return end
        pcall(function()
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
                    createESP(player.Character.Head, Color3.fromRGB(0, 255, 128), player.Name)
                end
            end
            if Workspace:FindFirstChild("Game") and Workspace.Game:FindFirstChild("Nextbots") then
                for _, bot in pairs(Workspace.Game.Nextbots:GetChildren()) do
                    if bot:FindFirstChild("HumanoidRootPart") then
                        createESP(bot.HumanoidRootPart, Color3.fromRGB(255, 0, 0), "⚠️ NEXTBOT")
                    end
                end
            end
        end)
    end)
    
    print("ROCKET | Скрипт успешно разблокирован п-- ROCKET Evade Ultimate Pro (Full Script with Weather Changer, Key System, Speed, Dash, ESP, Coin Farm & Anti-Ban)
-- Исполнитель: Delta Executor

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local CoreGui = game:GetService("CoreGui")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer

-- Настройки ключа
local CORRECT_KEY = "ROCKET2026"
local isAuthorized = false

-- Удаление старых интерфейсов
if CoreGui:FindFirstChild("RocketKeySystem") then CoreGui.RocketKeySystem:Destroy() end
if CoreGui:FindFirstChild("RocketEvadeUltimate") then CoreGui.RocketEvadeUltimate:Destroy() end

-- [ОКНО ВВОДА КЛЮЧА]
local KeyGui = Instance.new("ScreenGui")
KeyGui.Name = "RocketKeySystem"
KeyGui.Parent = CoreGui

local KeyFrame = Instance.new("Frame")
KeyFrame.Size = UDim2.new(0, 300, 0, 180)
KeyFrame.Position = UDim2.new(0.5, -150, 0.5, -90)
KeyFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
KeyFrame.BorderSizePixel = 0
KeyFrame.Active = true
KeyFrame.Draggable = true
KeyFrame.Parent = KeyGui
Instance.new("UICorner", KeyFrame).CornerRadius = UDim.new(0, 10)

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Size = UDim2.new(1, 0, 0, 40)
KeyTitle.BackgroundTransparency = 1
KeyTitle.Text = "ROCKET | Авторизация"
KeyTitle.TextColor3 = Color3.fromRGB(0, 255, 128)
KeyTitle.TextSize = 16
KeyTitle.Font = Enum.Font.SourceSansBold
KeyTitle.Parent = KeyFrame

local KeyBox = Instance.new("TextBox")
KeyBox.Size = UDim2.new(0, 260, 0, 35)
KeyBox.Position = UDim2.new(0, 20, 0, 55)
KeyBox.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
KeyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
KeyBox.PlaceholderText = "Введите ключ..."
KeyBox.TextSize = 14
KeyBox.Font = Enum.Font.SourceSansBold
KeyBox.Parent = KeyFrame
Instance.new("UICorner", KeyBox).CornerRadius = UDim.new(0, 6)

local SubmitBtn = Instance.new("TextButton")
SubmitBtn.Size = UDim2.new(0, 260, 0, 35)
SubmitBtn.Position = UDim2.new(0, 20, 0, 100)
SubmitBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 90)
SubmitBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
SubmitBtn.TextSize = 14
SubmitBtn.Font = Enum.Font.SourceSansBold
SubmitBtn.Text = "Подтвердить ключ"
SubmitBtn.Parent = KeyFrame
Instance.new("UICorner", SubmitBtn).CornerRadius = UDim.new(0, 6)

local GetKeyBtn = Instance.new("TextButton")
GetKeyBtn.Size = UDim2.new(0, 260, 0, 25)
GetKeyBtn.Position = UDim2.new(0, 20, 0, 142)
GetKeyBtn.BackgroundTransparency = 1
GetKeyBtn.TextColor3 = Color3.fromRGB(150, 150, 170)
GetKeyBtn.TextSize = 12
GetKeyBtn.Font = Enum.Font.SourceSansBold
GetKeyBtn.Text = "Получить ключ (Telegram)"
GetKeyBtn.Parent = KeyFrame

GetKeyBtn.MouseButton1Click:Connect(function()
    setclipboard("https://t.me/RocketWay")
    GetKeyBtn.Text = "Ссылка скопирована!"
    task.wait(1.5)
    GetKeyBtn.Text = "Получить ключ (Telegram)"
end)

SubmitBtn.MouseButton1Click:Connect(function()
    if KeyBox.Text == CORRECT_KEY then
        isAuthorized = true
        KeyGui:Destroy()
        LoadMainScript()
    else
        SubmitBtn.Text = "Неверный ключ!"
        SubmitBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        task.wait(1.5)
        SubmitBtn.Text = "Подтвердить ключ"
        SubmitBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 90)
    end
end)

-- [ОСНОВНОЙ СКРИПТ (Запускается после ввода ключа)]
function LoadMainScript()
    getgenv().RocketConfig = {
        SpeedEnabled = false,
        SpeedValue = 200,
        DashMultiplier = 2.5,
        CoinFarm = false,
        ESPEnabled = false,
        PublicVisuals = true,
        AntiBanBypass = true,
        CurrentWeather = "Normal"
    }

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "RocketEvadeUltimate"
    ScreenGui.Parent = CoreGui

    local MainFrame = Instance.new("Frame")
    MainFrame.Size = UDim2.new(0, 340, 0, 570)
    MainFrame.Position = UDim2.new(0.5, -170, 0.5, -285)
    MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
    MainFrame.BorderSizePixel = 0
    MainFrame.Active = true
    MainFrame.Draggable = true
    MainFrame.Parent = ScreenGui
    Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 10)

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.BackgroundTransparency = 1
    Title.Text = "ROCKET | Evade Ultimate Pro"
    Title.TextColor3 = Color3.fromRGB(0, 255, 128)
    Title.TextSize = 16
    Title.Font = Enum.Font.SourceSansBold
    Title.Parent = MainFrame

    local function createToggle(name, yPos, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 300, 0, 32)
        btn.Position = UDim2.new(0, 20, 0, yPos)
        btn.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.TextSize = 13
        btn.Font = Enum.Font.SourceSansBold
        btn.Text = name .. ": OFF"
        btn.Parent = MainFrame
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
        
        local state = false
        btn.MouseButton1Click:Connect(function()
            state = not state
            btn.BackgroundColor3 = state and Color3.fromRGB(0, 180, 90) or Color3.fromRGB(30, 30, 40)
            btn.Text = name .. (state and ": ON" or ": OFF")
            callback(state)
        end)
    end

    local function createTextBox(name, yPos, defaultVal, callback)
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(0, 160, 0, 32)
        label.Position = UDim2.new(0, 20, 0, yPos)
        label.BackgroundTransparency = 1
        label.TextColor3 = Color3.fromRGB(255, 255, 255)
        label.TextSize = 12
        label.Font = Enum.Font.SourceSansBold
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Text = name
        label.Parent = MainFrame
        
        local box = Instance.new("TextBox")
        box.Size = UDim2.new(0, 130, 0, 32)
        box.Position = UDim2.new(0, 190, 0, yPos)
        box.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        box.TextColor3 = Color3.fromRGB(0, 255, 128)
        box.TextSize = 13
        box.Font = Enum.Font.SourceSansBold
        box.Text = tostring(defaultVal)
        box.Parent = MainFrame
        Instance.new("UICorner", box).CornerRadius = UDim.new(0, 6)
        
        box.FocusLost:Connect(function()
            local num = tonumber(box.Text)
            if num then callback(num) else box.Text = tostring(defaultVal) callback(defaultVal) end
        end)
    end

    -- Элементы интерфейса
    createToggle("Спидхак (200+)", 45, function(state) getgenv().RocketConfig.SpeedEnabled = state end)
    createTextBox("Скорость:", 82, 200, function(val) getgenv().RocketConfig.SpeedValue = val end)
    createTextBox("Множитель Дэша:", 119, 2.5, function(val) getgenv().RocketConfig.DashMultiplier = val end)
    createToggle("Визуальная синхронизация", 156, function(state) getgenv().RocketConfig.PublicVisuals = state end)
    createToggle("Автосбор монет", 193, function(state) getgenv().RocketConfig.CoinFarm = state end)
    createToggle("ВХ (Игроки + Nextbots)", 230, function(state) getgenv().RocketConfig.ESPEnabled = state end)

    -- Меню выбора погоды в Evade
    local WeatherLabel = Instance.new("TextLabel")
    WeatherLabel.Size = UDim2.new(0, 300, 0, 20)
    WeatherLabel.Position = UDim2.new(0, 20, 0, 268)
    WeatherLabel.BackgroundTransparency = 1
    WeatherLabel.TextColor3 = Color3.fromRGB(150, 150, 170)
    WeatherLabel.TextSize = 12
    WeatherLabel.Font = Enum.Font.SourceSansBold
    WeatherLabel.Text = "Выбор погоды / Освещения в Evade:"
    WeatherLabel.Parent = MainFrame

    local weatherTypes = {"Normal", "Foggy", "Dark (Night)", "Sunset", "Blizzard"}
    local weatherIdx = 1

    local WeatherBtn = Instance.new("TextButton")
    WeatherBtn.Size = UDim2.new(0, 300, 0, 32)
    WeatherBtn.Position = UDim2.new(0, 20, 0, 292)
    WeatherBtn.BackgroundColor3 = Color3.fromRGB(50, 40, 70)
    WeatherBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    WeatherBtn.TextSize = 13
    WeatherBtn.Font = Enum.Font.SourceSansBold
    WeatherBtn.Text = "Погода: Normal (Нажмите для смены)"
    WeatherBtn.Parent = MainFrame
    Instance.new("UICorner", WeatherBtn).CornerRadius = UDim.new(0, 6)

    WeatherBtn.MouseButton1Click:Connect(function()
        weatherIdx = weatherIdx + 1
        if weatherIdx > #weatherTypes then weatherIdx = 1 end
        local selected = weatherTypes[weatherIdx]
        WeatherBtn.Text = "Погода: " .. selected
        
        pcall(function()
            if selected == "Normal" then
                Lighting.ClockTime = 14
                Lighting.Brightness = 2
                Lighting.FogEnd = 100000
                Lighting.Ambient = Color3.fromRGB(128, 128, 128)
            elseif selected == "Foggy" then
                Lighting.ClockTime = 10
                Lighting.Brightness = 1
                Lighting.FogEnd = 350
                Lighting.Ambient = Color3.fromRGB(90, 90, 90)
            elseif selected == "Dark (Night)" then
                Lighting.ClockTime = 0
                Lighting.Brightness = 0.2
                Lighting.FogEnd = 200
                Lighting.Ambient = Color3.fromRGB(15, 15, 20)
            elseif selected == "Sunset" then
                Lighting.ClockTime = 18.5
                Lighting.Brightness = 1.5
                Lighting.FogEnd = 5000
                Lighting.Ambient = Color3.fromRGB(200, 100, 50)
            elseif selected == "Blizzard" then
                Lighting.ClockTime = 12
                Lighting.Brightness = 1.2
                Lighting.FogEnd = 150
                Lighting.Ambient = Color3.fromRGB(200, 220, 255)
            end
        end)
    end)

    -- Блок связи
    local SocialLabel = Instance.new("TextLabel")
    SocialLabel.Size = UDim2.new(0, 300, 0, 20)
    SocialLabel.Position = UDim2.new(0, 20, 0, 332)
    SocialLabel.BackgroundTransparency = 1
    SocialLabel.TextColor3 = Color3.fromRGB(150, 150, 170)
    SocialLabel.TextSize = 12
    SocialLabel.Font = Enum.Font.SourceSansBold
    SocialLabel.Text = "Сообщество (Rocket Way):"
    SocialLabel.Parent = MainFrame

    local TgBtn = Instance.new("TextButton")
    TgBtn.Size = UDim2.new(0, 145, 0, 28)
    TgBtn.Position = UDim2.new(0, 20, 0, 355)
    TgBtn.BackgroundColor3 = Color3.fromRGB(0, 136, 204)
    TgBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    TgBtn.TextSize = 12
    TgBtn.Font = Enum.Font.SourceSansBold
    TgBtn.Text = "Telegram"
    TgBtn.Parent = MainFrame
    Instance.new("UICorner", TgBtn).CornerRadius = UDim.new(0, 6)
    TgBtn.MouseButton1Click:Connect(function() setclipboard("https://t.me/RocketWay") end)

    local DcBtn = Instance.new("TextButton")
    DcBtn.Size = UDim2.new(0, 145, 0, 28)
    DcBtn.Position = UDim2.new(0, 175, 0, 355)
    DcBtn.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
    DcBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    DcBtn.TextSize = 12
    DcBtn.Font = Enum.Font.SourceSansBold
    DcBtn.Text = "Discord"
    DcBtn.Parent = MainFrame
    Instance.new("UICorner", DcBtn).CornerRadius = UDim.new(0, 6)
    DcBtn.MouseButton1Click:Connect(function() setclipboard("https://discord.gg/RocketWay") end)

    local ToggleUIBtn = Instance.new("TextButton")
    ToggleUIBtn.Size = UDim2.new(0, 300, 0, 35)
    ToggleUIBtn.Position = UDim2.new(0, 20, 0, 520)
    ToggleUIBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 60)
    ToggleUIBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleUIBtn.TextSize = 13
    ToggleUIBtn.Font = Enum.Font.SourceSansBold
    ToggleUIBtn.Text = "Скрыть / Показать меню"
    ToggleUIBtn.Parent = MainFrame
    Instance.new("UICorner", ToggleUIBtn).CornerRadius = UDim.new(0, 6)

    local uiVisible = true
    ToggleUIBtn.MouseButton1Click:Connect(function()
        uiVisible = not uiVisible
        MainFrame.Size = uiVisible and UDim2.new(0, 340, 0, 570) or UDim2.new(0, 340, 0, 90)
        for _, child in pairs(MainFrame:GetChildren()) do
            if child ~= Title and child ~= ToggleUIBtn then child.Visible = uiVisible end
        end
        ToggleUIBtn.Visible = true
    end)

    -- Антибан обход
    pcall(function()
        local mt = getrawmetatable(game)
        setreadonly(mt, false)
        local oldIndex = mt.__index
        mt.__index = newcclosure(function(self, k)
            if not checkcaller() and (k == "WalkSpeed" or k == "JumpPower") then return 16 end
            return oldIndex(self, k)
        end)
        setreadonly(mt, true)
    end)

    -- Логика движения, спидхака, деша и анимаций
    RunService.Heartbeat:Connect(function(dt)
        pcall(function()
            local char = LocalPlayer.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChild("Humanoid")
            
            if root and hum then
                for _, track in pairs(hum:GetPlayingAnimationTracks()) do
                    if track.Speed == 0 then track:AdjustSpeed(1) end
                end
                
                if getgenv().RocketConfig.SpeedEnabled and hum.MoveDirection.Magnitude > 0 then
                    local currentSpeed = getgenv().RocketConfig.SpeedValue
                    if hum:GetState() == Enum.HumanoidStateType.Freefall or hum.Jump then
                        currentSpeed = currentSpeed * getgenv().RocketConfig.DashMultiplier
                    end

                    root.CFrame = root.CFrame + (hum.MoveDirection * (currentSpeed * dt))
                    
                    if getgenv().RocketConfig.PublicVisuals then
                        root.AssemblyLinearVelocity = hum.MoveDirection * currentSpeed
                    end
                end
            end
        end)
    end)

    -- Автосбор
    task.spawn(function()
        while task.wait(0.3) do
            if getgenv().RocketConfig.CoinFarm then
                pcall(function()
                    for _, obj in pairs(Workspace:GetDescendants()) do
                        if obj:IsA("BasePart") and (obj.Name:lower():find("coin") or obj.Name:lower():find("pickup") or obj.Name:lower():find("cash") or obj.Name:lower():find("ticket")) then
                            local char = LocalPlayer.Character
                            if char and char:FindFirstChild("HumanoidRootPart") then
                                char.HumanoidRootPart.CFrame = obj.CFrame + Vector3.new(0, 2.5, 0)
                                task.wait(0.05)
                            end
                        end
                    end
                end)
            end
        end
    end)

    -- ВХ
    local function createESP(object, color, textString)
        if object:FindFirstChild("RocketESP") then return end
        local bill = Instance.new("BillboardGui")
        bill.Name = "RocketESP"
        bill.Size = UDim2.new(0, 100, 0, 40)
        bill.AlwaysOnTop = true
        bill.StudsOffset = Vector3.new(0, 3, 0)
        
        local text = Instance.new("TextLabel")
        text.Size = UDim2.new(1, 0, 1, 0)
        text.BackgroundTransparency = 1
        text.TextColor3 = color
        text.TextScaled = true
        text.Font = Enum.Font.SourceSansBold
        text.TextStrokeTransparency = 0
        text.Text = textString
        text.Parent = bill
        bill.Parent = object
    end

    RunService.RenderStepped:Connect(function()
        if not getgenv().RocketConfig.ESPEnabled then return end
        pcall(function()
            for _, player in pairs(Players:GetPlayers()) do
                if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
                    createESP(player.Character.Head, Color3.fromRGB(0, 255, 128), player.Name)
                end
            end
            if Workspace:FindFirstChild("Game") and Workspace.Game:FindFirstChild("Nextbots") then
                for _, bot in pairs(Workspace.Game.Nextbots:GetChildren()) do
                    if bot:FindFirstChild("HumanoidRootPart") then
                        createESP(bot.HumanoidRootPart, Color3.fromRGB(255, 0, 0), "⚠️ NEXTBOT")
                    end
                end
            end
        end)
    end)
    
    print("ROCKET | Скрипт успешно разблокирован по ключу!")
end
