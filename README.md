local CoreGui = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local Terrain = workspace:FindFirstChildOfClass("Terrain")
local Players = game:GetService("Players")

if CoreGui:FindFirstChild("FixLagCustomGui") then
    CoreGui:FindFirstChild("FixLagCustomGui"):Destroy()
end

local ScreenGui = Instance.new("ScreenGui", CoreGui)
ScreenGui.Name = "FixLagCustomGui"
ScreenGui.ResetOnSpawn = false

local ToggleButton = Instance.new("TextButton", ScreenGui)
ToggleButton.Size = UDim2.new(0, 50, 0, 50)
ToggleButton.Position = UDim2.new(0.05, 0, 0.2, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
ToggleButton.Text = "Menu"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Font = Enum.Font.Code
ToggleButton.TextSize = 14

local ToggleCorner = Instance.new("UICorner", ToggleButton)
ToggleCorner.CornerRadius = UDim.new(1, 0)

local ToggleStroke = Instance.new("UIStroke", ToggleButton)
ToggleStroke.Color = Color3.fromRGB(255, 255, 255)
ToggleStroke.Thickness = 1.5

local FpsToggleLabel = Instance.new("TextLabel", ToggleButton)
FpsToggleLabel.Size = UDim2.new(1, 0, 0, 15)
FpsToggleLabel.Position = UDim2.new(0, 0, 1, 2)
FpsToggleLabel.BackgroundTransparency = 1
FpsToggleLabel.Text = "..."
FpsToggleLabel.Font = Enum.Font.Code
FpsToggleLabel.TextSize = 11

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 180, 0, 230)
MainFrame.Position = UDim2.new(0.5, -90, 0.5, -115)
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 0
MainFrame.Visible = true

local MainCorner = Instance.new("UICorner", MainFrame)
MainCorner.CornerRadius = UDim.new(0, 8)

local MainStroke = Instance.new("UIStroke", MainFrame)
MainStroke.Color = Color3.fromRGB(40, 40, 40)
MainStroke.Thickness = 1

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Fix Lag"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.Code
Title.TextSize = 14

local FpsLabel = Instance.new("TextLabel", MainFrame)
FpsLabel.Size = UDim2.new(1, 0, 0, 20)
FpsLabel.Position = UDim2.new(0, 0, 0, 30)
FpsLabel.BackgroundTransparency = 1
FpsLabel.Text = "FPS: ..."
FpsLabel.Font = Enum.Font.Code
FpsLabel.TextSize = 12

local function CreateButton(text, pos, callback)
    local btn = Instance.new("TextButton", MainFrame)
    btn.Size = UDim2.new(0, 150, 0, 30)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Code
    btn.TextSize = 12
    
    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 4)
    
    local stroke = Instance.new("UIStroke", btn)
    stroke.Color = Color3.fromRGB(60, 60, 60)
    stroke.Thickness = 1
    
    btn.MouseButton1Click:Connect(callback)
    return btn
end

local function MakeDraggable(obj)
    local dragging, dragInput, dragStart, startPos
    obj.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = obj.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    obj.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            obj.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

MakeDraggable(ToggleButton)
MakeDraggable(MainFrame)

ToggleButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

task.spawn(function()
    local StartTime = os.clock()
    local Frames = 0
    while task.wait() do
        Frames = Frames + 1
        local Now = os.clock()
        if Now - StartTime >= 1 then
            FpsLabel.Text = "FPS: " .. tostring(Frames)
            FpsToggleLabel.Text = tostring(Frames) .. " FPS"
            Frames = 0
            StartTime = Now
        end
    end
end)

task.spawn(function()
    while task.wait() do
        local hue = (os.clock() % 3) / 3
        local rainbowColor = Color3.fromHSV(hue, 1, 1)
        FpsLabel.TextColor3 = rainbowColor
        FpsToggleLabel.TextColor3 = rainbowColor
    end
end)

CreateButton("Fix Lag 1", UDim2.new(0, 15, 0, 60), function()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level05
    Lighting.GlobalShadows = false
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Clouds") or v:IsA("Sky") then v:Destroy() end
    end
    if Terrain then
        Terrain.WaterWaveSize = 0
        Terrain.WaterWaveSpeed = 0
        Terrain.WaterReflectance = 0
        Terrain.WaterTransparency = 0
    end
end)

CreateButton("Fix Lag 2", UDim2.new(0, 15, 0, 100), function()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    Lighting.GlobalShadows = false
    for _, v in pairs(Lighting:GetChildren()) do
        if v:IsA("Clouds") or v:IsA("Sky") then v:Destroy() end
    end
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Material = Enum.Material.SmoothPlastic
        end
    end
end)

CreateButton("Fix Lag 3", UDim2.new(0, 15, 0, 140), function()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    Lighting.GlobalShadows = false
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name:lower():find("tree") or v.Name:lower():find("leaf") then
            v:Destroy()
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Sparkles") then
            v.Enabled = false
        elseif v:IsA("BasePart") then
            v.Material = Enum.Material.SmoothPlastic
            v.Color = Color3.fromRGB(150, 150, 150)
        end
    end
end)

CreateButton("Fix Lag 4", UDim2.new(0, 15, 0, 180), function()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    Lighting.GlobalShadows = false
    
    local lp = Players.LocalPlayer
    local char = lp.Character or lp.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    
    -- Tạo sàn vô hình để nhân vật đứng vững không rơi xuống biển/vực
    local Floor = Instance.new("Part", workspace)
    Floor.Size = Vector3.new(50000, 1, 50000)
    Floor.Position = Vector3.new(hrp.Position.X, hrp.Position.Y - 3.5, hrp.Position.Z)
    Floor.Anchored = true
    Floor.Transparency = 0.8
    Floor.Color = Color3.fromRGB(0, 0, 0)
    Floor.CanCollide = true
    
    local function IsImportant(obj)
        if obj:IsDescendantOf(char) then return true end
        if obj.Name == "Enemies" or obj.Name == "NPCs" or obj.Name == "NPC" then return true end
        if obj:FindFirstChildOfClass("Humanoid") then return true end
        if obj:IsA("Tool") or obj:IsA("Weapon") then return true end
        if Players:GetPlayerFromCharacter(obj) then return true end
        return false
    end

    -- Ẩn map và giữ lại Quái vật, NPC, Vũ khí
    for _, v in pairs(workspace:GetDescendants()) do
        if v ~= char and v ~= Floor and not v:IsDescendantOf(char) then
            if v:IsA("BasePart") and not IsImportant(v) then
                if v.Name == "Water" or v.Parent.Name == "Sea" or v.Name:lower():find("map") then
                    pcall(function() v:Destroy() end)
                else
                    v.Transparency = 1
                    v.CanCollide = false
                end
            elseif (v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Sparkles")) and not IsImportant(v) then
                v.Enabled = false
            end
        end
    end
    
    workspace.DescendantAdded:Connect(function(v)
        task.wait()
        if v ~= char and v ~= Floor and not IsImportant(v) then
            if v:IsA("BasePart") then
                v.Transparency = 1
                v.CanCollide = false
            elseif v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Sparkles") then
                v.Enabled = false
            end
        end
    end)
    
    game:GetService("Lighting").ChildAdded:Connect(function(child)
        child:Destroy()
    end)

    -- ĐOẠN CODE XÓA THÔNG BÁO (EXP, TIỀN, THÔNG THẠO) ĐƯỢC THÊM VÀO ĐÂY:
    task.spawn(function()
        local pgui = lp:WaitForChild("PlayerGui")
        while task.wait(0.1) do
            pcall(function()
                -- Xóa khung thông báo lớn ở giữa màn hình (như ảnh vẽ)
                if pgui:FindFirstChild("Notifications") then
                    pgui.Notifications:Destroy()
                end
                -- Xóa các text thông báo hiệu ứng nhảy chữ danh sách thưởng
                local labels = pgui:FindFirstChild("Labels")
                if labels then
                    for _, v in pairs(labels:GetChildren()) do
                        if v:IsA("TextLabel") or v:IsA("Frame") then
                            v:Destroy()
                        end
                    end
                end
            end)
        end
    end)
end)
