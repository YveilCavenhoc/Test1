local RunS = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TS = game:GetService("TweenService")
local lclplr = game.Players.LocalPlayer
local Char
local HRP
local PartVar

local function BodyRotationCam()
	local Cam = workspace.CurrentCamera
	local RX,RY,RZ = Cam.CFrame:ToOrientation()
	return CFrame.Angles(0,RY,0) * CFrame.Angles(RX,0,0) 
end

local function MovementStud(vector)
	local Cam = workspace.CurrentCamera
	local RX,RY,RZ = Cam.CFrame:ToOrientation()
	local CamAngle = CFrame.Angles(RX,0,0):Inverse() * CFrame.Angles(0,RY,0):Inverse()
	return CFrame.new(Vector3.new(HRP.Position) + CamAngle:VectorToObjectSpace(vector)) * BodyRotationCam()
end



--------------------------------------------------------------------------------------------||| Booleans
local PlatformBool = false
local HumanoidInfo = false
local NoclipBool = false
local FlyBool = false
--------------------------------------------------------------------------------------------|||


--------------------------------------------------------------------------------------------||| GUI
local ScreenG = Instance.new("ScreenGui")
ScreenG.Parent = lclplr.PlayerGui
ScreenG.ResetOnSpawn = false

local ScrollFrame = Instance.new("Frame", ScreenG)
ScrollFrame.Size = UDim2.new(.05,0,.5,0)
ScrollFrame.AnchorPoint = Vector2.new(1,.5)
ScrollFrame.Position = UDim2.new(1, 0,0.5, 0)

local Scroll = Instance.new("ScrollingFrame", ScrollFrame)
Scroll.Size = UDim2.new(1,0,1,0)
Scroll.ScrollBarThickness = .1
Scroll.ScrollBarImageTransparency = 1

local ListLayout = Instance.new("UIListLayout", Scroll)
ListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center


local function CreateButton(Text)
	local Frame = Instance.new("Frame", Scroll)
	Frame.Size = UDim2.new(0,50,0,50)
	Frame.AnchorPoint = Vector2.new(.5,.5)

	local Button = Instance.new("TextButton", Frame)
	Button.Size = UDim2.new(1,0,1,0)
	Button.Text = Text
	Button.BackgroundColor3 = Color3.new(1, 1, 1)
	
	return Button
end

local PlatformButton = CreateButton("Platform")
local HumanoidInfoButton = CreateButton("Humanoid Info")
local NoclipButton = CreateButton("Noclip")
local FlyButton = CreateButton("Fly")
--------------------------------------------------------------------------------------------|||


--------------------------------------------------------------------------------------------||| Functions for Platform
local function ReplacePlat()
	PartVar:Destroy()
	
	local PlatOffset = 0
	
	if UIS:IsKeyDown(Enum.KeyCode.Z) then
		PlatOffset += -.4
	end
	if UIS:IsKeyDown(Enum.KeyCode.X) then
		PlatOffset += 1
	end
	
	local NewP = Instance.new("Part", workspace)
	NewP.Size = Vector3.new(50,.001,50)
	NewP.CFrame = CFrame.new(HRP.Position.X,math.floor(HRP.Position.Y)-2.9+PlatOffset,HRP.Position.Z) 
	NewP.Transparency = 1
	PartVar = NewP
end

local function MakePlat()
	local NewP = Instance.new("Part", workspace)
	NewP.Size = Vector3.new(50,1,50)
	NewP.CFrame = HRP.CFrame * CFrame.new(0,-3,0)
	NewP.Transparency = 1
	PartVar = NewP
end
--------------------------------------------------------------------------------------------|||

--------------------------------------------------------------------------------------------||| Noclip

local function EnableNoclip()
	for i, v in pairs(Char:GetChildren()) do
		if v:IsA("Part") or v:IsA("MeshPart") then
			v.CanCollide = false
		end
	end
end

local function DisableNoclip()
	for i, v in pairs(Char:GetChildren()) do
		if v.Name == "HumanoidRootPart" then
			v.CanCollide = true
		end
	end
end
--------------------------------------------------------------------------------------------|||

--------------------------------------------------------------------------------------------||| Fly

local function EnableFly()
	HRP.Anchored = true
	local FlyStud = 1
	
	local FB = 0
	local LR = 0
	
	if UIS:IsKeyDown(Enum.KeyCode.W) then
		FB += -FlyStud
	end
	if UIS:IsKeyDown(Enum.KeyCode.A) then
		LR += -FlyStud
	end
	if UIS:IsKeyDown(Enum.KeyCode.S) then
		FB += FlyStud
	end
	if UIS:IsKeyDown(Enum.KeyCode.D) then
		LR += FlyStud
	end
	
	HRP.CFrame = CFrame.new(HRP.Position) * MovementStud(Vector3.new(LR,0,FB))
	task.wait(.1)
	HRP.Anchored = false
end

local function DisableFly()
	HRP.Anchored = false
end

--------------------------------------------------------------------------------------------|||








--------------------------------------------------------------------------------------------||| Heartbeat
local RunningService = RunS.Heartbeat:Connect(function(dt)
	Char = lclplr.Character
	HRP = Char.HumanoidRootPart
	
	-------------------------------------------|| Platform
	if Char ~= nil and HRP ~= nil then
		if PlatformBool == true then
			if PartVar ~= nil then
				ReplacePlat()
			else
				MakePlat()
			end

		else

			if PartVar ~= nil then
				PartVar:Destroy()
			end

		end
	end
	-------------------------------------------||
	
	-------------------------------------------|| Noclip
	if Char ~= nil and HRP ~= nil then
		if NoclipBool == true then
			EnableNoclip()
		else
			DisableNoclip()
		end
	end
	-------------------------------------------||
	
	-------------------------------------------|| Fly
	if Char ~= nil and HRP ~= nil then
		if FlyBool == true then
			EnableFly()
		else
			DisableFly()
		end
	end
	-------------------------------------------||
	
end)
--------------------------------------------------------------------------------------------|||

--------------------------------------------------------------------------------------------||| Inferior Repeat (Lag Reduction)
task.spawn(function()
	repeat
		task.wait(.2)
		
		-------------------------------------------|| Humanoid Info
		local MarkedNames = {
			"Rake",
			"Zombie",
			"Killer",
		}
		
		local function NameColors(NameOfChar)
			if game.Players:FindFirstChild(NameOfChar) then
				return tostring('<font color="#00d5ff">'..NameOfChar..'</font>')
			end
			
			if table.find(MarkedNames, NameOfChar) then
				return tostring('<font color="#ff0004">'..NameOfChar..'</font>')
			end

			return NameOfChar
		end
		
		local function CreateHumanoidInfo(CharOfHumanoid)
			local BillBoardParent
			
			if CharOfHumanoid:FindFirstChild("Head") then
				BillBoardParent = CharOfHumanoid.Head
			end
			if BillBoardParent == nil then
				for i, v in pairs(CharOfHumanoid:GetChildren()) do
					if v:IsA("Part") or v:IsA("MeshPart") then
						BillBoardParent = v
					end
				end
			end
			if BillBoardParent == nil then
				return 0
			end
			
			
			local NewBillboard = Instance.new("BillboardGui", BillBoardParent)
			NewBillboard.AlwaysOnTop = true
			NewBillboard.ResetOnSpawn = false
			NewBillboard.Enabled = true
			NewBillboard.Size = UDim2.new(4,0,1,0)
			NewBillboard.SizeOffset = Vector2.new(0,2)
			
			local NewFrame = Instance.new("Frame", NewBillboard)
			NewFrame.Size = UDim2.new(1,0,1,0)
			NewFrame.BackgroundTransparency = 1
			
			local NewTextLabel = Instance.new("TextLabel", NewFrame)
			NewTextLabel.Size = UDim2.new(1,0,1,0)
			NewTextLabel.BackgroundTransparency = 1
			NewTextLabel.TextScaled = false
			NewTextLabel.TextColor3 = Color3.new(1, 1, 1)
			NewTextLabel.TextStrokeColor3 = Color3.new(0, 0, 0)
			NewTextLabel.TextStrokeTransparency = 0
			NewTextLabel.RichText = true
			
			if CharOfHumanoid:FindFirstChild("HumanoidRootPart") then
				NewTextLabel.Text = tostring(
					NameColors(CharOfHumanoid.Name)..
						"\n"..
						"Studs: "..
						math.floor((CharOfHumanoid.HumanoidRootPart.Position - HRP.Position).magnitude)
				)
			else
				NewTextLabel.Text = tostring(
					NameColors(CharOfHumanoid.Name)
				)
			end
			
			
			task.delay(.25, function()
				NewBillboard:Destroy()
			end)
		end
		-------------------------------------------||
		
		for i, v in pairs(game.Workspace:GetDescendants()) do
			if v:IsA("Humanoid") then
				if Char ~= nil and HRP ~= nil then
					
					
					local CharOfHumanoid = v.Parent
					if HumanoidInfo == true then
						task.spawn(function()
							CreateHumanoidInfo(CharOfHumanoid)
						end)
					end
					
					
					
				end
			end
		end
		
		
	until false
end)
--------------------------------------------------------------------------------------------|||


--------------------------------------------------------------------------------------------||| Button Triggers
local function ButtonColor(ButtonPressed, Bool)
	if Bool == true then
		ButtonPressed.BackgroundColor3 = Color3.new(0.0666667, 1, 0)
	else
		ButtonPressed.BackgroundColor3 = Color3.new(1, 1, 1)
	end
	
end


PlatformButton.Activated:Connect(function(touchpos, gamepro)
	if PlatformBool == false then
		PlatformBool = true
		ButtonColor(PlatformButton, true)
	else
		PlatformBool = false
		ButtonColor(PlatformButton, false)
	end
end)

HumanoidInfoButton.Activated:Connect(function(touchpos, gamepro)
	if HumanoidInfo == false then
		HumanoidInfo = true
		ButtonColor(HumanoidInfoButton, true)
	else
		HumanoidInfo = false
		ButtonColor(HumanoidInfoButton, false)
	end
end)

NoclipButton.Activated:Connect(function(touchpos, gamepro)
	if NoclipBool == false then
		NoclipBool = true
		ButtonColor(NoclipButton, true)
	else
		NoclipBool = false
		ButtonColor(NoclipButton, false)
	end
end)

FlyButton.Activated:Connect(function(touchpos, gamepro)
	if FlyBool == false then
		FlyBool = true
		ButtonColor(FlyButton, true)
	else
		FlyBool = false
		ButtonColor(FlyButton, false)
	end
end)
--------------------------------------------------------------------------------------------|||
