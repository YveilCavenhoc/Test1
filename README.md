local RunS = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local lclplr = game.Players.LocalPlayer
local Char
local HRP
local PartVar

--------------------------------------------------------------------------------------------||| Booleans
local PlatformBool = false
local HumanoidInfo = false
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
Scroll.ScrollBarThickness = 1
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
	
	return Button
end

local PlatformButton = CreateButton("Platform")
local HumanoidInfoButton = CreateButton("Humanoid Info")
--------------------------------------------------------------------------------------------|||


--------------------------------------------------------------------------------------------||| Functions for Platform
local function ReplacePlat()
	PartVar:Destroy()
	
	local NewP = Instance.new("Part", workspace)
	NewP.Size = Vector3.new(10,.001,10)
	NewP.CFrame = CFrame.new(HRP.Position.X,math.floor(HRP.Position.Y)-2.9,HRP.Position.Z) 
	NewP.Transparency = 1
	PartVar = NewP
end

local function MakePlat()
	local NewP = Instance.new("Part", workspace)
	NewP.Size = Vector3.new(10,1,10)
	NewP.CFrame = HRP.CFrame * CFrame.new(0,-3,0)
	NewP.Transparency = 1
	PartVar = NewP
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
	
	
	
	
end)
--------------------------------------------------------------------------------------------|||

--------------------------------------------------------------------------------------------||| Inferior Repeat (Lag Reduction)
task.spawn(function()
	repeat
		task.wait(.2)
		
		-------------------------------------------|| Humanoid Info
		local MarkedNames = {
			"Rake"
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
			local HeadOfChar = CharOfHumanoid.Head
			
			local NewBillboard = Instance.new("BillboardGui", HeadOfChar)
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
			
			NewTextLabel.Text = tostring(
				NameColors(CharOfHumanoid.Name)..
				"\n"..
				"Studs: "..
				math.floor((CharOfHumanoid.HumanoidRootPart.Position - HRP.Position).magnitude)
			)
			
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
PlatformButton.Activated:Connect(function(touchpos, gamepro)
	if PlatformBool == false then
		PlatformBool = true
	else
		PlatformBool = false
	end
end)

HumanoidInfoButton.Activated:Connect(function(touchpos, gamepro)
	if HumanoidInfo == false then
		HumanoidInfo = true
	else
		HumanoidInfo = false
	end
end)
--------------------------------------------------------------------------------------------|||
