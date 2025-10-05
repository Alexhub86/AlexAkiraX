-- Anti-Lag Script com UI RGB, Drag, Minimizar, Redimensionar
local CoreGui = game:GetService("CoreGui")
local Lighting = game:GetService("Lighting")
local UserInputService = game:GetService("UserInputService")

pcall(function()
    CoreGui:FindFirstChild("AntiLagUI"):Destroy()
end)

local gui = Instance.new("ScreenGui", CoreGui)
gui.Name = "AntiLagUI"
gui.ResetOnSpawn = false

-- Main Frame
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 230)
frame.Position = UDim2.new(0.35, 0, 0.35, 0)
frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
frame.BorderSizePixel = 0
frame.Name = "MainFrame"
Instance.new("UICorner", frame)

-- RGB Background
spawn(function()
	while true do
		for i = 0, 1, 0.01 do
			frame.BackgroundColor3 = Color3.fromHSV(i, 1, 1)
			wait(0.01)
		end
	end
end)

-- Title Bar
local titleBar = Instance.new("TextLabel", frame)
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.BackgroundTransparency = 1
titleBar.Text = "🎮 Anti-Lag Script"
titleBar.TextColor3 = Color3.fromRGB(255, 255, 255)
titleBar.Font = Enum.Font.GothamBold
titleBar.TextSize = 20
titleBar.Name = "TitleBar"

-- Botões estilo Windows
local function criarJanelaBotao(text, pos, callback)
	local btn = Instance.new("TextButton", frame)
	btn.Size = UDim2.new(0, 30, 0, 30)
	btn.Position = UDim2.new(1, -pos, 0, 0)
	btn.Text = text
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 18
	btn.TextColor3 = Color3.fromRGB(255,255,255)
	btn.BackgroundColor3 = Color3.fromRGB(30,30,30)
	btn.BorderSizePixel = 0
	Instance.new("UICorner", btn)
	btn.MouseButton1Click:Connect(callback)
	return btn
end

-- Conteúdo
local contentFrame = Instance.new("Frame", frame)
contentFrame.Size = UDim2.new(1, 0, 1, -30)
contentFrame.Position = UDim2.new(0, 0, 0, 30)
contentFrame.BackgroundTransparency = 1

-- Botões
local function criarBotao(texto, posY)
	local botao = Instance.new("TextButton", contentFrame)
	botao.Size = UDim2.new(0.8, 0, 0, 30)
	botao.Position = UDim2.new(0.1, 0, 0, posY)
	botao.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	botao.Text = texto
	botao.TextColor3 = Color3.fromRGB(255, 255, 255)
	botao.Font = Enum.Font.Gotham
	botao.TextSize = 14
	Instance.new("UICorner", botao)
	return botao
end

-- Funções
local function ativarAntiLag()
	for _, v in pairs(workspace:GetDescendants()) do
		if v:IsA("Part") or v:IsA("Union") or v:IsA("MeshPart") then
			v.Material = Enum.Material.SmoothPlastic
			v.CastShadow = false
			v.Reflectance = 0
		elseif v:IsA("Decal") or v:IsA("Texture") then
			v.Transparency = 1
		end
	end
	Lighting.GlobalShadows = false
	Lighting.FogEnd = 1000000
	Lighting.Brightness = 1
	Lighting.EnvironmentDiffuseScale = 0
	Lighting.EnvironmentSpecularScale = 0
	print("✅ Anti-Lag Ativado")
end

local function desativarAntiLag()
	Lighting.GlobalShadows = true
	Lighting.FogEnd = 1000
	Lighting.Brightness = 2
	Lighting.EnvironmentDiffuseScale = 1
	Lighting.EnvironmentSpecularScale = 1
	print("❌ Anti-Lag Desativado")
end

-- Botões principais
local btnAtivar = criarBotao("✅ Ativar Anti-Lag", 10)
local btnDesativar = criarBotao("❌ Desativar Anti-Lag", 50)
local btnCreditos = criarBotao("💡 Créditos", 90)
local btnFechar = criarBotao("❎ Fechar UI", 130)

btnAtivar.MouseButton1Click:Connect(ativarAntiLag)
btnDesativar.MouseButton1Click:Connect(desativarAntiLag)
btnCreditos.MouseButton1Click:Connect(function()
	titleBar.Text = "🎉 Feito por: Alex AkiraX 💻"
	wait(3)
	titleBar.Text = "🎮 Anti-Lag Script"
end)
btnFechar.MouseButton1Click:Connect(function()
	gui:Destroy()
end)

-- Notificação Lateral
local notification = Instance.new("TextLabel", gui)
notification.Position = UDim2.new(0, 10, 0.5, -50)
notification.Size = UDim2.new(0, 250, 0, 100)
notification.BackgroundTransparency = 1
notification.TextWrapped = true
notification.Text = "✅ Obrigado por usar nosso script!\nPor favor, volte sempre!"
notification.TextColor3 = Color3.fromRGB(255, 255, 255)
notification.Font = Enum.Font.Gotham
notification.TextSize = 16

-- Movimento (Drag)
local dragging, dragInput, dragStart, startPos

titleBar.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
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

titleBar.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

-- Estado UI
local minimizado = false
local expandido = false
local tamanhoNormal = UDim2.new(0, 300, 0, 230)
local tamanhoExpandido = UDim2.new(0, 500, 0, 400)

-- Botão Minimizar
criarJanelaBotao("_", 90, function()
	minimizado = not minimizado
	contentFrame.Visible = not minimizado
end)

-- Botão Redimensionar
criarJanelaBotao("▭", 60, function()
	if expandido then
		frame.Size = tamanhoNormal
	else
		frame.Size = tamanhoExpandido
	end
	expandido = not expandido
end)

-- Botão Fechar (mesmo do X)
criarJanelaBotao("X", 30, function()
	gui:Destroy()
end)
