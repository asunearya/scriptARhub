# scriptARhub
-- =================================================================
-- DELTA EXECUTOR GUI SCRIPT (TOGGLE + MENU LIST)
-- =================================================================

local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")
local player = Players.LocalPlayer

-- Mencegah GUI duplikat jika script dijalankan 2x
if CoreGui:FindFirstChild("DeltaCustomHub") then
	CoreGui.DeltaCustomHub:Destroy()
end

-- 1. MEMBUAT SCREEN GUI UTAMA DI CORE GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "DeltaCustomHub"
screenGui.ResetOnSpawn = false
screenGui.Parent = CoreGui

-- 2. MEMBUAT TOMBOL BUKA/TUTUP (TOGGLE BUTTON MELAYANG)
local toggleButton = Instance.new("TextButton")
toggleButton.Name = "ToggleButton"
toggleButton.Size = UDim2.new(0, 75, 0, 30)
toggleButton.Position = UDim2.new(0, 20, 0, 150) -- Bisa digeser atau diletakkan di pinggir
toggleButton.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
toggleButton.Text = "AR HUB"
toggleButton.TextSize = 10
toggleButton.TextColor3 = Color3.fromRGB(255, 0, 0)
toggleButton.Parent = screenGui

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 10)
toggleCorner.Parent = toggleButton

-- Membuat tombol bisa digeser (Draggable) di HP/PC agar tidak menutupi tombol game
local dragging, dragInput, dragStart, startPos
toggleButton.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = toggleButton.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

toggleButton.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

game:GetService("UserInputService").InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		toggleButton.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

-- 3. MEMBUAT JENDELA UTAMA (MAIN FRAME)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 300, 0, 350)
mainFrame.Position = UDim2.new(0.5, -150, 0.5, -175)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.BorderSizePixel = 0
mainFrame.Visible = false -- Awalnya tertutup supaya tidak mengganggu
mainFrame.Parent = screenGui

local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0, 12)
frameCorner.Parent = mainFrame

-- Border tipis keren untuk jendela
local frameStroke = Instance.new("UIStroke")
frameStroke.Color = Color3.fromRGB(80, 80, 80)
frameStroke.Thickness = 1.5
frameStroke.Parent = mainFrame

-- 4. JUDUL GUI
local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "TitleLabel"
titleLabel.Size = UDim2.new(1, 0, 0, 45)
titleLabel.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
titleLabel.Text = "⚡ AR HUB ⚡"
titleLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
titleLabel.TextSize = 18
titleLabel.Font = Enum.Font.GothamBold
titleLabel.Parent = mainFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 12)
titleCorner.Parent = titleLabel

-- 5. CONTAINER LIST SCRIPT (SCROLLING FRAME)
local scrollingFrame = Instance.new("ScrollingFrame")
scrollingFrame.Name = "MenuList"
scrollingFrame.Size = UDim2.new(1, -16, 1, -60)
scrollingFrame.Position = UDim2.new(0, 8, 0, 52)
scrollingFrame.BackgroundTransparency = 1
scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
scrollingFrame.ScrollBarThickness = 4
scrollingFrame.Parent = mainFrame

local uiListLayout = Instance.new("UIListLayout")
uiListLayout.Parent = scrollingFrame
uiListLayout.SortOrder = Enum.SortOrder.LayoutOrder
uiListLayout.Padding = UDim.new(0, 8)

uiListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
	scrollingFrame.CanvasSize = UDim2.new(0, 0, 0, uiListLayout.AbsoluteContentSize.Y + 10)
end)

-- =================================================================
-- FUNGSI MENTAHAN LIST (UNTUK MENAMBAHKAN TOMBOL/FITUR BARU)
-- =================================================================
local function addMenuButton(buttonText, callbackFunction)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, 0, 0, 40)
	btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	btn.Text = buttonText
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.TextSize = 13
	btn.Font = Enum.Font.GothamSemibold
	btn.Parent = scrollingFrame

	local btnCorner = Instance.new("UICorner")
	btnCorner.CornerRadius = UDim.new(0, 8)
	btnCorner.Parent = btn

	btn.MouseButton1Click:Connect(function()
		local success, err = pcall(callbackFunction)
		if not success then
			warn("Gagal menjalankan fitur: " .. tostring(err))
		end
	end)
end

-- =================================================================
-- TEMPAT MENAMBAHKAN SCRIPT / FITUR ANDA SECARA MANUAL
-- =================================================================

-- ListSC 1: Fitur Teleport ke Spawn
addMenuButton("🚀 Teleport ke Spawn", function()
	local char = player.Character
	if char and char:FindFirstChild("HumanoidRootPart") then
		char.HumanoidRootPart.CFrame = CFrame.new(0, 50, 0)
	end
end)

-- ListSC 2: Fitur Fly (fly,noclip,anti-kick)
addMenuButton("✈️ Fly,noclip,anti-kick", function()
   	loadstring(game:HttpGet("https://gist.githubusercontent.com/ScriptHub927/986c0c2982ec6b14a583bf1b394f3385/raw/60fa84efac03e8a3ed459334bf08a309112a8e8d/ArHubFly.lua"))()
	
end)

-- ListSC 3: Fitur Teleport (teleport)
addMenuButton("✨ [Teleport]", function()
    loadstring(game:HttpGet("https://gist.githubusercontent.com/ScriptHub927/66ce42f22fdf3a3400d233380e2a522a/raw/2823d97f0245be5feb3216fded467adb830392ac/AR%20TP%20HUB.lua"))()

end)

-- ListSC 4: Fitur WalkSpeed
addMenuButton("🏃 Walkspeed", function()
    loadstring(game:HttpGet("https://gist.githubusercontent.com/ScriptHub927/f233d54e0d6bb5b1307ffd0aa663cd31/raw/07831a7fe3556c8c8c927a5b6464e8bb7902c7ea/AR%20WalkSpeed.lua"))()

end)

-- ListSC 5: Fitur Inf&Jump Power
addMenuButton("🦘 Inf&Jump Power", function()
    loadstring(game:HttpGet("https://gist.githubusercontent.com/ScriptHub927/295859dc90a850b0a90fb8afd7473fd5/raw/1b0fc294c9eb3e6b79d31332974ae34db1226d6a/AR%20inf%26jump%20power.lua"))()

end)

-- ListSC 6 Fitur Emote&Animasi
addMenuButton("💃 Emote&Animasi", function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-7yd7-I-Emote-Script-48024"))()

end)

-- ListSC 7 Fitur Custom Animasi
addMenuButton("💃 Custom Animasi", function()
    loadstring(game:HttpGet("https://api.rubis.app/v2/scrap/PwFrcMysMOIJuWAQ/raw"))()

end)

-- ListSC 8 Fitur Recording walk
addMenuButton("🎥 Recording walk", function()
    loadstring(game:HttpGet("https://gist.githubusercontent.com/ScriptHub927/788d2cb6191625a17ddfe91063aa581c/raw/4e72759e75d113b0dbfc1dc15af7288a6625efbf/ArRecorder.lua"))()

end)

-- =================================================================
-- SISTEM BUKA / TUTUP GUI
-- =================================================================
local isOpen = false
toggleButton.MouseButton1Click:Connect(function()
	isOpen = not isOpen
	mainFrame.Visible = isOpen
	toggleButton.Text = isOpen and "❌" or "AR HUB"
	toggleButton.BackgroundColor3 = isOpen and Color3.fromRGB(180, 40, 40) or Color3.fromRGB(35, 35, 35)
end)
