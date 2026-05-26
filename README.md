# SLICKX-UNIVERSAL
-- [[ SLICK X | Futuristic Roblox Utility ]]
-- Ported to Lua for Native Roblox Environment

local function getService(name)
    local ok, service = pcall(game.GetService, game, name)
    return ok and service
end

local TweenService = getService("TweenService")
local UserInputService = getService("UserInputService")
local HttpService = getService("HttpService")
local Players = getService("Players")

local LocalPlayer = Players and Players.LocalPlayer
local CoreGui = pcall(function() return game:GetService("CoreGui") end) and game:GetService("CoreGui")
local PlayerGui = LocalPlayer and LocalPlayer:WaitForChild("PlayerGui", 5)
local UIParent = CoreGui or PlayerGui

local BG_MAIN = Color3.fromRGB(5, 5, 5)
local BG_SIDEBAR = Color3.fromRGB(10, 10, 10)
local ACCENT_BLUE = Color3.fromRGB(0, 210, 255)
local ACCENT_PURPLE = Color3.fromRGB(157, 0, 255)
local TEXT_MAIN = Color3.fromRGB(224, 224, 224)

local function makeDraggable(frame)
    local dragging, dragInput, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

local SlickXTool = {
    KnowledgeBase = {
        platformer = {"movement", "jumping", "collision_detection", "gravity"},
        rpg = {"stats", "inventory", "leveling", "combat_system"},
        fps = {"shooting", "reloading", "camera_control", "aimbot"},
        sandbox = {"building", "resource_gathering", "world_generation"}
    }
}

function SlickXTool:SearchAndAnalyze(query)
	query = query:lower()
	if query:find("mario") or query:find("sonic") or query:find("platform") then
		return "platformer", self.KnowledgeBase.platformer
	elseif query:find("warcraft") or query:find("elden") or query:find("rpg") then
		return "rpg", self.KnowledgeBase.rpg
	elseif query:find("doom") or query:find("halo") or query:find("fps") or query:find("gun") then
		return "fps", self.KnowledgeBase.fps
	elseif query:find("build") or query:find("sandbox") or query:find("mine") then
		return "sandbox", self.KnowledgeBase.sandbox
	else
		return "universal", {"movement", "jumping", "combat_system", "shooting", "reloading"}
	end
end

function SlickXTool:GenerateLuaScript(genre, features)
    local gameTitle = "SLICK X " .. (genre:sub(1,1):upper() .. genre:sub(2)) .. " Hub"
    local scriptLines = {
        "-- [[ " .. gameTitle .. " ]]",
        "-- Powered by SLICK X Cybernetic Engine",
        "",
        "local Library = { Toggled = false }",
        "local GUI = Instance.new('ScreenGui', game.CoreGui)",
        "GUI.Name = '" .. (gameTitle:gsub("%s+", "")) .. "'",
        "",
        "-- Core " .. genre .. " Modules"
    }

    for _, feature in ipairs(features) do
        table.insert(scriptLines, "-- Implementing " .. (feature:gsub("_", " ")))
        if feature == "movement" then
            table.insert(scriptLines, "function Library:handleMovement(dt)\n    -- Add movement logic here\nend")
        elseif feature == "jumping" then
            table.insert(scriptLines, "function Library:jump()\n    -- Jump logic implementation\n    print('[SLICK X] Jump triggered')\nend")
        elseif feature == "combat_system" then
            table.insert(scriptLines, "function Library:attack(target)\n    local damage = self.stats.atk\n    target:takeDamage(damage)\nend")
        elseif feature == "shooting" then
            table.insert(scriptLines, "function Library:shoot()\n    -- Shooting mechanics\n    print('[SLICK X] Shooting')\nend")
        elseif feature == "reloading" then
            table.insert(scriptLines, "function Library:reload()\n    -- Reloading mechanics\n    print('[SLICK X] Reloading weapon...')\nend")
        else
            table.insert(scriptLines, "function Library:" .. feature .. "()\n    print('[SLICK X] " .. feature .. " activated')\nend")
        end
        table.insert(scriptLines, "")
    end

    table.insert(scriptLines, "print('[SLICK X] Script Loaded Successfully')")
    table.insert(scriptLines, "return Library")
    return table.concat(scriptLines, "\n")
end

function SlickXTool:Notify(title, msg)
    local sg = UIParent:FindFirstChild("SLICK_X_GUI")
    if not sg then return end

    local notification = Instance.new("Frame", sg)
    notification.Size = UDim2.new(0, 250, 0, 80)
    notification.Position = UDim2.new(1, 10, 1, -100)
    notification.BackgroundColor3 = BG_SIDEBAR
    notification.BorderSizePixel = 0
    
    local Corner = Instance.new("UICorner", notification)
    Corner.CornerRadius = UDim.new(0, 8)
    local Stroke = Instance.new("UIStroke", notification)
    Stroke.Color = ACCENT_PURPLE
    Stroke.Thickness = 2

    local t = Instance.new("TextLabel", notification)
    t.Text = title:upper(); t.Size = UDim2.new(1, -20, 0, 30); t.Position = UDim2.new(0, 10, 0, 5)
    t.TextColor3 = ACCENT_PURPLE; t.Font = Enum.Font.SourceSansBold; t.TextSize = 16
    t.BackgroundTransparency = 1; t.TextXAlignment = Enum.TextXAlignment.Left

    local m = Instance.new("TextLabel", notification)
    m.Text = msg; m.Size = UDim2.new(1, -20, 0, 40); m.Position = UDim2.new(0, 10, 0, 30)
    m.TextColor3 = TEXT_MAIN; m.Font = Enum.Font.SourceSans; m.TextSize = 14
    m.BackgroundTransparency = 1; m.TextXAlignment = Enum.TextXAlignment.Left; m.TextWrapped = true

    notification:TweenPosition(UDim2.new(1, -260, 1, -100), "Out", "Quint", 0.5, true)
    task.delay(3, function()
        notification:TweenPosition(UDim2.new(1, 10, 1, -100), "In", "Quint", 0.5, true, function() notification:Destroy() end)
    end)
end

function SlickXTool:CreateUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "SLICK_X_GUI"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = UIParent

    local MainFrame = Instance.new("Frame")
    MainFrame.Size = UDim2.new(0, 900, 0, 600)
    MainFrame.Position = UDim2.new(0.5, -450, 0.5, -300)
    MainFrame.BackgroundColor3 = BG_MAIN
    MainFrame.BorderSizePixel = 0
    MainFrame.Parent = ScreenGui
    makeDraggable(MainFrame)

    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 12)
    Corner.Parent = MainFrame

    -- Sidebar
    local Sidebar = Instance.new("Frame")
    Sidebar.Size = UDim2.new(0, 200, 1, 0)
    Sidebar.BackgroundColor3 = BG_SIDEBAR
    Sidebar.BorderSizePixel = 0
    Sidebar.Parent = MainFrame

    local SidebarCorner = Instance.new("UICorner")
    SidebarCorner.CornerRadius = UDim.new(0, 12)
    SidebarCorner.Parent = Sidebar

    local Title = Instance.new("TextLabel")
    Title.Text = "SLICK X"
    Title.Font = Enum.Font.SourceSansBold
    Title.TextSize = 24
    Title.TextColor3 = ACCENT_PURPLE
    Title.BackgroundTransparency = 1
    Title.Size = UDim2.new(1, 0, 0, 80)
    Title.Parent = Sidebar

    local TabContainer = Instance.new("Frame")
    TabContainer.Position = UDim2.new(0, 0, 0, 100)
    TabContainer.Size = UDim2.new(1, 0, 1, -100)
    TabContainer.BackgroundTransparency = 1
    TabContainer.Parent = Sidebar

    local tabs = {"Home", "Script Hub", "Saved Scripts", "Executor", "Game Scanner", "Settings"}
    for i, tab in ipairs(tabs) do
        local btn = Instance.new("TextButton")
        btn.Text = "  " .. tab:upper()
        btn.Font = Enum.Font.SourceSansBold
        btn.TextSize = 14
        btn.TextColor3 = TEXT_MAIN
        btn.BackgroundColor3 = BG_SIDEBAR
        btn.BorderSizePixel = 0
        btn.Size = UDim2.new(1, -20, 0, 40)
        btn.Position = UDim2.new(0, 10, 0, (i-1) * 45)
        btn.TextXAlignment = Enum.TextXAlignment.Left
        btn.Parent = TabContainer

        btn.MouseEnter:Connect(function()
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = ACCENT_BLUE, TextColor3 = Color3.new(0,0,0)}):Play()
        end)
        btn.MouseLeave:Connect(function()
            TweenService:Create(btn, TweenInfo.new(0.2), {BackgroundColor3 = BG_SIDEBAR, TextColor3 = TEXT_MAIN}):Play()
        end)
    end

    -- Main Content Area
    local Content = Instance.new("Frame")
    Content.Position = UDim2.new(0, 200, 0, 0)
    Content.Size = UDim2.new(1, -200, 1, 0)
    Content.BackgroundTransparency = 1
    Content.Parent = MainFrame

    local SearchBox = Instance.new("TextBox")
    SearchBox.Size = UDim2.new(1, -60, 0, 40)
    SearchBox.Position = UDim2.new(0, 30, 0, 30)
    SearchBox.BackgroundColor3 = Color3.fromRGB(17, 17, 17)
    SearchBox.TextColor3 = ACCENT_BLUE
    SearchBox.PlaceholderText = "Search Game or Script..."
    SearchBox.Text = ""
    SearchBox.Font = Enum.Font.SourceSansBold
    SearchBox.TextSize = 16
    SearchBox.Parent = Content

    local OutputFrame = Instance.new("ScrollingFrame")
    OutputFrame.Size = UDim2.new(1, -60, 0, 350)
    OutputFrame.Position = UDim2.new(0, 30, 0, 90)
    OutputFrame.BackgroundColor3 = Color3.new(0,0,0)
    OutputFrame.ScrollBarThickness = 4
    OutputFrame.Parent = Content

    local Stroke = Instance.new("UIStroke")
    Stroke.Color = ACCENT_PURPLE
    Stroke.Parent = OutputFrame

    local OutputText = Instance.new("TextBox")
    OutputText.Size = UDim2.new(1, -10, 1, 0)
    OutputText.Position = UDim2.new(0, 5, 0, 5)
    OutputText.BackgroundTransparency = 1
    OutputText.TextColor3 = ACCENT_BLUE
    OutputText.Font = Enum.Font.Code
    OutputText.TextSize = 14
    OutputText.TextXAlignment = Enum.TextXAlignment.Left
    OutputText.TextYAlignment = Enum.TextYAlignment.Top
    OutputText.MultiLine = true
    OutputText.ClearTextOnFocus = false
    OutputText.Text = "-- Output Terminal ready..."
    OutputText.Parent = OutputFrame

    local ButtonFrame = Instance.new("Frame")
    ButtonFrame.Size = UDim2.new(1, 0, 0, 50)
    ButtonFrame.Position = UDim2.new(0, 0, 1, -80)
    ButtonFrame.BackgroundTransparency = 1
    ButtonFrame.Parent = Content

    local ListLayout = Instance.new("UIListLayout")
    ListLayout.FillDirection = Enum.FillDirection.Horizontal
    ListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    ListLayout.Padding = UDim.new(0, 15)
    ListLayout.Parent = ButtonFrame

    local function createBtn(text, color, callback)
        local b = Instance.new("TextButton")
        b.Text = text
        b.Size = UDim2.new(0, 150, 0, 40)
        b.BackgroundColor3 = color
        b.Font = Enum.Font.SourceSansBold
        b.TextSize = 14
        b.TextColor3 = Color3.new(0,0,0)
        b.Parent = ButtonFrame
        b.MouseButton1Click:Connect(callback)
        
        local c = Instance.new("UICorner")
        c.CornerRadius = UDim.new(0, 6)
        c.Parent = b
        return b
    end

    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Size = UDim2.new(1, 0, 0, 20)
    StatusLabel.Position = UDim2.new(0, 0, 1, -20)
    StatusLabel.BackgroundColor3 = BG_MAIN
    StatusLabel.TextColor3 = Color3.fromRGB(68, 68, 68)
    StatusLabel.Text = "SYSTEM READY // SLICK X V1.0"
    StatusLabel.Font = Enum.Font.Code
    StatusLabel.TextSize = 12
    StatusLabel.Parent = MainFrame

    createBtn("INJECT & GENERATE", ACCENT_BLUE, function()
        local query = SearchBox.Text
        if query == "" then return end
        
        StatusLabel.Text = "DETECTING ENGINE: " .. query:upper() .. "..."
        StatusLabel.TextColor3 = ACCENT_BLUE
        
        task.wait(0.5)
        local genre, features = self:SearchAndAnalyze(query)
        local luaCode = self:GenerateLuaScript(genre, features)
        
        OutputText.Text = luaCode
        StatusLabel.Text = "SLICK X: MODULES GENERATED"
        StatusLabel.TextColor3 = ACCENT_PURPLE
        self:Notify("SLICK X", "Script modules injected successfully!")
    end)

    createBtn("COPY", Color3.fromRGB(20, 20, 20), function()
        local clip = setclipboard or toclipboard or (getgenv and getgenv().setclipboard)
        if clip then
            clip(OutputText.Text)
            StatusLabel.Text = "BUFFER COPIED TO CLIPBOARD"
            StatusLabel.TextColor3 = ACCENT_BLUE
        else
            StatusLabel.Text = "COPY FAILED: NO EXECUTOR CLIPBOARD API"
            StatusLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
        end
    end)

    createBtn("CLEAR", Color3.fromRGB(20, 20, 20), function()
        OutputText.Text = ""
        StatusLabel.Text = "CONSOLE WIPED"
        StatusLabel.TextColor3 = Color3.fromRGB(68, 68, 68)
    end)
end

SlickXTool:CreateUI()
