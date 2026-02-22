--==============================================================--
--      RICKFPS 🎭 SPEED HUB - PURPLE EDITION
--==============================================================--

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer

if player.PlayerGui:FindFirstChild("RickFps_Ultimate") then
    player.PlayerGui.RickFps_Ultimate:Destroy()
end

--======================== CONFIG SPEED =========================--

local KEY_1 = Enum.KeyCode.Q
local KEY_2 = Enum.KeyCode.F 
local SpeedMax = 60
local SpeedItem = 30


local Enabled1 = false
local Enabled2 = false
local SpeedConn = nil
local IsMinimized = false

--======================== CONFIG DESYNC ========================--

local desyncActive = false
local firstActivation = true

local FFlags = {
    GameNetPVHeaderRotationalVelocityZeroCutoffExponent = -5000,
    LargeReplicatorWrite5 = true,
    LargeReplicatorEnabled9 = true,
    AngularVelociryLimit = 360,
    TimestepArbiterVelocityCriteriaThresholdTwoDt = 2147483646,
    S2PhysicsSenderRate = 15000,
    DisableDPIScale = true,
    MaxDataPacketPerSend = 2147483647,
    PhysicsSenderMaxBandwidthBps = 20000,
    TimestepArbiterHumanoidLinearVelThreshold = 21,
    MaxMissedWorldStepsRemembered = -2147483648,
    PlayerHumanoidPropertyUpdateRestrict = true,
    SimDefaultHumanoidTimestepMultiplier = 0,
    StreamJobNOUVolumeLengthCap = 2147483647,
    DebugSendDistInSteps = -2147483648,
    GameNetDontSendRedundantNumTimes = 1,
    CheckPVLinearVelocityIntegrateVsDeltaPositionThresholdPercent = 1,
    CheckPVDifferencesForInterpolationMinVelThresholdStudsPerSecHundredth = 1,
    LargeReplicatorSerializeRead3 = true,
    ReplicationFocusNouExtentsSizeCutoffForPauseStuds = 2147483647,
    CheckPVCachedVelThresholdPercent = 10,
    CheckPVDifferencesForInterpolationMinRotVelThresholdRadsPerSecHundredth = 1,
    GameNetDontSendRedundantDeltaPositionMillionth = 1,
    InterpolationFrameVelocityThresholdMillionth = 5,
    StreamJobNOUVolumeCap = 2147483647,
    InterpolationFrameRotVelocityThresholdMillionth = 5,
    CheckPVCachedRotVelThresholdPercent = 10,
    WorldStepMax = 30,
    InterpolationFramePositionThresholdMillionth = 5,
    TimestepArbiterHumanoidTurningVelThreshold = 1,
    SimOwnedNOUCountThresholdMillionth = 2147483647,
    GameNetPVHeaderLinearVelocityZeroCutoffExponent = -5000,
    NextGenReplicatorEnabledWrite4 = true,
    TimestepArbiterOmegaThou = 1073741823,
    MaxAcceptableUpdateDelay = 1,
    LargeReplicatorSerializeWrite4 = true
}

local defaultFFlags = {
    GameNetPVHeaderRotationalVelocityZeroCutoffExponent = 8,
    LargeReplicatorWrite5 = false,
    LargeReplicatorEnabled9 = false,
    AngularVelociryLimit = 180,
    TimestepArbiterVelocityCriteriaThresholdTwoDt = 100,
    S2PhysicsSenderRate = 60,
    DisableDPIScale = false,
    MaxDataPacketPerSend = 1024,
    PhysicsSenderMaxBandwidthBps = 10000,
    TimestepArbiterHumanoidLinearVelThreshold = 10,
    MaxMissedWorldStepsRemembered = 10,
    PlayerHumanoidPropertyUpdateRestrict = false,
    SimDefaultHumanoidTimestepMultiplier = 1,
    StreamJobNOUVolumeLengthCap = 1000,
    DebugSendDistInSteps = 10,
    GameNetDontSendRedundantNumTimes = 10,
    CheckPVLinearVelocityIntegrateVsDeltaPositionThresholdPercent = 50,
    CheckPVDifferencesForInterpolationMinVelThresholdStudsPerSecHundredth = 100,
    LargeReplicatorSerializeRead3 = false,
    ReplicationFocusNouExtentsSizeCutoffForPauseStuds = 100,
    CheckPVCachedVelThresholdPercent = 50,
    CheckPVDifferencesForInterpolationMinRotVelThresholdRadsPerSecHundredth = 100,
    GameNetDontSendRedundantDeltaPositionMillionth = 100,
    InterpolationFrameVelocityThresholdMillionth = 100,
    StreamJobNOUVolumeCap = 1000,
    InterpolationFrameRotVelocityThresholdMillionth = 100,
    CheckPVCachedRotVelThresholdPercent = 50,
    WorldStepMax = 60,
    InterpolationFramePositionThresholdMillionth = 100,
    TimestepArbiterHumanoidTurningVelThreshold = 10,
    SimOwnedNOUCountThresholdMillionth = 1000,
    GameNetPVHeaderLinearVelocityZeroCutoffExponent = 8,
    NextGenReplicatorEnabledWrite4 = false,
    TimestepArbiterOmegaThou = 1000,
    MaxAcceptableUpdateDelay = 10,
    LargeReplicatorSerializeWrite4 = false
}

--======================== FUNÇÕES AUXILIARES ===================--

local function applyFFlags(flags)
    for name, value in pairs(flags) do
        pcall(function()
            setfflag(tostring(name), tostring(value))
        end)
    end
end

local function respawn(plr)
    local char = plr.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Dead) end
        char:ClearAllChildren()
        local newChar = Instance.new("Model", workspace)
        plr.Character = newChar
        task.wait()
        plr.Character = char
        newChar:Destroy()
    end
end

--======================== GUI PRINCIPAL =========================--

local screenGui = Instance.new("ScreenGui", player.PlayerGui)
screenGui.Name = "RickFps_Ultimate"
screenGui.ResetOnSpawn = false

local mainFrame = Instance.new("Frame", screenGui)
mainFrame.Size = UDim2.new(0, 240, 0, 245)
mainFrame.Position = UDim2.new(1, -250, 0.5, -120)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.BorderSizePixel = 0
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 12)

local border = Instance.new("Frame", mainFrame)
border.Size = UDim2.new(1, 0, 1, 0)
border.BackgroundColor3 = Color3.fromRGB(130, 0, 255) -- BORDA ROXA
Instance.new("UICorner", border).CornerRadius = UDim.new(0, 12)

local purpleGradient = Instance.new("UIGradient", border)
purpleGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(160, 32, 240)),
    ColorSequenceKeypoint.new(0.5, Color3.fromRGB(80, 0, 150)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(160, 32, 240)),
})
RunService.Heartbeat:Connect(function(dt) purpleGradient.Rotation = (purpleGradient.Rotation + dt * 45) % 360 end)

local inner = Instance.new("Frame", border)
inner.Size = UDim2.new(1, -4, 1, -4)
inner.Position = UDim2.new(0, 2, 0, 2)
inner.BackgroundColor3 = Color3.fromRGB(10, 5, 15) -- FUNDO ESCURO ARROXEADO
inner.ClipsDescendants = true
Instance.new("UICorner", inner).CornerRadius = UDim.new(0, 10)

--======================== TÍTULO SUPERIOR =========================--

local topTitle = Instance.new("TextLabel", inner)
topTitle.Size = UDim2.new(1, 0, 0, 35)
topTitle.Position = UDim2.new(0, 0, 0, 5)
topTitle.BackgroundTransparency = 1
topTitle.Text = "RICKFPS 🎭"
topTitle.TextColor3 = Color3.fromRGB(200, 100, 255)
topTitle.Font = Enum.Font.GothamBold
topTitle.TextSize = 18

--======================== BOTÃO MINIMIZAR =========================--

local minBtn = Instance.new("TextButton", inner)
minBtn.Size = UDim2.new(0, 25, 0, 25)
minBtn.Position = UDim2.new(1, -30, 0, 10)
minBtn.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
minBtn.Text = "-"
minBtn.TextColor3 = Color3.new(1,1,1)
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 20
minBtn.ZIndex = 15
Instance.new("UICorner", minBtn)

--======================== SISTEMA DE ABAS =========================--

local tabHolder = Instance.new("Frame", inner)
tabHolder.Size = UDim2.new(1, -20, 0, 35)
tabHolder.Position = UDim2.new(0, 10, 0, 40)
tabHolder.BackgroundTransparency = 1

local pageSpeed = Instance.new("Frame", inner)
local pageDesync = Instance.new("Frame", inner)

for _, p in ipairs({pageSpeed, pageDesync}) do
    p.Size = UDim2.new(1, 0, 1, -110)
    p.Position = UDim2.new(0, 0, 0, 80)
    p.BackgroundTransparency = 1
    p.Visible = false
end
pageSpeed.Visible = true

local tabButtons = {}

local function updateTabs(activeBtn)
    for _, btn in ipairs(tabButtons) do
        if btn == activeBtn then
            btn.BackgroundColor3 = Color3.fromRGB(160, 32, 240)
            btn.TextColor3 = Color3.new(1, 1, 1)
        else
            btn.BackgroundColor3 = Color3.fromRGB(30, 15, 45)
            btn.TextColor3 = Color3.fromRGB(180, 180, 180)
        end
    end
end

local function createTab(text, pos, target)
    local b = Instance.new("TextButton", tabHolder)
    b.Size = UDim2.new(0.5, -5, 1, 0)
    b.Position = pos
    b.Text = text
    b.BackgroundColor3 = Color3.fromRGB(30, 15, 45)
    b.TextColor3 = Color3.new(1,1,1)
    b.Font = Enum.Font.GothamBold
    b.TextSize = 12
    Instance.new("UICorner", b)
    
    table.insert(tabButtons, b)
    
    b.MouseButton1Click:Connect(function()
        if IsMinimized then return end
        pageSpeed.Visible = false; pageDesync.Visible = false
        target.Visible = true
        updateTabs(b)
    end)
    return b
end

local tabBooster = createTab("BOOSTER", UDim2.new(0, 0, 0, 0), pageSpeed)
local tabDesync = createTab("DESYNC", UDim2.new(0.5, 5, 0, 0), pageDesync)

updateTabs(tabBooster)

--======================== CONTEÚDO BOOSTER =========================--

local btnQ = Instance.new("TextButton", pageSpeed)
btnQ.Size = UDim2.new(1, -20, 0, 45)
btnQ.Position = UDim2.new(0, 10, 0, 5)
btnQ.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
btnQ.Text = "SPEED Q (SEM PET)"
btnQ.TextColor3 = Color3.new(1,1,1)
btnQ.Font = Enum.Font.GothamBold
btnQ.TextSize = 13
Instance.new("UICorner", btnQ)

local btnF = Instance.new("TextButton", pageSpeed)
btnF.Size = UDim2.new(1, -20, 0, 45)
btnF.Position = UDim2.new(0, 10, 0, 55)
btnF.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
btnF.Text = "SPEED F (COM PET)"
btnF.TextColor3 = Color3.new(1,1,1)
btnF.Font = Enum.Font.GothamBold
btnF.TextSize = 13
Instance.new("UICorner", btnF)

--======================== CONTEÚDO DESYNC =========================--

local btnDesync = Instance.new("TextButton", pageDesync)
btnDesync.Size = UDim2.new(1, -20, 0, 45)
btnDesync.Position = UDim2.new(0, 10, 0, 5)
btnDesync.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
btnDesync.Text = "DESYNC: OFF"
btnDesync.TextColor3 = Color3.new(1,1,1)
btnDesync.Font = Enum.Font.GothamBold
btnDesync.TextSize = 14
Instance.new("UICorner", btnDesync)

--======================== RODAPÉ =========================--

local footer = Instance.new("TextLabel", inner)
footer.Size = UDim2.new(1, 0, 0, 25)
footer.Position = UDim2.new(0, 0, 1, -25)
footer.BackgroundColor3 = Color3.fromRGB(20, 10, 30)
footer.Text = "CRIADO POR RICKFPS18"
footer.TextColor3 = Color3.fromRGB(180, 100, 255)
footer.Font = Enum.Font.GothamBold
footer.TextSize = 11
Instance.new("UICorner", footer).CornerRadius = UDim.new(0, 10)

--======================== LOGICAS FUNCIONAIS =--

local function StartSpeedLogic()
    if SpeedConn then SpeedConn:Disconnect() end
    SpeedConn = RunService.RenderStepped:Connect(function()
        local char = player.Character
        if not char or not char:FindFirstChild("HumanoidRootPart") then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        local hrp = char.HumanoidRootPart
        
        local finalSpeed = 0
        if Enabled1 then finalSpeed = SpeedMax
        elseif Enabled2 then finalSpeed = SpeedItem end

        if finalSpeed > 0 and hum.MoveDirection.Magnitude > 0 then
            hrp.AssemblyLinearVelocity = Vector3.new(hum.MoveDirection.X * finalSpeed, hrp.AssemblyLinearVelocity.Y, hum.MoveDirection.Z * finalSpeed)
        end
        hrp.AssemblyAngularVelocity = Vector3.zero
    end)
end

local function UpdateSpeedVisuals()
    btnQ.Text = Enabled1 and "SPEED Q: ON" or "SPEED Q (SEM PET)"
    btnQ.BackgroundColor3 = Enabled1 and Color3.fromRGB(160, 32, 240) or Color3.fromRGB(40, 20, 60)
    
    btnF.Text = Enabled2 and "SPEED F: ON" or "SPEED F (COM PET)"
    btnF.BackgroundColor3 = Enabled2 and Color3.fromRGB(160, 32, 240) or Color3.fromRGB(40, 20, 60)
end

local function Toggle1()
    Enabled1 = not Enabled1; Enabled2 = false
    UpdateSpeedVisuals(); StartSpeedLogic()
end

local function Toggle2()
    Enabled2 = not Enabled2; Enabled1 = false
    UpdateSpeedVisuals(); StartSpeedLogic()
end

btnQ.MouseButton1Click:Connect(Toggle1)
btnF.MouseButton1Click:Connect(Toggle2)

btnDesync.MouseButton1Click:Connect(function()
    desyncActive = not desyncActive
    if desyncActive then
        applyFFlags(FFlags)
        if firstActivation then respawn(player); firstActivation = false end
        btnDesync.Text = "DESYNC: ON"; btnDesync.BackgroundColor3 = Color3.fromRGB(160, 32, 240)
    else
        applyFFlags(defaultFFlags)
        btnDesync.Text = "DESYNC: OFF"; btnDesync.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
    end
end)

--======================== MINIMIZAR E DRAG =========================--

minBtn.MouseButton1Click:Connect(function()
    IsMinimized = not IsMinimized
    if IsMinimized then
        mainFrame:TweenSize(UDim2.new(0, 240, 0, 48), "Out", "Quart", 0.3, true)
        minBtn.Text = "+"
        tabHolder.Visible = false; pageSpeed.Visible = false; pageDesync.Visible = false; footer.Visible = false; topTitle.Visible = true
    else
        mainFrame:TweenSize(UDim2.new(0, 240, 0, 245), "Out", "Quart", 0.3, true)
        minBtn.Text = "-"
        task.wait(0.2); tabHolder.Visible = true; footer.Visible = true; topTitle.Visible = true
        if pageDesync.Visible == false then pageSpeed.Visible = true end
    end
end)

UIS.InputBegan:Connect(function(i, g)
    if g then return end
    if i.KeyCode == KEY_1 then Toggle1() elseif i.KeyCode == KEY_2 then Toggle2() end
end)

local d, ds, sp
inner.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 then d = true; ds = i.Position; sp = mainFrame.Position end
end)
UIS.InputChanged:Connect(function(i)
    if d and i.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = i.Position - ds
        mainFrame.Position = UDim2.new(sp.X.Scale, sp.X.Offset + delta.X, sp.Y.Scale, sp.Y.Offset + delta.Y)
    end
end)
UIS.InputEnded:Connect(function(i) if i.UserInputType == Enum.UserInputType.MouseButton1 then d = false end end)
