--[[
✅ Safe AntiBan UI
✔️ Velocidade real com impulso (CFrame)
✔️ ESP com cor do time
✔️ Pulo infinito, Noclip, Queda lenta
✔️ Auto Revistar
✔️ Auto Kick por HP baixo
✔️ Aimbot com Hitbox Expandida
✔️ Hitbox Grande com Ponto Branco Central
]]--

-- Anti-Detect leve
pcall(function()
	setfflag("HumanoidParallelRemoveNoPhysics", "False")
	setfflag("HumanoidParallelRemoveNoPhysicsNoSimulate2", "False")
end)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer
local Remote = ReplicatedStorage:WaitForChild("RemoteNovos"):WaitForChild("bixobrabo")

-- Variáveis
local speed = 0
local infJump, noclip, slowFall, autoRevistarAtivo, autoKickAtivo = false, false, false, false, false
local cooldownGlobal = false

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "SafeUI"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 200, 0, 295)
frame.Position = UDim2.new(0.05, 0, 0.5, -147)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.Active = true
frame.Draggable = true
frame.BorderSizePixel = 0

local title = Instance.new("TextLabel", frame)
title.Text = "✅ Safe UI"
title.Size = UDim2.new(1, 0, 0, 25)
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.TextSize = 16

-- Campo de velocidade
local speedLabel = Instance.new("TextLabel", frame)
speedLabel.Text = "Velocidade real"
speedLabel.Size = UDim2.new(1, -10, 0, 15)
speedLabel.Position = UDim2.new(0, 5, 0, 30)
speedLabel.BackgroundTransparency = 1
speedLabel.TextColor3 = Color3.new(1, 1, 1)
speedLabel.Font = Enum.Font.SourceSans
speedLabel.TextSize = 14

local speedBox = Instance.new("TextBox", frame)
speedBox.Size = UDim2.new(0, 180, 0, 25)
speedBox.Position = UDim2.new(0, 10, 0, 50)
speedBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
speedBox.Text = "0"
speedBox.Font = Enum.Font.SourceSans
speedBox.TextColor3 = Color3.new(1, 1, 1)
speedBox.TextSize = 14
speedBox.ClearTextOnFocus = false

-- Criador de botões
local function createButton(txt, posY)
	local b = Instance.new("TextButton", frame)
	b.Size = UDim2.new(0, 180, 0, 25)
	b.Position = UDim2.new(0, 10, 0, posY)
	b.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
	b.TextColor3 = Color3.fromRGB(255, 255, 255)
	b.Font = Enum.Font.SourceSans
	b.TextSize = 14
	b.Text = txt
	b.BorderSizePixel = 0
	return b
end

-- Botões
local btnJump = createButton("🦘 Pulo Infinito", 80)
local btnNoclip = createButton("🚪 Noclip", 110)
local btnFall = createButton("🪂 Queda Lenta", 140)
local btnRevistar = createButton("🔍 Auto Revistar", 170)
local btnAutoKick = createButton("🔴 Auto Kick HP ≤ 13", 200)
local btnAimbot = createButton("🎯 Aimbot", 230)
local btnHitbox = createButton("📦 Hitbox", 260)

-- Velocidade com CFrame
RunService.Heartbeat:Connect(function()
	local char = LocalPlayer.Character
	if not char then return end
	local hrp = char:FindFirstChild("HumanoidRootPart")
	local hum = char:FindFirstChild("Humanoid")
	if hrp and hum and hum.MoveDirection.Magnitude > 0 then
		speed = tonumber(speedBox.Text) or 0
		if speed > 0 then
			local dir = hum.MoveDirection * speed
			hrp.CFrame = hrp.CFrame + dir
		end
	end
end)

-- Pulo infinito
btnJump.MouseButton1Click:Connect(function()
	infJump = not infJump
	btnJump.Text = infJump and "🟢 Pulo Infinito" or "🦘 Pulo Infinito"
end)

UIS.JumpRequest:Connect(function()
	if infJump and LocalPlayer.Character then
		local hum = LocalPlayer.Character:FindFirstChild("Humanoid")
		if hum then hum:ChangeState("Jumping") end
	end
end)

-- Noclip
btnNoclip.MouseButton1Click:Connect(function()
	noclip = not noclip
	btnNoclip.Text = noclip and "🟢 Noclip" or "🚪 Noclip"
end)

RunService.Stepped:Connect(function()
	if noclip and LocalPlayer.Character then
		for _, p in ipairs(LocalPlayer.Character:GetDescendants()) do
			if p:IsA("BasePart") then p.CanCollide = false end
		end
	end
end)

-- Queda lenta
btnFall.MouseButton1Click:Connect(function()
	slowFall = not slowFall
	btnFall.Text = slowFall and "🟢 Queda Lenta" or "🪂 Queda Lenta"
end)

RunService.Heartbeat:Connect(function()
	local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
	if slowFall and hrp and hrp.Velocity.Y < 0 then
		hrp.Velocity = Vector3.new(hrp.Velocity.X, -5, hrp.Velocity.Z)
	end
end)

-- Auto Revistar
btnRevistar.MouseButton1Click:Connect(function()
	autoRevistarAtivo = not autoRevistarAtivo
	btnRevistar.Text = autoRevistarAtivo and "🟢 Auto Revistar" or "🔍 Auto Revistar"
end)

RunService.Heartbeat:Connect(function()
	if not autoRevistarAtivo or cooldownGlobal then return end

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") then
			local humanoid = player.Character.Humanoid
			local distancia = (LocalPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude

			if distancia <= 20 and humanoid.Health <= 0 then
				cooldownGlobal = true
				for i = 1, 3 do
					Remote:FireServer("/revistar")
					task.wait(0.07)
				end
				task.delay(2, function()
					cooldownGlobal = false
				end)
				break
			end
		end
	end
end)

-- Auto Kick
btnAutoKick.MouseButton1Click:Connect(function()
	autoKickAtivo = not autoKickAtivo
	btnAutoKick.Text = autoKickAtivo and "🟢 Auto Kick HP ≤ 13" or "🔴 Auto Kick HP ≤ 13"

	if autoKickAtivo then
		RunService.Heartbeat:Connect(function()
			if autoKickAtivo and LocalPlayer.Character then
				local hum = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
				if hum and hum.Health <= 13 then
					LocalPlayer:Kick("Você perdeu seu HP da média.")
				end
			end
		end)
	end
end)

-- Aimbot + Hitbox Expandida
btnAimbot.MouseButton1Click:Connect(function()
	local Camera = workspace.CurrentCamera
	local circle = Drawing.new("Circle")
	circle.Color = Color3.fromRGB(170, 0, 255)
	circle.Thickness = 2
	circle.Radius = 75
	circle.Filled = false
	circle.Visible = true
	circle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)

	local function IsInCircle(position)
		local screenPos, onScreen = Camera:WorldToViewportPoint(position)
		if onScreen then
			local dist = (Vector2.new(screenPos.X, screenPos.Y) - circle.Position).Magnitude
			return dist <= circle.Radius
		end
		return false
	end

	local function GetClosestPlayer()
		local closestPlayer, closestDistance = nil, math.huge
		for _, player in pairs(Players:GetPlayers()) do
			if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
				local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
				if humanoid and humanoid.Health > 0 then
					local headPos = player.Character.Head.Position
					if IsInCircle(headPos) then
						local screenPos = Camera:WorldToViewportPoint(headPos)
						local distance = (Vector2.new(screenPos.X, screenPos.Y) - circle.Position).Magnitude
						if distance < closestDistance then
							closestDistance = distance
							closestPlayer = player
						end
					end
				end
			end
		end
		return closestPlayer
	end

	local currentTarget, originalSize = nil, nil

	local function ExpandHead(player)
		if player and player.Character and player.Character:FindFirstChild("Head") then
			local head = player.Character.Head
			if not originalSize then
				originalSize = head.Size
			end
			head.Size = Vector3.new(5, 5, 5)
			head.CanCollide = false
		end
	end

	local function RestoreHead(player)
		if player and player.Character and player.Character:FindFirstChild("Head") and originalSize then
			local head = player.Character.Head
			head.Size = originalSize
			head.CanCollide = false
		end
		originalSize = nil
	end

	RunService.RenderStepped:Connect(function()
		local target = GetClosestPlayer()
		if target ~= currentTarget then
			if currentTarget then RestoreHead(currentTarget) end
			currentTarget = target
			if currentTarget then ExpandHead(currentTarget) end
		end
		if currentTarget and currentTarget.Character and currentTarget.Character:FindFirstChild("Head") then
			local headPos = currentTarget.Character.Head.Position
			Camera.CFrame = CFrame.new(Camera.CFrame.Position, headPos)
		end
	end)
end)

-- Hitbox Grande com Ponto Branco
btnHitbox.MouseButton1Click:Connect(function()
	local Camera = workspace.CurrentCamera
	local dot = Drawing.new("Circle")
	dot.Color = Color3.new(1, 1, 1)
	dot.Radius = 4
	dot.Filled = true
	dot.Thickness = 2
	dot.Visible = true

	local function ExpandHitbox(player)
		if player and player.Character and player.Character:FindFirstChild("Head") then
			local head = player.Character.Head
			head.Size = Vector3.new(35, 35, 35)
			head.Transparency = 1
			head.CanCollide = false
			head.Massless = true
		end
	end

	local function ResetHitbox(player)
		if player and player.Character and player.Character:FindFirstChild("Head") then
			local head = player.Character.Head
			head.Size = Vector3.new(2, 1, 1)
			head.Transparency = 0
			head.CanCollide = false
		end
	end

	RunService.RenderStepped:Connect(function()
		local viewportSize = Camera.ViewportSize
		dot.Position = Vector2.new(viewportSize.X / 2, viewportSize.Y / 2)

		for _, player in pairs(Players:GetPlayers()) do
			if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
				local headPos, onScreen = Camera:WorldToViewportPoint(player.Character.Head.Position)
				if onScreen then
					local distance = (Vector2.new(headPos.X, headPos.Y) - dot.Position).Magnitude
					if distance <= 100 then
						ExpandHitbox(player)
					else
						ResetHitbox(player)
					end
				else
					ResetHitbox(player)
				end
			end
		end
	end)
end)

-- Botão: AUTO ROUBAR
local btnAutoRoubar = createButton("🪙 AUTO ROUBAR", 290)

btnAutoRoubar.MouseButton1Click:Connect(function()
	-- Função para deletar todas as NotifyGui
	local function deletarNotifyGui()
		local playerGui = Players.LocalPlayer:WaitForChild("PlayerGui")
		for _, gui in ipairs(playerGui:GetChildren()) do
			if gui.Name == "NotifyGui" and gui:IsA("ScreenGui") then
				gui:Destroy()
			end
		end
	end

	-- Lista de itens para tentar pegar
	local itens = {
		"AK47", "Uzi", "Glock17", "Glock 17", "Parafal", "PARAFAL",
		"Faca", "IA2", "G3", "Tratamento", "FACA", "Xbox", "Hi Power",
		"Natalina", "C4", "AR-15", "AR15", "Escudo", "ESCUDO"
	}

	-- Parâmetros para o InvokeServer
	local args = {
		[1] = "mudaInv",
		[2] = "2",
		[4] = "1"
	}

	-- Loop do Auto Roubo
	task.spawn(function()
		while true do
			deletarNotifyGui()
			for i, item in ipairs(itens) do
				if i <= 16 then
					args[3] = item
					args[2] = tostring(i)
					task.spawn(function()
						ReplicatedStorage:WaitForChild("Modules"):WaitForChild("InvRemotes"):WaitForChild("InvRequest"):InvokeServer(unpack(args))
					end)
				end
			end
			task.wait(0.1) -- ajuste conforme desempenho
		end
	end)
end)

-- ESP Nick + Verificação de Tools especiais (em verde) - Autoexecutável

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local espNicks = {}

-- Lista de armas importantes (com variações)
local armasEspeciais = {
    "UZI", "NATALINA", "G3", "AR15", "AR-15", "PARAFAL", "HI POWER", "AK47", "FACA", "ESCUDO", "C4",
    "GLOCK17", "GLOCK 17", "USP", "TRATAMENTO"
}

-- Função para verificar se o nome da tool está na lista
local function detectarArmas(backpack)
    local encontrados = {}
    for _, tool in pairs(backpack:GetChildren()) do
        if tool:IsA("Tool") then
            local nome = tool.Name:upper()
            for _, arma in pairs(armasEspeciais) do
                if nome:find(arma) then
                    table.insert(encontrados, arma)
                end
            end
        end
    end
    return encontrados
end

RunService.RenderStepped:Connect(function()
    -- Remove desenhos antigos
    for _, d in pairs(espNicks) do
        d:Remove()
    end
    espNicks = {}

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local pos = player.Character.HumanoidRootPart.Position + Vector3.new(0, 2.5, 0)
            local screenPos, onScreen = Camera:WorldToViewportPoint(pos)

            if onScreen then
                -- Parte do nome
                local teamName = player.Team and player.Team.Name or "NoTeam"
                local nomeTexto = player.Name .. " | " .. teamName
                local corNome = Color3.fromRGB(0, 102, 255)
                if teamName:lower() == "staff" then
                    corNome = Color3.fromRGB(255, 255, 0)
                end

                local nomeDraw = Drawing.new("Text")
                nomeDraw.Text = nomeTexto
                nomeDraw.Size = 13
                nomeDraw.Color = corNome
                nomeDraw.Position = Vector2.new(screenPos.X, screenPos.Y)
                nomeDraw.Outline = true
                nomeDraw.Center = true
                nomeDraw.Visible = true
                table.insert(espNicks, nomeDraw)

                -- Parte das armas (acima do nome)
                local armas = detectarArmas(player:FindFirstChild("Backpack") or player:WaitForChild("Backpack"))
                if #armas > 0 then
                    local armaTexto = table.concat(armas, " | ")
                    local armaDraw = Drawing.new("Text")
                    armaDraw.Text = armaTexto
                    armaDraw.Size = 11
                    armaDraw.Color = Color3.fromRGB(0, 255, 127) -- Verde claro
                    armaDraw.Position = Vector2.new(screenPos.X, screenPos.Y - 15)
                    armaDraw.Outline = true
                    armaDraw.Center = true
                    armaDraw.Visible = true
                    table.insert(espNicks, armaDraw)
                end
            end
        end
    end
end)

--[[
✅ Script para Arceus X e similares
🛡️ Botão neutro, seguro e móvel
📌 Teleporta para player com 0 de HP por 2s e volta
🧱 Tool que remove paredes ao clicar
]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- 🛡️ Anti-Detect básico
pcall(function()
    if setfflag then
        setfflag("AbuseReportScreenshot", "False")
        setfflag("AbuseReportScreenshotPercentage", "0")
    end
end)

-- 🖱️ Criar botão quadrado seguro (visual de botão de erro padrão)
local CoreGui = game:GetService("CoreGui")
local gui = Instance.new("ScreenGui", CoreGui)
gui.Name = "SafeNeutralGUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 100, 0, 40)
button.Position = UDim2.new(0.5, -50, 0.5, -20)
button.Text = ""
button.BackgroundColor3 = Color3.fromRGB(120, 120, 120)
button.BorderSizePixel = 2
button.BorderColor3 = Color3.fromRGB(0, 150, 255) -- azul claro estilo erro do sistema
button.AutoButtonColor = false
button.Draggable = true
button.Active = true
button.Parent = gui

-- 👣 Teleporte rápido para jogador morto
local busy = false
local lastPos

local function getDeadPlayer()
    local closest, dist = nil, math.huge
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Humanoid") and p.Character:FindFirstChild("HumanoidRootPart") then
            if p.Character.Humanoid.Health <= 0 then
                local d = (LocalPlayer.Character.HumanoidRootPart.Position - p.Character.HumanoidRootPart.Position).Magnitude
                if d < dist then
                    dist = d
                    closest = p.Character.HumanoidRootPart
                end
            end
        end
    end
    return closest
end

button.MouseButton1Click:Connect(function()
    if busy then return end
    busy = true

    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("HumanoidRootPart") then
        busy = false
        return
    end

    lastPos = char.HumanoidRootPart.CFrame
    local target = getDeadPlayer()
    if target then
        char.HumanoidRootPart.CFrame = target.CFrame + Vector3.new(0, 2, 0)
        wait(2)
        char.HumanoidRootPart.CFrame = lastPos
    end
    busy = false
end)

-- 🛠️ Tool na Backpack que destrói obstáculos
local tool = Instance.new("Tool")
tool.Name = "WallBreaker"
tool.RequiresHandle = false

tool.Activated:Connect(function()
    local target = Mouse.Target
    if target and target:IsA("BasePart") and target.Locked == false then
        target:Destroy()
    end
end)

tool.Parent = LocalPlayer:FindFirstChild("Backpack") or LocalPlayer:WaitForChild("Backpack")

tool.AncestryChanged:Connect(function()
    if not tool:IsDescendantOf(LocalPlayer) then
        wait(1)
        tool.Parent = LocalPlayer:FindFirstChild("Backpack")
    end
end)
