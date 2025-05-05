local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local UserInputService = game:GetService("UserInputService")

local lp = Players.LocalPlayer
repeat task.wait() until lp and lp:FindFirstChild("PlayerGui")

local Gui = Instance.new("ScreenGui", lp.PlayerGui)
Gui.Name = "SimpleSpyLite"
Gui.ResetOnSpawn = false

local Frame = Instance.new("Frame", Gui)
Frame.Size = UDim2.new(0, 620, 0, 370)
Frame.Position = UDim2.new(0.5, -310, 0.5, -185)
Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Frame.BorderSizePixel = 0
Frame.Visible = false
Instance.new("UICorner", Frame)

-- Sidebar
local Sidebar = Instance.new("ScrollingFrame", Frame)
Sidebar.Size = UDim2.new(0, 200, 1, 0)
Sidebar.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Sidebar.BorderSizePixel = 0
Sidebar.CanvasSize = UDim2.new(0, 0, 0, 0)
Sidebar.ScrollBarThickness = 4
Instance.new("UICorner", Sidebar)

-- Viewer
local Viewer = Instance.new("TextBox", Frame)
Viewer.Position = UDim2.new(0, 210, 0, 10)
Viewer.Size = UDim2.new(1, -220, 0, 220)
Viewer.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Viewer.TextColor3 = Color3.new(1, 1, 1)
Viewer.TextSize = 14
Viewer.ClearTextOnFocus = false
Viewer.TextEditable = false
Viewer.TextWrapped = true
Viewer.Font = Enum.Font.Code
Viewer.Text = "Click a remote on the left to view the code"
Viewer.TextYAlignment = Enum.TextYAlignment.Top
Instance.new("UICorner", Viewer)

-- Button container
local BtnContainer = Instance.new("Frame", Frame)
BtnContainer.Position = UDim2.new(0, 210, 0, 240)
BtnContainer.Size = UDim2.new(1, -220, 0, 120)
BtnContainer.BackgroundTransparency = 1

-- Store latest code
local lastCode = ""

-- Button creator
local function createActionButton(text, color, pos, callback)
    local Btn = Instance.new("TextButton", BtnContainer)
    Btn.Text = text
    Btn.Size = UDim2.new(0.3, -5, 0, 40)
    Btn.Position = pos
    Btn.BackgroundColor3 = color
    Btn.TextColor3 = Color3.new(1, 1, 1)
    Btn.Font = Enum.Font.GothamBold
    Btn.TextSize = 14
    Btn.BorderSizePixel = 0
    Instance.new("UICorner", Btn)
    Btn.MouseButton1Click:Connect(callback)
end

-- Buttons
createActionButton("Copy", Color3.fromRGB(0, 200, 0), UDim2.new(0, 0, 0, 0), function()
    if setclipboard and lastCode ~= "" then
        setclipboard(lastCode)
    end
end)

createActionButton("Execute", Color3.fromRGB(0, 125, 255), UDim2.new(0.35, 0, 0, 0), function()
    if loadstring and lastCode ~= "" then
        local success, result = pcall(function()
            loadstring(lastCode)()
        end)
        if not success then warn("Execution error:", result) end
    else
        warn("loadstring not supported or code empty.")
    end
end)

createActionButton("Generate Loop Script", Color3.fromRGB(255, 100, 0), UDim2.new(0.7, 0, 0, 0), function()
    if lastCode ~= "" then
        Viewer.Text = "while true do\n    " .. lastCode .. "\n    wait(1)\nend"
        lastCode = Viewer.Text
    end
end)

-- Utility: Build path
local function getPath(obj)
    local path = {}
    while obj and obj ~= game do
        table.insert(path, 1, obj.Name)
        obj = obj.Parent
    end
    return "game." .. table.concat(path, ".")
end

-- Utility: Build code line
local function buildFireServerCode(remote, args)
    local code = getPath(remote) .. ":FireServer("
    for i, arg in ipairs(args) do
        local formatted = typeof(arg) == "string" and string.format("%q", arg) or tostring(arg)
        code = code .. formatted .. (i < #args and ", " or "")
    end
    code = code .. ")"
    return code
end

-- Add remote to sidebar
local remoteCount = 0
local function createRemoteButton(remote, args)
    remoteCount += 1
    local Btn = Instance.new("TextButton", Sidebar)
    Btn.Size = UDim2.new(1, -10, 0, 30)
    Btn.Position = UDim2.new(0, 5, 0, (remoteCount - 1) * 35)
    Btn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    Btn.TextColor3 = Color3.new(1, 1, 1)
    Btn.TextSize = 12
    Btn.Font = Enum.Font.GothamBold
    Btn.Text = remote.Name
    Btn.TextXAlignment = Enum.TextXAlignment.Left
    Instance.new("UICorner", Btn)

    local code = buildFireServerCode(remote, args)
    Btn.MouseButton1Click:Connect(function()
        Viewer.Text = code
        lastCode = code
    end)

    Sidebar.CanvasSize = UDim2.new(0, 0, 0, remoteCount * 35)
end

-- Watch remotes in a specific container (ReplicatedStorage or Workspace)
local function watchRemotes(container)
    for _, obj in pairs(container:GetDescendants()) do
        if obj:IsA("RemoteEvent") then
            -- Create button in the sidebar for the remote event
            createRemoteButton(obj, {})
        end
    end
end

-- Watch all RemoteEvents in ReplicatedStorage and Workspace
watchRemotes(ReplicatedStorage)
watchRemotes(Workspace)

-- Listen for dynamically added RemoteEvents in ReplicatedStorage and Workspace
ReplicatedStorage.ChildAdded:Connect(function(child)
    if child:IsA("RemoteEvent") then
        createRemoteButton(child, {})
    end
end)

Workspace.ChildAdded:Connect(function(child)
    if child:IsA("RemoteEvent") then
        createRemoteButton(child, {})
    end
end)

-- Toggle with K
UserInputService.InputBegan:Connect(function(input, gpe)
    if not gpe and input.KeyCode == Enum.KeyCode.L then
        Frame.Visible = not Frame.Visible
    end
end)
