loadstring([[
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

-- Remover GUI anterior no CoreGui
pcall(function()
	if game.CoreGui:FindFirstChild("BringGUI") then
		game.CoreGui:FindFirstChild("BringGUI"):Destroy()
	end
end)

-- Criar GUI no CoreGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BringGUI"
ScreenGui.IgnoreGuiInset = true
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Global
ScreenGui.Parent = game.CoreGui

-- Botão menor
local ToggleButton = Instance.new("TextButton")
ToggleButton.Name = "BringToggle"
ToggleButton.Size = UDim2.new(0, 50, 0, 25) -- BOTÃO MENOR
ToggleButton.Position = UDim2.new(0, 20, 0, 120)
ToggleButton.BackgroundColor3 = Color3.fromRGB(170, 0, 0) -- Vermelho = OFF
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.TextScaled = true
ToggleButton.Text = "OFF"
ToggleButton.ZIndex = 9999
ToggleButton.AutoButtonColor = true
ToggleButton.Active = true
ToggleButton.Draggable = true
ToggleButton.Visible = true
ToggleButton.Parent = ScreenGui

-- Suporte arrastar mobile
local UIS = game:GetService("UserInputService")
local dragging, dragInput, dragStart, startPos

ToggleButton.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = ToggleButton.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

ToggleButton.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement then
		dragInput = input
	end
end)

UIS.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		ToggleButton.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

-- Bring
local bringAtivo = false
local conexao = nil

local function ativarBring()
	conexao = mouse.Button1Down:Connect(function()
		local target = mouse.Target
		if target and target.Parent and target.Parent:FindFirstChild("HumanoidRootPart") then
			local targetPlayer = game.Players:GetPlayerFromCharacter(target.Parent)
			if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
				targetPlayer.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame * CFrame.new(2, 0, 0)
			end
		end
	end)
end

local function desativarBring()
	if conexao then
		conexao:Disconnect()
		conexao = nil
	end
end

-- Alternância ON/OFF
ToggleButton.MouseButton1Click:Connect(function()
	bringAtivo = not bringAtivo
	if bringAtivo then
		ToggleButton.Text = "ON"
		ToggleButton.BackgroundColor3 = Color3.fromRGB(0, 170, 0) -- Verde
		ativarBring()
	else
		ToggleButton.Text = "OFF"
		ToggleButton.BackgroundColor3 = Color3.fromRGB(170, 0, 0) -- Vermelho
		desativarBring()
	end
end)
]])()
