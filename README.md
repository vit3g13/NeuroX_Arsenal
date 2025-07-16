
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LoadstringGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game.CoreGui

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 500, 0, 220)
Frame.Position = UDim2.new(0.25, 0, 0.25, 0)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local UICorner = Instance.new("UICorner", Frame)
UICorner.CornerRadius = UDim.new(0, 10)


local Title = Instance.new("TextLabel")
Title.Text = "executor"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 24
Title.TextColor3 = Color3.new(1, 1, 1)
Title.BackgroundTransparency = 1
Title.Size = UDim2.new(1, -50, 0, 40)
Title.Position = UDim2.new(0, 10, 0, 1)
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Frame


local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 40, 0, 30)
CloseButton.Position = UDim2.new(1, -45, 0, 5)
CloseButton.Text = "X"
CloseButton.Font = Enum.Font.GothamBold
CloseButton.TextSize = 20
CloseButton.TextColor3 = Color3.new(1, 1, 1)
CloseButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
CloseButton.Parent = Frame

local CloseCorner = Instance.new("UICorner", CloseButton)
CloseCorner.CornerRadius = UDim.new(0, 6)

CloseButton.MouseButton1Click:Connect(function()
	ScreenGui:Destroy()
end)


local TextBox = Instance.new("TextBox")
TextBox.Size = UDim2.new(0, 250, 0, 110)
TextBox.Position = UDim2.new(0, 10, 0, 40)
TextBox.PlaceholderText = "Paste  you scripts here"
TextBox.Text = ""
TextBox.TextSize = 16
TextBox.ClearTextOnFocus = false
TextBox.TextWrapped = true
TextBox.TextXAlignment = Enum.TextXAlignment.Left
TextBox.TextYAlignment = Enum.TextYAlignment.Top
TextBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
TextBox.TextColor3 = Color3.new(1, 1, 1)
TextBox.Parent = Frame

local UICorner2 = Instance.new("UICorner", TextBox)
UICorner2.CornerRadius = UDim.new(0, 6)


local LoadButton = Instance.new("TextButton")
LoadButton.Size = UDim2.new(0, 250, 0, 50)
LoadButton.Position = UDim2.new(0, 10, 0, 160)
LoadButton.Text = "Execute"
LoadButton.Font = Enum.Font.GothamBold
LoadButton.TextSize = 18
LoadButton.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
LoadButton.TextColor3 = Color3.new(1, 1, 1)
LoadButton.Parent = Frame

local UICorner3 = Instance.new("UICorner", LoadButton)
UICorner3.CornerRadius = UDim.new(0, 6)


LoadButton.MouseButton1Click:Connect(function()
    local success, err = pcall(function()
        loadstring(TextBox.Text)()
    end)
    if not success then
        warn("Error (Loadistring and normal scripts only!!!): " .. err)
    end
end)



local LoadButton = Instance.new("TextButton")
LoadButton.Size = UDim2.new(0, 230, 0, 40)
LoadButton.Position = UDim2.new(0, 262, 0, 170)
LoadButton.Text = "Aimbot(Legit)"
LoadButton.Font = Enum.Font.GothamBold
LoadButton.TextSize = 18
LoadButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
LoadButton.TextColor3 = Color3.new(1, 1, 1)
LoadButton.Parent = Frame

local UICorner3 = Instance.new("UICorner", LoadButton)
UICorner3.CornerRadius = UDim.new(0, 6)


LoadButton.MouseButton1Click:Connect(function()
  local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Configuration
local Config = {
  Enabled = false,
  Key = Enum.KeyCode.E,
  TeamCheck = true,
  TargetPart = "Head", -- The part to aim at (Head, HumanoidRootPart, etc.)
  FOV = 250, -- Field of view for initial target acquisition
  Sensitivity = 0.1, -- Lower = smoother, Higher = faster
  ShowFOV = true, -- Visual indicator of FOV
  FOVColor = Color3.fromRGB(0, 255, 0),
  LockOnTarget = nil
}

-- FOV Circle Drawing
local FOVCircle
if Config.ShowFOV and Drawing then
  FOVCircle = Drawing.new("Circle")
  FOVCircle.Visible = Config.Enabled
  FOVCircle.Radius = Config.FOV
  FOVCircle.Color = Config.FOVColor
  FOVCircle.Thickness = 1
  FOVCircle.Filled = false
  FOVCircle.Transparency = 1
end

-- Toggle function
local function toggleAimbot()
  Config.Enabled = not Config.Enabled
  if FOVCircle then
    FOVCircle.Visible = Config.Enabled
  end
  
  -- Reset locked target when disabling
  if not Config.Enabled then
    Config.LockOnTarget = nil
  end
  
  -- Notification
  local message = Config.Enabled and "Aimbot: ON" or "Aimbot: OFF"
  game.StarterGui:SetCore("SendNotification", {
    Title = "Aimbot",
    Text = message,
    Duration = 2
  })
end

-- Check if player is on the same team
local function isTeamMate(player)
  if not Config.TeamCheck then return false end
  
  return player.Team == LocalPlayer.Team and player.Team ~= nil
end

-- Check if player is valid target
local function isValidTarget(player)
  if player == LocalPlayer then return false end
  if not player.Character then return false end
  if not player.Character:FindFirstChild("Humanoid") then return false end
  if player.Character.Humanoid.Health <= 0 then return false end
  if isTeamMate(player) then return false end
  
  return true
end

-- 🌟 Přidaná funkce pro kontrolu, jestli vidíme hráče (raycast)
local function isVisible(targetPart)
  local origin = Camera.CFrame.Position
  local direction = (targetPart.Position - origin)
  local raycastParams = RaycastParams.new()
  raycastParams.FilterDescendantsInstances = {LocalPlayer.Character, workspace.CurrentCamera}
  raycastParams.FilterType = Enum.RaycastFilterType.Blacklist

  local result = workspace:Raycast(origin, direction, raycastParams)
  return not result or result.Instance:IsDescendantOf(targetPart.Parent)
end

-- Get closest player within FOV for initial target acquisition
local function getClosestPlayerInFOV()
  local closestPlayer = nil
  local shortestDistance = Config.FOV
  
  for _, player in pairs(Players:GetPlayers()) do
    if isValidTarget(player) then
      local targetPart = player.Character:FindFirstChild(Config.TargetPart)
      
      if targetPart and isVisible(targetPart) then -- 🌟 přidáno
        local screenPoint = Camera:WorldToScreenPoint(targetPart.Position)
        local vectorDistance = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(screenPoint.X, screenPoint.Y)).Magnitude
        
        if vectorDistance < shortestDistance then
          closestPlayer = player
          shortestDistance = vectorDistance
        end
      end
    end
  end
  
  return closestPlayer
end

-- Update FOV circle position
if FOVCircle then
  RunService.RenderStepped:Connect(function()
    FOVCircle.Position = Vector2.new(Mouse.X, Mouse.Y + 36)
  end)
end

-- Check if current locked target is still valid
local function isLockedTargetValid()
  local target = Config.LockOnTarget
  
  if not target then return false end
  if not target.Parent then return false end -- Player left the game
  if not isValidTarget(target) then return false end
  local targetPart = target.Character:FindFirstChild(Config.TargetPart)
  if not targetPart then return false end
  if not isVisible(targetPart) then return false end -- 🌟 přidáno
  
  return true
end

-- Main aimbot loop
RunService.RenderStepped:Connect(function()
  if Config.Enabled then
    -- Check if we have a locked target and if it's still valid
    if Config.LockOnTarget and isLockedTargetValid() then
      local targetPart = Config.LockOnTarget.Character[Config.TargetPart]
      local targetPosition = Camera:WorldToScreenPoint(targetPart.Position)
      
      -- Only aim if target is on screen
      if targetPosition.Z > 0 then
        local mousePosition = Vector2.new(Mouse.X, Mouse.Y)
        local aimPosition = Vector2.new(targetPosition.X, targetPosition.Y)
        
        -- Calculate the movement needed
        local movement = (aimPosition - mousePosition) * Config.Sensitivity
        
        -- Move the mouse
        mousemoverel(movement.X, movement.Y)
      end
    else
      -- No valid locked target, acquire a new one
      local newTarget = getClosestPlayerInFOV()
      if newTarget then
        Config.LockOnTarget = newTarget
      end
    end
  end
end)

-- Input handling for toggle
UserInputService.InputBegan:Connect(function(input, gameProcessed)
  if not gameProcessed and input.KeyCode == Config.Key then
    toggleAimbot()
    
    -- Reset locked target when toggling
    Config.LockOnTarget = nil
  end
end)

-- Player removal handling
Players.PlayerRemoving:Connect(function(player)
  if Config.LockOnTarget == player then
    Config.LockOnTarget = nil
    
    if Config.Enabled then
      game.StarterGui:SetCore("SendNotification", {
        Title = "Target Lost",
        Text = "Target left the game",
        Duration = 1.5
      })
    end
  end
end)

-- Initial notification
game.StarterGui:SetCore("SendNotification", {
  Title = " NeuroX Aimbot Loaded",
  Text = "Press E to toggle.",
  Duration = 3
})

end)


local LoadButton = Instance.new("TextButton")
LoadButton.Size = UDim2.new(0, 230, 0, 40)
LoadButton.Position = UDim2.new(0, 262, 0, 125)
LoadButton.Text = "Aimbot(Rage)"
LoadButton.Font = Enum.Font.GothamBold
LoadButton.TextSize = 18
LoadButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
LoadButton.TextColor3 = Color3.new(1, 1, 1)
LoadButton.Parent = Frame

local UICorner3 = Instance.new("UICorner", LoadButton)
UICorner3.CornerRadius = UDim.new(0, 6)


LoadButton.MouseButton1Click:Connect(function()
  local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Configuration
local Config = {
  Enabled = false,
  Key = Enum.KeyCode.Q,
  TeamCheck = true,
  TargetPart = "Head", -- The part to aim at (Head, HumanoidRootPart, etc.)
  FOV = 250, -- Field of view for initial target acquisition
  Sensitivity = 1, -- Lower = smoother, Higher = faster
  ShowFOV = true, -- Visual indicator of FOV
  FOVColor = Color3.fromRGB(0, 255, 0),
  LockOnTarget = nil
}

-- FOV Circle Drawing
local FOVCircle
if Config.ShowFOV and Drawing then
  FOVCircle = Drawing.new("Circle")
  FOVCircle.Visible = Config.Enabled
  FOVCircle.Radius = Config.FOV
  FOVCircle.Color = Config.FOVColor
  FOVCircle.Thickness = 1
  FOVCircle.Filled = false
  FOVCircle.Transparency = 1
end

-- Toggle function
local function toggleAimbot()
  Config.Enabled = not Config.Enabled
  if FOVCircle then
    FOVCircle.Visible = Config.Enabled
  end
  
  -- Reset locked target when disabling
  if not Config.Enabled then
    Config.LockOnTarget = nil
  end
  
  -- Notification
  local message = Config.Enabled and "Aimbot: ON" or "Aimbot: OFF"
  game.StarterGui:SetCore("SendNotification", {
    Title = "Aimbot",
    Text = message,
    Duration = 2
  })
end

-- Check if player is on the same team
local function isTeamMate(player)
  if not Config.TeamCheck then return false end
  
  return player.Team == LocalPlayer.Team and player.Team ~= nil
end

-- Check if player is valid target
local function isValidTarget(player)
  if player == LocalPlayer then return false end
  if not player.Character then return false end
  if not player.Character:FindFirstChild("Humanoid") then return false end
  if player.Character.Humanoid.Health <= 0 then return false end
  if isTeamMate(player) then return false end
  
  return true
end

-- 🌟 Přidaná funkce pro kontrolu, jestli vidíme hráče (raycast)
local function isVisible(targetPart)
  local origin = Camera.CFrame.Position
  local direction = (targetPart.Position - origin)
  local raycastParams = RaycastParams.new()
  raycastParams.FilterDescendantsInstances = {LocalPlayer.Character, workspace.CurrentCamera}
  raycastParams.FilterType = Enum.RaycastFilterType.Blacklist

  local result = workspace:Raycast(origin, direction, raycastParams)
  return not result or result.Instance:IsDescendantOf(targetPart.Parent)
end

-- Get closest player within FOV for initial target acquisition
local function getClosestPlayerInFOV()
  local closestPlayer = nil
  local shortestDistance = Config.FOV
  
  for _, player in pairs(Players:GetPlayers()) do
    if isValidTarget(player) then
      local targetPart = player.Character:FindFirstChild(Config.TargetPart)
      
      if targetPart and isVisible(targetPart) then -- 🌟 přidáno
        local screenPoint = Camera:WorldToScreenPoint(targetPart.Position)
        local vectorDistance = (Vector2.new(Mouse.X, Mouse.Y) - Vector2.new(screenPoint.X, screenPoint.Y)).Magnitude
        
        if vectorDistance < shortestDistance then
          closestPlayer = player
          shortestDistance = vectorDistance
        end
      end
    end
  end
  
  return closestPlayer
end

-- Update FOV circle position
if FOVCircle then
  RunService.RenderStepped:Connect(function()
    FOVCircle.Position = Vector2.new(Mouse.X, Mouse.Y + 36)
  end)
end

-- Check if current locked target is still valid
local function isLockedTargetValid()
  local target = Config.LockOnTarget
  
  if not target then return false end
  if not target.Parent then return false end -- Player left the game
  if not isValidTarget(target) then return false end
  local targetPart = target.Character:FindFirstChild(Config.TargetPart)
  if not targetPart then return false end
  if not isVisible(targetPart) then return false end -- 🌟 přidáno
  
  return true
end

-- Main aimbot loop
RunService.RenderStepped:Connect(function()
  if Config.Enabled then
    -- Check if we have a locked target and if it's still valid
    if Config.LockOnTarget and isLockedTargetValid() then
      local targetPart = Config.LockOnTarget.Character[Config.TargetPart]
      local targetPosition = Camera:WorldToScreenPoint(targetPart.Position)
      
      -- Only aim if target is on screen
      if targetPosition.Z > 0 then
        local mousePosition = Vector2.new(Mouse.X, Mouse.Y)
        local aimPosition = Vector2.new(targetPosition.X, targetPosition.Y)
        
        -- Calculate the movement needed
        local movement = (aimPosition - mousePosition) * Config.Sensitivity
        
        -- Move the mouse
        mousemoverel(movement.X, movement.Y)
      end
    else
      -- No valid locked target, acquire a new one
      local newTarget = getClosestPlayerInFOV()
      if newTarget then
        Config.LockOnTarget = newTarget
      end
    end
  end
end)

-- Input handling for toggle
UserInputService.InputBegan:Connect(function(input, gameProcessed)
  if not gameProcessed and input.KeyCode == Config.Key then
    toggleAimbot()
    
    -- Reset locked target when toggling
    Config.LockOnTarget = nil
  end
end)

-- Player removal handling
Players.PlayerRemoving:Connect(function(player)
  if Config.LockOnTarget == player then
    Config.LockOnTarget = nil
    
    if Config.Enabled then
      game.StarterGui:SetCore("SendNotification", {
        Title = "Target Lost",
        Text = "Target left the game",
        Duration = 1.5
      })
    end
  end
end)

-- Initial notification
game.StarterGui:SetCore("SendNotification", {
  Title = " NeuroX Aimbot Loaded",
  Text = "Press Q to toggle.",
  Duration = 3
})

end)

local LoadButton = Instance.new("TextButton")
LoadButton.Size = UDim2.new(0, 230, 0, 40)
LoadButton.Position = UDim2.new(0, 262, 0, 80)
LoadButton.Text = "Esp"
LoadButton.Font = Enum.Font.GothamBold
LoadButton.TextSize = 18
LoadButton.BackgroundColor3 = Color3.fromRGB(3, 192, 255)
LoadButton.TextColor3 = Color3.new(1, 1, 1)
LoadButton.Parent = Frame

local UICorner3 = Instance.new("UICorner", LoadButton)
UICorner3.CornerRadius = UDim.new(0, 6)



LoadButton.MouseButton1Click:Connect(function()

_G.FriendColor = Color3.fromRGB(0, 0, 255)
_G.EnemyColor = Color3.fromRGB(255, 0, 0)
_G.UseTeamColor = true

--------------------------------------------------------------------
local Holder = Instance.new("Folder", game.CoreGui)
Holder.Name = "ESP"

local Box = Instance.new("BoxHandleAdornment")
Box.Name = "nilBox"
Box.Size = Vector3.new(1, 2, 1)
Box.Color3 = Color3.new(100 / 255, 100 / 255, 100 / 255)
Box.Transparency = 0.7
Box.ZIndex = 0
Box.AlwaysOnTop = false
Box.Visible = false

local NameTag = Instance.new("BillboardGui")
NameTag.Name = "nilNameTag"
NameTag.Enabled = false
NameTag.Size = UDim2.new(0, 200, 0, 50)
NameTag.AlwaysOnTop = true
NameTag.StudsOffset = Vector3.new(0, 1.8, 0)
local Tag = Instance.new("TextLabel", NameTag)
Tag.Name = "Tag"
Tag.BackgroundTransparency = 1
Tag.Position = UDim2.new(0, -50, 0, 0)
Tag.Size = UDim2.new(0, 300, 0, 20)
Tag.TextSize = 15
Tag.TextColor3 = Color3.new(100 / 255, 100 / 255, 100 / 255)
Tag.TextStrokeColor3 = Color3.new(0 / 255, 0 / 255, 0 / 255)
Tag.TextStrokeTransparency = 0.4
Tag.Text = "nil"
Tag.Font = Enum.Font.SourceSansBold
Tag.TextScaled = false

local LoadCharacter = function(v)
	repeat wait() until v.Character ~= nil
	v.Character:WaitForChild("Humanoid")
	local vHolder = Holder:FindFirstChild(v.Name)
	vHolder:ClearAllChildren()
	local b = Box:Clone()
	b.Name = v.Name .. "Box"
	b.Adornee = v.Character
	b.Parent = vHolder
	local t = NameTag:Clone()
	t.Name = v.Name .. "NameTag"
	t.Enabled = true
	t.Parent = vHolder
	t.Adornee = v.Character:WaitForChild("Head", 5)
	if not t.Adornee then
		return UnloadCharacter(v)
	end
	t.Tag.Text = v.Name
	b.Color3 = Color3.new(v.TeamColor.r, v.TeamColor.g, v.TeamColor.b)
	t.Tag.TextColor3 = Color3.new(v.TeamColor.r, v.TeamColor.g, v.TeamColor.b)
	local Update
	local UpdateNameTag = function()
		if not pcall(function()
			v.Character.Humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
			local maxh = math.floor(v.Character.Humanoid.MaxHealth)
			local h = math.floor(v.Character.Humanoid.Health)
		end) then
			Update:Disconnect()
		end
	end
	UpdateNameTag()
	Update = v.Character.Humanoid.Changed:Connect(UpdateNameTag)
end

local UnloadCharacter = function(v)
	local vHolder = Holder:FindFirstChild(v.Name)
	if vHolder and (vHolder:FindFirstChild(v.Name .. "Box") ~= nil or vHolder:FindFirstChild(v.Name .. "NameTag") ~= nil) then
		vHolder:ClearAllChildren()
	end
end

local LoadPlayer = function(v)
	local vHolder = Instance.new("Folder", Holder)
	vHolder.Name = v.Name
	v.CharacterAdded:Connect(function()
		pcall(LoadCharacter, v)
	end)
	v.CharacterRemoving:Connect(function()
		pcall(UnloadCharacter, v)
	end)
	v.Changed:Connect(function(prop)
		if prop == "TeamColor" then
			UnloadCharacter(v)
			wait()
			LoadCharacter(v)
		end
	end)
	LoadCharacter(v)
end

local UnloadPlayer = function(v)
	UnloadCharacter(v)
	local vHolder = Holder:FindFirstChild(v.Name)
	if vHolder then
		vHolder:Destroy()
	end
end

for i,v in pairs(game:GetService("Players"):GetPlayers()) do
	spawn(function() pcall(LoadPlayer, v) end)
end

game:GetService("Players").PlayerAdded:Connect(function(v)
	pcall(LoadPlayer, v)
end)

game:GetService("Players").PlayerRemoving:Connect(function(v)
	pcall(UnloadPlayer, v)
end)

game:GetService("Players").LocalPlayer.NameDisplayDistance = 0

if _G.Reantheajfdfjdgs then
    return
end

_G.Reantheajfdfjdgs = ":suifayhgvsdghfsfkajewfrhk321rk213kjrgkhj432rj34f67df"

local players = game:GetService("Players")
local plr = players.LocalPlayer

function esp(target, color)
    if target.Character then
        if not target.Character:FindFirstChild("GetReal") then
            local highlight = Instance.new("Highlight")
            highlight.RobloxLocked = true
            highlight.Name = "GetReal"
            highlight.Adornee = target.Character
            highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            highlight.FillColor = color
            highlight.Parent = target.Character
        else
            target.Character.GetReal.FillColor = color
        end
    end
end

-- ✅ Upravený tým check
while task.wait() do
    for i, v in pairs(players:GetPlayers()) do
        if v == plr then
           -- zobraz sám sebe
        elseif v.TeamColor ~= plr.TeamColor then
            esp(v, _G.EnemyColor) -- zobraz nepřátele
        end
        -- spoluhráči kromě tebe se nezobrazí
    end
end


end)


local LoadButton = Instance.new("TextButton")
LoadButton.Size = UDim2.new(0, 230, 0, 40)
LoadButton.Position = UDim2.new(0, 262, 0, 35)
LoadButton.Text = "Play Music"
LoadButton.Font = Enum.Font.GothamBold
LoadButton.TextSize = 18
LoadButton.BackgroundColor3 = Color3.fromRGB(255, 242, 3)
LoadButton.TextColor3 = Color3.new(1, 1, 1)
LoadButton.Parent = Frame

local UICorner3 = Instance.new("UICorner", LoadButton)
UICorner3.CornerRadius = UDim.new(0, 6)



LoadButton.MouseButton1Click:Connect(function()

  local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://142376088"
sound.Parent = game.workspace
sound.Looped = true
sound:Play()
sound.Volume = 1

end)

local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 120, 0, 20)
CloseButton.Position = UDim2.new(1, -383, 0, 10)
CloseButton.Text = "Inf Jump"
CloseButton.Font = Enum.Font.GothamBold
CloseButton.TextSize = 20
CloseButton.TextColor3 = Color3.new(1, 1, 1)
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 3, 209)
CloseButton.Parent = Frame

local CloseCorner = Instance.new("UICorner", CloseButton)
CloseCorner.CornerRadius = UDim.new(0, 6)

CloseButton.MouseButton1Click:Connect(function()

local InfiniteJumpEnabled = true
game:GetService("UserInputService").JumpRequest:connect(function()
	if InfiniteJumpEnabled then
		game:GetService"Players".LocalPlayer.Character:FindFirstChildOfClass'Humanoid':ChangeState("Jumping")
	end
end)

end)

local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 120, 0, 20)
CloseButton.Position = UDim2.new(1, -260, 0, 10)
CloseButton.Text = "Kill(You)"
CloseButton.Font = Enum.Font.GothamBold
CloseButton.TextSize = 20
CloseButton.TextColor3 = Color3.new(1, 1, 1)
CloseButton.BackgroundColor3 = Color3.fromRGB(125, 3, 255)
CloseButton.Parent = Frame

local CloseCorner = Instance.new("UICorner", CloseButton)
CloseCorner.CornerRadius = UDim.new(0, 6)

CloseButton.MouseButton1Click:Connect(function()

local Players = game:GetService("Players")
local player = Players.LocalPlayer

player.CharacterAdded:Connect(function(character)
    character:WaitForChild("Humanoid").Health = 0
end)

-- pokud už má postavu
if player.Character then
    player.Character:WaitForChild("Humanoid").Health = 0
end

end)

local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 85, 0, 20)
CloseButton.Position = UDim2.new(1, -132, 0, 10)
CloseButton.Text = "Stop(music)"
CloseButton.Font = Enum.Font.GothamBold
CloseButton.TextSize = 15
CloseButton.TextColor3 = Color3.new(1, 1, 1)
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 150, 3)
CloseButton.Parent = Frame

local CloseCorner = Instance.new("UICorner", CloseButton)
CloseCorner.CornerRadius = UDim.new(0, 6)

CloseButton.MouseButton1Click:Connect(function()



-- Projdi všechny objekty ve hře
for _, obj in ipairs(game:GetDescendants()) do
	-- Pokud je to Sound a právě hraje, zastav ho
	if obj:IsA("Sound") and obj.IsPlaying then
		obj:Stop()
	end
end

end)


