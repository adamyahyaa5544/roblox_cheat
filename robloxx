-- Rivals ESP v3.0 - RightShift Menu Toggle

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- ESP Configuration
local ESP = {
    Enabled = true,
    ShowHealth = true,
    ShowDistance = true,
    MaxDistance = 500,
    TeamCheck = false,
    Boxes = true,
    Tracers = false,
    Players = {}
}

-- Create GUI if not already exists
if not game:GetService("CoreGui"):FindFirstChild("RivalsESPGui") then
    local GUI = Instance.new("ScreenGui")
    GUI.Name = "RivalsESPGui"
    GUI.Parent = game:GetService("CoreGui")
    GUI.ResetOnSpawn = false

    local MenuFrame = Instance.new("Frame")
    MenuFrame.Name = "ESPMenu"
    MenuFrame.Size = UDim2.new(0, 250, 0, 300)
    MenuFrame.Position = UDim2.new(0.5, -125, 0.5, -150)
    MenuFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
    MenuFrame.BorderSizePixel = 0
    MenuFrame.Visible = false
    MenuFrame.Active = true
    MenuFrame.Draggable = true
    MenuFrame.Parent = GUI

    local Title = Instance.new("TextLabel")
    Title.Text = "RIVALS ESP v3.0"
    Title.Size = UDim2.new(1, 0, 0, 30)
    Title.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
    Title.TextColor3 = Color3.new(1, 1, 1)
    Title.Font = Enum.Font.GothamBold
    Title.Parent = MenuFrame
end

-- Get references
local GUI = game:GetService("CoreGui").RivalsESPGui
local MenuFrame = GUI:FindFirstChild("ESPMenu")

-- Toggle Menu with RightShift
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.UserInputType == Enum.UserInputType.Keyboard then
        if input.KeyCode == Enum.KeyCode.RightShift then
            MenuFrame.Visible = not MenuFrame.Visible
            print("[ESP] Menu Toggled:", MenuFrame.Visible)
        end
    end
end)

-- ESP Functions
local function CreateESP(player)
    if player == LocalPlayer then return end
    local ESPData = {
        Box = Drawing.new("Square"),
        NameTag = Drawing.new("Text"),
        HealthBar = Drawing.new("Line"),
        HealthText = Drawing.new("Text"),
        Tracer = Drawing.new("Line")
    }
    ESPData.Box.Thickness = 1
    ESPData.Box.Filled = false
    ESPData.NameTag.Size = 18
    ESPData.NameTag.Center = true
    ESPData.NameTag.Outline = true
    ESPData.HealthBar.Thickness = 3
    ESPData.Tracer.Thickness = 1
    ESP.Players[player] = ESPData
end

local function UpdateESP()
    if not LocalPlayer.Character or not LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
    for player, esp in pairs(ESP.Players) do
        if not player or not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then
            for _, drawing in pairs(esp) do drawing.Visible = false end
            goto continue
        end
        local rootPart = player.Character.HumanoidRootPart
        local head = player.Character:FindFirstChild("Head")
        local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
        if not rootPart or not head or not humanoid then goto continue end
        local rootPos, rootVis = Camera:WorldToViewportPoint(rootPart.Position)
        local headPos = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 1, 0))
        if not rootVis then goto continue end
        local distance = (LocalPlayer.Character.HumanoidRootPart.Position - rootPart.Position).Magnitude
        if distance > ESP.MaxDistance then goto continue end
        local height = (headPos.Y - rootPos.Y) * 1.5
        local width = height * 0.65
        local teamColor = Color3.new(1, 0, 0)
        if ESP.TeamCheck and player.Team == LocalPlayer.Team then
            teamColor = Color3.new(0, 1, 0)
        end
        esp.Box.Size = Vector2.new(width, height)
        esp.Box.Position = Vector2.new(rootPos.X - width/2, rootPos.Y - height/2)
        esp.Box.Color = teamColor
        esp.Box.Visible = ESP.Enabled and ESP.Boxes
        esp.NameTag.Text = player.Name .. (ESP.ShowDistance and (" [" .. math.floor(distance) .. "m]") or "")
        esp.NameTag.Position = Vector2.new(rootPos.X, rootPos.Y - height/2 - 20)
        esp.NameTag.Color = teamColor
        esp.NameTag.Visible = ESP.Enabled
        if ESP.ShowHealth then
            local healthPercent = humanoid.Health / humanoid.MaxHealth
            local healthY = rootPos.Y + height/2 + 5
            esp.HealthBar.From = Vector2.new(rootPos.X - width/2, healthY)
            esp.HealthBar.To = Vector2.new(rootPos.X - width/2 + width * healthPercent, healthY)
            esp.HealthBar.Color = Color3.new(1 - healthPercent, healthPercent, 0)
            esp.HealthBar.Visible = ESP.Enabled
            esp.HealthText.Text = math.floor(humanoid.Health) .. "HP"
            esp.HealthText.Position = Vector2.new(rootPos.X + width/2 + 10, healthY - 10)
            esp.HealthText.Color = Color3.new(1, 1, 1)
            esp.HealthText.Visible = ESP.Enabled
        end
        if ESP.Tracers then
            esp.Tracer.From = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y)
            esp.Tracer.To = Vector2.new(rootPos.X, rootPos.Y)
            esp.Tracer.Color = teamColor
            esp.Tracer.Visible = ESP.Enabled
        end
        ::continue::
    end
end

Players.PlayerAdded:Connect(CreateESP)
Players.PlayerRemoving:Connect(function(player)
    if ESP.Players[player] then
        for _, drawing in pairs(ESP.Players[player]) do drawing:Remove() end
        ESP.Players[player] = nil
    end
end)
for _, player in ipairs(Players:GetPlayers()) do CreateESP(player) end

-- Create Menu Controls
if not MenuFrame:FindFirstChild("ESPToggle") then
    local yOffset = 40
    local function CreateToggle(text, configKey, yPos)
        local toggle = Instance.new("TextButton")
        toggle.Name = "ESPToggle"
        toggle.Text = text
        toggle.Size = UDim2.new(0.8, 0, 0, 30)
        toggle.Position = UDim2.new(0.1, 0, 0, yPos)
        toggle.BackgroundColor3 = ESP[configKey] and Color3.fromRGB(0, 80, 0) or Color3.fromRGB(80, 0, 0)
        toggle.TextColor3 = Color3.new(1, 1, 1)
        toggle.Font = Enum.Font.Gotham
        toggle.Parent = MenuFrame
        toggle.MouseButton1Click:Connect(function()
            ESP[configKey] = not ESP[configKey]
            toggle.BackgroundColor3 = ESP[configKey] and Color3.fromRGB(0, 80, 0) or Color3.fromRGB(80, 0, 0)
            toggle.Text = text .. ": " .. (ESP[configKey] and "ON" or "OFF")
        end)
        toggle.Text = text .. ": " .. (ESP[configKey] and "ON" or "OFF")
        return toggle
    end

    CreateToggle("ESP Master", "Enabled", yOffset)
    CreateToggle("Show Health", "ShowHealth", yOffset + 40)
    CreateToggle("Team Check", "TeamCheck", yOffset + 80)
    CreateToggle("Box ESP", "Boxes", yOffset + 120)
    CreateToggle("Tracers", "Tracers", yOffset + 160)
    CreateToggle("Show Distance", "ShowDistance", yOffset + 200)

    local distanceSlider = Instance.new("TextLabel")
    distanceSlider.Text = "Max Distance: " .. ESP.MaxDistance
    distanceSlider.Size = UDim2.new(0.8, 0, 0, 20)
    distanceSlider.Position = UDim2.new(0.1, 0, 0, yOffset + 240)
    distanceSlider.TextColor3 = Color3.new(1, 1, 1)
    distanceSlider.BackgroundTransparency = 1
    distanceSlider.Font = Enum.Font.Gotham
    distanceSlider.Parent = MenuFrame

    local slider = Instance.new("TextBox")
    slider.Text = tostring(ESP.MaxDistance)
    slider.Size = UDim2.new(0.8, 0, 0, 30)
    slider.Position = UDim2.new(0.1, 0, 0, yOffset + 260)
    slider.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    slider.TextColor3 = Color3.new(1, 1, 1)
    slider.Font = Enum.Font.Gotham
    slider.Parent = MenuFrame

    slider.FocusLost:Connect(function()
        local num = tonumber(slider.Text)
        if num then
            ESP.MaxDistance = math.clamp(num, 50, 2000)
            slider.Text = tostring(ESP.MaxDistance)
            distanceSlider.Text = "Max Distance: " .. ESP.MaxDistance
        else
            slider.Text = tostring(ESP.MaxDistance)
        end
    end)
end

RunService.RenderStepped:Connect(UpdateESP)
print("[RIVALS ESP] Loaded! Press RightShift to toggle the menu.")
