-- Combined Script (Local и Server код)
-- Поместите этот скрипт в ServerScriptService

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- Название валюты (измените, если необходимо)
local currencyName = "Корм"

-- Создаем RemoteEvent (если его еще нет)
local FarmEvent = ReplicatedStorage:FindFirstChild("RemoteEvents") and ReplicatedStorage.RemoteEvents:FindFirstChild("FarmNearest")
if not FarmEvent then
    local remoteEventsFolder = ReplicatedStorage:FindFirstChild("RemoteEvents") or Instance.new("Folder")
    remoteEventsFolder.Name = "RemoteEvents"
    remoteEventsFolder.Parent = ReplicatedStorage

    FarmEvent = Instance.new("RemoteEvent")
    FarmEvent.Name = "FarmNearest"
    FarmEvent.Parent = remoteEventsFolder
end

-- Функция для получения leaderstats
local function getLeaderstats(player)
    return player:WaitForChild("leaderstats", 5)
end

-- Функция для добавления валюты
local function addCurrency(player, amount)
    local leaderstats = getLeaderstats(player)
    if leaderstats then
        local currency = leaderstats:FindFirstChild(currencyName)
        if currency then
            currency.Value = currency.Value + amount
        else
            print("Валюта не найдена в leaderstats")
        end
    else
        print("leaderstats не найден")
    end
end

-- Функция для фарма ближайших объектов
local function farmNearest(player)
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

    local nearestObject = nil
    local nearestDistance = math.huge
    local itemsFolder = game.Workspace:FindFirstChild("Items")

    if not itemsFolder then
        print("Папка Items не найдена")
        return
    end

    for _, object in pairs(itemsFolder:GetChildren()) do
        if (object:IsA("BasePart") or object:IsA("Model")) then
            local canFarm = object:GetAttribute("CanFarm")
            if canFarm == true then
                local distance = (humanoidRootPart.Position - object.Position).Magnitude
                if distance < nearestDistance then
                    nearestObject = object
                    nearestDistance = distance
                end
            end
        end
    end

    if nearestObject then
        -- Выдаем валюту

        local rewardAmount = 5 -- Размер награды
        addCurrency(player, rewardAmount)
        print("Игрок " .. player.Name .. " получил " .. rewardAmount .. " " .. currencyName .. " за фарм " .. nearestObject.Name)
    else
        print("Нет объектов для фарма поблизости")
    end
end

-- Server-side: Привязка RemoteEvent к функции фарма
FarmEvent.OnServerEvent:Connect(farmNearest)


-- Client-side GUI (запускается только на клиенте)
Players.PlayerGui:Connect(function(playerGui)
    local localPlayer = Players.LocalPlayer

    -- Меню
    local MainMenuGui = Instance.new("ScreenGui")
    MainMenuGui.Name = "MainMenu"
    MainMenuGui.Parent = playerGui
    MainMenuGui.ResetOnSpawn = false
    MainMenuGui.Enabled = false -- Сначала меню скрыто

    local MainMenuFrame = Instance.new("Frame")
    MainMenuFrame.Size = UDim2.new(0, 200, 0, 250)
    MainMenuFrame.Position = UDim2.new(0.5, -100, 0.5, -125)
    MainMenuFrame.BackgroundTransparency = 0.2
    MainMenuFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    MainMenuFrame.BorderSizePixel = 0
    MainMenuFrame.Parent = MainMenuGui

    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Size = UDim2.new(1, 0, 0.2, 0)
    TitleLabel.Position = UDim2.new(0, 0, 0, 0)
    TitleLabel.Text = "Главное меню"
    TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.TextScaled = true
    TitleLabel.Font = Enum.Font.SourceSansBold
    TitleLabel.Parent = MainMenuFrame

    -- Кнопка Near Farm
    local nearFarmButton = Instance.new("TextButton")
    nearFarmButton.Size = UDim2.new(1, -20, 0.15, 0)
    nearFarmButton.Position = UDim2.new(0, 10, 0.25, 0)
    nearFarmButton.Text = "Near Farm"
    nearFarmButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    nearFarmButton.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    nearFarmButton.BorderSizePixel = 0
    nearFarmButton.TextScaled = true
    nearFarmButton.Font = Enum.Font.SourceSansBold
    nearFarmButton.Parent = MainMenuFrame

    -- Функция открытия/закрытия меню (при нажатии кнопки, например)
    local function toggleMenu()
        MainMenuGui.Enabled = not MainMenuGui.Enabled
    end

    -- Client-side: Привязка к кнопке (замените KeyCode на нужную клавишу)
    UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
        if gameProcessedEvent then return end
        if input.KeyCode == Enum.KeyCode.M then -- Клавиша "M" для открытия меню
            toggleMenu()
        end
    end)

    -- Client-side: Функция при нажатии кнопки Near Farm
    nearFarmButton.MouseButton1Click:Connect(function()
        print("Нажата кнопка Near Farm")
        FarmEvent:FireServer()
    end)

    -- Предотвращаем выполнение GUI-кода на сервере
    if RunService:IsServer() then
        MainMenuGui:Destroy()
    end
end)

print("Объединенный скрипт загружен")
