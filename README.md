-- ============================================
-- PAPADA BOAT SPEED - Boat Speed para Blox Fruits
-- ============================================
if getgenv().PapadaBoatSpeed then return end
getgenv().PapadaBoatSpeed = true

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer

-- ============================================
-- PROTEÇÃO DA GUI
-- ============================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PapadaBoatSpeed"
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

if syn and syn.protect_gui then
    syn.protect_gui(screenGui)
    screenGui.Parent = CoreGui
elseif gethui then
    screenGui.Parent = gethui()
elseif get_hidden_ui then
    screenGui.Parent = get_hidden_ui()
else
    screenGui.Parent = CoreGui
end

-- ============================================
-- DRAG COM EFEITO GELATINA
-- ============================================
local function makeJellyDraggable(frame, isCircular)
    local dragging, dragStart, startPos
    local lastDelta = Vector2.new(0, 0)
    local velocity = Vector2.new(0, 0)
    local jellyConnection = nil
    local baseSize = frame.AbsoluteSize

    local function stopJelly()
        if jellyConnection then
            jellyConnection:Disconnect()
            jellyConnection = nil
        end
    end

    local function resetJelly()
        stopJelly()
        local sizeGoal = UDim2.new(
            frame.Size.X.Scale, baseSize.X,
            frame.Size.Y.Scale, baseSize.Y
        )
        local tween = TweenService:Create(
            frame,
            TweenInfo.new(0.5, Enum.EasingStyle.Elastic, Enum.EasingDirection.Out),
            { Size = sizeGoal }
        )
        tween:Play()
        baseSize = Vector2.new(baseSize.X, baseSize.Y)
    end

    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 
        or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            baseSize = frame.AbsoluteSize
            velocity = Vector2.new(0, 0)
            lastDelta = Vector2.new(0, 0)

            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                    resetJelly()
                end
            end)
        end
    end)

    frame.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement 
        or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStart

            -- Movimento
            frame.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )

            -- Calcular velocidade para o efeito gelatina
            local deltaMove = Vector2.new(
                delta.X - lastDelta.X,
                delta.Y - lastDelta.Y
            )
            lastDelta = Vector2.new(delta.X, delta.Y)

            -- Aplicar squash/stretch baseado na velocidade
            local speedMag = deltaMove.Magnitude
            local squash = math.clamp(speedMag / 60, 0, 0.35)

            local sizeX, sizeY
            if isCircular then
                sizeX = baseSize.X * (1 + squash * 0.6)
                sizeY = baseSize.Y * (1 - squash * 0.6)
            else
                sizeX = baseSize.X * (1 - squash * 0.4)
                sizeY = baseSize.Y * (1 + squash * 0.4)
            end

            frame.Size = UDim2.new(0, sizeX, 0, sizeY)
        end
    end)
end

-- ============================================
-- BOTÃO CIRCULAR "P" (Preto e Branco Minimalista)
-- ============================================
local btn = Instance.new("TextButton")
btn.Name = "PButton"
btn.Size = UDim2.new(0, 50, 0, 50)
btn.Position = UDim2.new(0.05, 0, 0.4, 0)
btn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
btn.Text = "P"
btn.TextColor3 = Color3.fromRGB(255, 255, 255)
btn.Font = Enum.Font.GothamBold
btn.TextSize = 22
btn.AutoButtonColor = false
btn.BorderSizePixel = 0
btn.Parent = screenGui

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(1, 0)
btnCorner.Parent = btn

local btnStroke = Instance.new("UIStroke")
btnStroke.Color = Color3.fromRGB(255, 255, 255)
btnStroke.Thickness = 2
btnStroke.Parent = btn

-- ============================================
-- GUI PRINCIPAL
-- ============================================
local frame = Instance.new("Frame")
frame.Name = "MainFrame"
frame.Size = UDim2.new(0, 220, 0, 175)
frame.Position = UDim2.new(0.15, 0, 0.4, 0)
frame.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
frame.BorderSizePixel = 0
frame.Visible = false
frame.Parent = screenGui

local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0, 14)
frameCorner.Parent = frame

local frameStroke = Instance.new("UIStroke")
frameStroke.Color = Color3.fromRGB(255, 255, 255)
frameStroke.Thickness = 1.5
frameStroke.Transparency = 0.4
frameStroke.Parent = frame

-- Aplicar animação gelatina
makeJellyDraggable(btn, true)
makeJellyDraggable(frame, false)

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 28)
title.Position = UDim2.new(0, 0, 0, 8)
title.BackgroundTransparency = 1
title.Text = "papada boat speed"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.Parent = frame

-- Valor
local valueLabel = Instance.new("TextLabel")
valueLabel.Size = UDim2.new(1, 0, 0, 22)
valueLabel.Position = UDim2.new(0, 0, 0, 42)
valueLabel.BackgroundTransparency = 1
valueLabel.Text = "speed: 120"
valueLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
valueLabel.Font = Enum.Font.Gotham
valueLabel.TextSize = 13
valueLabel.Parent = frame

-- ============================================
-- SLIDER MINIMALISTA
-- ============================================
local sliderBg = Instance.new("Frame")
sliderBg.Size = UDim2.new(0.85, 0, 0, 6)
sliderBg.Position = UDim2.new(0.075, 0, 0, 78)
sliderBg.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
sliderBg.BorderSizePixel = 0
sliderBg.Parent = frame

local sliderBgCorner = Instance.new("UICorner")
sliderBgCorner.CornerRadius = UDim.new(1, 0)
sliderBgCorner.Parent = sliderBg

local sliderFill = Instance.new("Frame")
sliderFill.Size = UDim2.new(0.2, 0, 1, 0)
sliderFill.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
sliderFill.BorderSizePixel = 0
sliderFill.Parent = sliderBg

local sliderFillCorner = Instance.new("UICorner")
sliderFillCorner.CornerRadius = UDim.new(1, 0)
sliderFillCorner.Parent = sliderFill

local sliderKnob = Instance.new("Frame")
sliderKnob.Size = UDim2.new(0, 14, 0, 14)
sliderKnob.Position = UDim2.new(0.2, -7, 0.5, -7)
sliderKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
sliderKnob.BorderSizePixel = 0
sliderKnob.Parent = sliderBg

local sliderKnobCorner = Instance.new("UICorner")
sliderKnobCorner.CornerRadius = UDim.new(1, 0)
sliderKnobCorner.Parent = sliderKnob

-- ============================================
-- BOTÕES
-- ============================================
local applyBtn = Instance.new("TextButton")
applyBtn.Size = UDim2.new(0.4, 0, 0, 30)
applyBtn.Position = UDim2.new(0.075, 0, 0, 115)
applyBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
applyBtn.Text = "ativar"
applyBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
applyBtn.Font = Enum.Font.GothamBold
applyBtn.TextSize = 12
applyBtn.BorderSizePixel = 0
applyBtn.AutoButtonColor = true
applyBtn.Parent = frame

local applyCorner = Instance.new("UICorner")
applyCorner.CornerRadius = UDim.new(0, 8)
applyCorner.Parent = applyBtn

local stopBtn = Instance.new("TextButton")
stopBtn.Size = UDim2.new(0.4, 0, 0, 30)
stopBtn.Position = UDim2.new(0.525, 0, 0, 115)
stopBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
stopBtn.Text = "parar"
stopBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
stopBtn.Font = Enum.Font.GothamBold
stopBtn.TextSize = 12
stopBtn.BorderSizePixel = 0
stopBtn.AutoButtonColor = true
stopBtn.Parent = frame

local stopCorner = Instance.new("UICorner")
stopCorner.CornerRadius = UDim.new(0, 8)
stopCorner.Parent = stopBtn

local stopStroke = Instance.new("UIStroke")
stopStroke.Color = Color3.fromRGB(255, 255, 255)
stopStroke.Thickness = 1
stopStroke.Transparency = 0.6
stopStroke.Parent = stopBtn

-- ============================================
-- SLIDER LOGIC
-- ============================================
local speedValue = 120
local minVal = 50
local maxVal = 500
local sliding = false

local function updateSlider(percent)
    percent = math.clamp(percent, 0, 1)
    sliderFill.Size = UDim2.new(percent, 0, 1, 0)
    sliderKnob.Position = UDim2.new(percent, -7, 0.5, -7)
    speedValue = math.floor(minVal + (maxVal - minVal) * percent)
    valueLabel.Text = "speed: " .. speedValue
end

updateSlider((speedValue - minVal) / (maxVal - minVal))

local function handleSliderInput(input)
    local relX = input.Position.X - sliderBg.AbsolutePosition.X
    local percent = relX / sliderBg.AbsoluteSize.X
    updateSlider(percent)
end

sliderBg.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 
    or input.UserInputType == Enum.UserInputType.Touch then
        sliding = true
        handleSliderInput(input)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if sliding and (input.UserInputType == Enum.UserInputType.MouseMovement 
    or input.UserInputType == Enum.UserInputType.Touch) then
        handleSliderInput(input)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 
    or input.UserInputType == Enum.UserInputType.Touch then
        sliding = false
    end
end)

-- ============================================
-- TOGGLE GUI (com animação gelatina de abertura)
-- ============================================
local function jellyPop(target)
    frame.Visible = true
    frame.Size = UDim2.new(0, 0, 0, 0)
    TweenService:Create(
        frame,
        TweenInfo.new(0.45, Enum.EasingStyle.Back, Enum.EasingDirection.Out),
        { Size = UDim2.new(0, 220, 0, 175) }
    ):Play()
end

local function toggleGUI()
    if frame.Visible then
        frame.Visible = false
    else
        jellyPop(true)
    end
end

btn.MouseButton1Click:Connect(toggleGUI)
if UserInputService.TouchEnabled then
    btn.TouchTap:Connect(toggleGUI)
end

-- Fechar com ESC
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.Escape then
        frame.Visible = false
    end
end)

-- ============================================
-- BYPASS & CONTROLE DE VELOCIDADE
-- ============================================
local activeSpeed = 0
local originalMaxSpeed = nil
local originalTorque = nil
local appliedSeat = nil

local function getPlayerBoat()
    local char = player.Character
    if not char then return nil end

    local humanoid = char:FindFirstChildOfClass("Humanoid")
    if not humanoid then return nil end

    local seat = humanoid.SeatPart
    if not seat then return nil end

    if seat:IsA("VehicleSeat") then
        return seat
    end

    local parent = seat.Parent
    while parent and parent ~= workspace do
        local vehSeat = parent:FindFirstChildWhichIsA("VehicleSeat")
        if vehSeat then return vehSeat end
        parent = parent.Parent
    end

    return nil
end

local function applySpeedBypass()
    local seat = getPlayerBoat()

    if not seat then
        valueLabel.Text = "❌ não está em barco"
        task.wait(1.5)
        valueLabel.Text = "speed: " .. speedValue
        return
    end

    if appliedSeat ~= seat then
        appliedSeat = seat
        originalMaxSpeed = seat.MaxSpeed
        originalTorque = seat.Torque
    end

    activeSpeed = speedValue
    applyBtn.Text = "ativo"
    applyBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 100)
end

local function stopSpeed()
    activeSpeed = 0
    applyBtn.Text = "ativar"
    applyBtn.BackgroundColor3 = Color3.fromRGB(255, 255, 255)

    if appliedSeat and originalMaxSpeed then
        pcall(function()
            appliedSeat.MaxSpeed = originalMaxSpeed
            if originalTorque then
                appliedSeat.Torque = originalTorque
            end
        end)
    end
end

applyBtn.MouseButton1Click:Connect(applySpeedBypass)
stopBtn.MouseButton1Click:Connect(stopSpeed)

-- ============================================
-- LOOP DE APLICAÇÃO GRADUAL (BYPASS)
-- ============================================
RunService.Heartbeat:Connect(function()
    if activeSpeed <= 0 then return end

    local seat = getPlayerBoat()
    if not seat then return end

    pcall(function()
        local current = seat.MaxSpeed
        local target = activeSpeed

        if math.abs(current - target) > 0.5 then
            local step = (target - current) * 0.15
            if step > 3 then step = 3 end
            if step < -3 then step = -3 end
            seat.MaxSpeed = current + step
        else
            seat.MaxSpeed = target
        end

        if seat.Torque and seat.Torque.Magnitude < 100000 then
            seat.Torque = Vector3.new(100000, 0, 100000)
        end
    end)
end)

-- ============================================
-- ATUALIZAR SEAT AO TROCAR DE BARCO
-- ============================================
RunService.Heartbeat:Connect(function()
    if activeSpeed > 0 then
        local seat = getPlayerBoat()
        if seat and seat ~= appliedSeat then
            appliedSeat = seat
            originalMaxSpeed = seat.MaxSpeed
            originalTorque = seat.Torque
        end
    end
end)

print("[papada boat speed] carregado com sucesso!")
