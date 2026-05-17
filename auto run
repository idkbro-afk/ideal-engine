-- Ok 2.68
-- TweenHealth
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/loadstring/TweenHealth.lua"))()
print("[ TweenHealth ] Successful loaded.")
-- More loadstring coming soon... Awoo :3 oh shit I'm a furry.

-- ========= HEALTHBAR AND SOUNDS ===========
-- ใช้ใน LocalScript (client-side)
-- ปรับปรุงการตรวจสอบ nil เพื่อหลีกเลี่ยง "attempt to index nil with 'FindFirstChild'"

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local player = Players.LocalPlayer

-- ถ้า LocalPlayer ยังไม่พร้อม ให้รอ
if not player then
    Players:GetPropertyChangedSignal("LocalPlayer"):Wait()
    player = Players.LocalPlayer
end

-- เป้าหมายใหม่ของตำแหน่ง HealthBar (ปรับได้)
local newHealthBarPosition = UDim2.new(0.835, 0, 0, 0)
local newHealthBarSize = UDim2.new(0.109, 0, 1, 0)
local newInnerBarSize = UDim2.new(1.85, 0, 0, 10)

-- helper: หาวัตถุ TopBarApp.HealthBar แบบปลอดภัย (returns instance or nil)
local function getHealthBarRoot()
    local topBar = CoreGui:FindFirstChild("TopBarApp")
    if not topBar then return nil end
    local innerTop = topBar:FindFirstChild("TopBarApp") or topBar
    if not innerTop then return nil end
    local unibar = innerTop:FindFirstChild("UnibarLeftFrame")
    if not unibar then return nil end
    local healthBar = unibar:FindFirstChild("HealthBar")
    return healthBar
end

-- ปรับตำแหน่ง HealthBar (loop ปกติแต่ปลอดภัย)
task.spawn(function()
    while true do
        pcall(function()
            local bar = getHealthBarRoot()
            if bar then
                -- ตรวจสอบก่อนตั้งค่าทุกครั้ง
                if bar and bar.Parent then
                    bar.Visible = true
                    -- try/catch แต่ pcall รอบนอกแล้ว
                    bar.Position = newHealthBarPosition
                    bar.Size = newHealthBarSize
                end

                local innerBar = bar:FindFirstChild("HealthBar")
                if innerBar then
                    innerBar.Visible = true
                    innerBar.Size = newInnerBarSize
                end
            end
        end)
        task.wait(0.1)
    end
end)

-- GUI แสดงค่า HP / Speed / FPS (สร้างในโฟลเดอร์ใต้ HealthBar)
local function setupValueGui()
    local healthBar = getHealthBarRoot()
    if not healthBar then return end

    local folder = healthBar:FindFirstChild("ValueFolder")
    if not folder then
        folder = Instance.new("Folder")
        folder.Name = "ValueFolder"
        folder.Parent = healthBar
    end

    local gui = folder:FindFirstChild("ValueGui")
    if not gui then
        gui = Instance.new("ScreenGui")
        gui.Name = "ValueGui"
		gui.DisplayOrder = 2147483645
        gui.Parent = folder
    end

    -- frame container (point)
    local fm = gui:FindFirstChild("point")
    if not fm then
        fm = Instance.new("Frame")
        fm.Name = "point"
        fm.Size = UDim2.new(0.92, 0, 0, 15)
        fm.Position = UDim2.new(0, 0, 0, -50)
        fm.BackgroundTransparency = 1
        fm.Parent = gui
    end

    -- UIListLayout inside fm (horizontal)
    local layo = fm:FindFirstChildOfClass("UIListLayout")
    if not layo then
        layo = Instance.new("UIListLayout")
        layo.FillDirection = Enum.FillDirection.Horizontal
        layo.HorizontalAlignment = Enum.HorizontalAlignment.Right
        layo.Padding = UDim.new(0, 10)
        layo.Parent = fm
    end

    local function createLabel(name, size, strokeColor)
        -- label parent should be fm (container)
        local existing = fm:FindFirstChild(name)
        if existing and existing:IsA("TextLabel") then
            return existing
        end

        local lbl = Instance.new("TextLabel")
        lbl.Name = name
        lbl.BackgroundTransparency = 0.3
        lbl.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        lbl.Size = size or UDim2.new(0.1, 0, 1, 0)
        lbl.Position = UDim2.new(0, 0, 0, 0)
        lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
        lbl.TextStrokeTransparency = 0
        lbl.TextStrokeColor3 = strokeColor or Color3.fromRGB(255,255,255)
        lbl.TextScaled = true
        lbl.Text = "[ ... ]"
        lbl.Parent = fm

        local stroke = Instance.new("UIStroke")
        stroke.Color = strokeColor or Color3.fromRGB(255,255,255)
        stroke.Thickness = 1.5
        stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        stroke.Parent = lbl

        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(0, 4)
        corner.Parent = lbl

        return lbl
    end

    createLabel("HealthLabel", UDim2.new(0.1, 0, 1, 0), Color3.fromRGB(0,255,0))
    createLabel("SpeedLabel",  UDim2.new(0.1, 0, 1, 0), Color3.fromRGB(0,0,255))
    createLabel("FPSLabel",    UDim2.new(0.1, 0, 1, 0), Color3.fromRGB(255,0,0))
end

-- Gradient/Stroke Setup (guarded)
local fillGradient = nil
local strokeObj, strokeGradient = nil, nil

local function setupGradients()
    local healthBar = getHealthBarRoot()
    if not healthBar then return end

    local innerHealthBar = healthBar:FindFirstChild("HealthBar")
    local fill = innerHealthBar and innerHealthBar:FindFirstChild("Fill")
    if fill then
        fillGradient = fill:FindFirstChildOfClass("UIGradient")
        if not fillGradient then
            fillGradient = Instance.new("UIGradient")
            fillGradient.Name = "UIGradient"
            fillGradient.Rotation = 0
            fillGradient.Parent = fill
        end
    end

    if innerHealthBar then
        strokeObj = innerHealthBar:FindFirstChildOfClass("UIStroke")
        if not strokeObj then
            strokeObj = Instance.new("UIStroke")
            strokeObj.Name = "UIStroke"
            strokeObj.Parent = innerHealthBar
        end
        strokeObj.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
        strokeObj.Color = Color3.fromRGB(255,255,255)
        strokeObj.Thickness = 0

        strokeGradient = strokeObj:FindFirstChildOfClass("UIGradient")
        if not strokeGradient then
            strokeGradient = Instance.new("UIGradient")
            strokeGradient.Name = "UIGradient"
            strokeGradient.Parent = strokeObj
        end
    end
end

-- utility strings -> number formatting
local function getHealthText(current)
    return string.format("%.3f", current or 0)
end

local function calculateSpeed(velocity)
    if not velocity then return "0.000" end
    return string.format("%.3f", velocity.Magnitude)
end

local fps = 60
local lastUpdate = tick()
local frameCount = 0
local function calculateFPS()
    frameCount = frameCount + 1
    local now = tick()
    local dt = now - lastUpdate
    if dt >= 0.5 then
        fps = math.floor(frameCount / dt + 0.5)
        frameCount = 0
        lastUpdate = now
    end
    return fps
end

-- อัปเดตทุกเฟรม (RenderStepped)
RunService.RenderStepped:Connect(function()
    local char = player and player.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    local hrp = char and (char:FindFirstChild("HumanoidRootPart") or char.PrimaryPart)
    if not (hum and hrp) then return end

    -- สร้าง GUI/gradients หากยังไม่มี
    setupValueGui()
    setupGradients()

    local healthBar = getHealthBarRoot()
    if not healthBar then return end

    local folder = healthBar:FindFirstChild("ValueFolder")
    local gui = folder and folder:FindFirstChild("ValueGui")
    if not gui then return end

    local fm = gui:FindFirstChild("point")
    if not fm then return end

    local healthLabel = fm:FindFirstChild("HealthLabel")
    local speedLabel = fm:FindFirstChild("SpeedLabel")
    local fpsLabel   = fm:FindFirstChild("FPSLabel")

    local percent = 0
    if hum and hum.MaxHealth and hum.MaxHealth > 0 then
        percent = math.clamp(hum.Health / hum.MaxHealth, 0, 1)
    end

    -- use Color3.fromHSV directly
    local mainColor = Color3.fromHSV(percent * 0.33, 1, 1)
    local altColor = Color3.fromHSV(((percent * 0.33) - 0.08) % 1, 0.9, 1)

    if healthLabel and hum then
        pcall(function()
            healthLabel.Text = getHealthText(hum.Health) .. " HP"
            healthLabel.TextStrokeColor3 = mainColor
        end)
    end

    if speedLabel and hrp then
        pcall(function()
            speedLabel.Text = calculateSpeed(hrp.Velocity) .. " WS/s"
        end)
    end

    if fpsLabel then
        pcall(function() fpsLabel.Text = "FPS " .. tostring(calculateFPS()) end)
    end

    -- Update Fill Gradient safely
    if fillGradient then
        pcall(function()
            fillGradient.Color = ColorSequence.new({
                ColorSequenceKeypoint.new(0, altColor),
                ColorSequenceKeypoint.new(1, mainColor)
            })
        end)
    end

    if strokeObj and strokeGradient then
        pcall(function()
            strokeObj.Thickness = (1 - percent) * 4
            strokeGradient.Color = ColorSequence.new({
                ColorSequenceKeypoint.new(0, altColor),
                ColorSequenceKeypoint.new(1, mainColor)
            })
        end)
    end
end)

print("[ HealthBar ] Successful loaded.")
-- Image and sound pools (guarded usage)
local imageIds = {
    96532568222291, 87031182561721, 98399849016295,
    85810598205383, 78345061727777
}

local soundIds = {
    6445594239, 7772283448, 18900008907,
    17517499979, 6701126635, 127758670731005,
    100656590080703, 91213210724720, 9106880189,
    18437707128, 15022681798, 73807980472301,
    133843340754810, 4492455380, 134547185130980,
    6729922069, 6349641063, 80156405968805, 8449305114,
    17748195478, 154146535, 17620645740,
    5853668794, 8551016315, 7056720271,
    2661731024, 108277187006018, 17229758629,
    4910368846, 8626458303, 6345884580,
    74360230474567, 78781475442496, 17629583511,
    115036117519824, 9083355768, 108820459035129, 4067643809, 105101770611803,
    104496776279828, 6435400690, 17390851754,
    4643776975, 8481038804, 6435404036,
    8987546731, 101627462619966, 6435412998,
    132582860782653, 17593207303, 129111537687116,
    18908859228, 114739399228816, 577475178,
    100835951102315, 17673195487, 88282256294957,
}

-- helper: safely pick random element
local function safeRandomChoice(tbl)
    if type(tbl) ~= "table" or #tbl == 0 then return nil end
    return tbl[math.random(1, #tbl)]
end

-- find healthBarPath robustly (used for image UI)
local function findHealthBarPath()
    local top = CoreGui:FindFirstChild("TopBarApp")
    if not top then return nil end
    local inner = top:FindFirstChild("TopBarApp") or top
    return inner
end

local healthBarPath = findHealthBarPath()

local function createOrUpdateImage()
    -- try to refresh healthBarPath if nil
    if not healthBarPath then healthBarPath = findHealthBarPath() end
    if not healthBarPath then return end

    local healthBar = healthBarPath:FindFirstChild("UnibarLeftFrame", true)
    if not healthBar then return end

    local imageLabel = healthBar:FindFirstChild("ImageDeathRandomClient")
    if not imageLabel then
        imageLabel = Instance.new("ImageLabel")
        imageLabel.Name = "ImageDeathRandomClient"
        imageLabel.Size = UDim2.new(1.5, 0, 0.19000037, 0)
        imageLabel.BackgroundTransparency = 1
        imageLabel.ImageTransparency = 0
        imageLabel.Visible = true
        imageLabel.ZIndex = 10
        imageLabel.Parent = healthBar
    end

    local selectedId = safeRandomChoice(imageIds)
    if selectedId then
        pcall(function()
            imageLabel.Image = "rbxassetid://" .. tostring(selectedId)
            imageLabel.Position = UDim2.new(2.5, 0, 0.410000175, 0)
        end)
    end
end

local function playRandomSound(rootPart)
    if not rootPart then return end
    local selectedId = safeRandomChoice(soundIds)
    if not selectedId then return end

    local sound = Instance.new("Sound")
    sound.Name = "DeathSoundClient"
    sound.SoundId = "rbxassetid://" .. tostring(selectedId)
    sound.Volume = 1
    sound.Looped = false
    sound.RollOffMode = Enum.RollOffMode.Inverse
    sound.RollOffMaxDistance = 100
    sound.Parent = rootPart

    -- try to play safely
    local ok, err = pcall(function()
        -- try :Play() directly; if not yet loaded, connect Loaded
        local played = false
        if sound.IsLoaded then
            sound:Play()
            played = true
        else
            -- connect Loaded event (some executors/clients have it)
            if sound.Loaded then
                sound.Loaded:Connect(function()
                    pcall(function() sound:Play() end)
                end)
            else
                -- fallback: wait briefly then try Play
                task.delay(0.5, function()
                    pcall(function() sound:Play() end)
                end)
            end
        end
    end)
    if not ok then
        warn("[Healthbar] playRandomSound error: "..tostring(err))
    end
end

local function monitorHumanoid(humanoid, character)
    if not humanoid then return end
    humanoid.Died:Connect(function()
        pcall(function()
            createOrUpdateImage()
            local rootPart = character and (character:FindFirstChild("HumanoidRootPart") or character.PrimaryPart)
            if rootPart then
                playRandomSound(rootPart)
            end
        end)
    end)
end

-- connect existing character & future ones
player.CharacterAdded:Connect(function(char)
    local humanoid = char:FindFirstChildOfClass("Humanoid") or char:WaitForChild("Humanoid", 5)
    if humanoid then
        monitorHumanoid(humanoid, char)
    end
end)

if player.Character then
    local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        monitorHumanoid(humanoid, player.Character)
    end
end

-- Add-ons folder safe creation
do
    local hb = getHealthBarRoot()
    if hb then
        local addons = hb:FindFirstChild("Add-ons_Folder")
        if not addons then
            local ok, err = pcall(function()
                local newFolder = Instance.new("Folder")
                newFolder.Name = "Add-ons_Folder"
                newFolder.Parent = hb
            end)
            if not ok then warn("[Healthbar] Could not create Add-ons_Folder: "..tostring(err)) end
        end
    end
end

print("[ HealthBar Death Sounds ] Successful loaded.")
-- =========== AVAILABLE NOW ===============

-- =========== CONTINUE EXPSETTINGS SCRPIT ======





-- Client: ExperienceUI_Client.lua
-- LocalScript — สร้าง UI, ควบคุม Toggle, รับ thumbnail จาก Client API

--!strict
local MarketplaceService = game:GetService("MarketplaceService")
local StarterGui = game:GetService("StarterGui")
local GuiService = game:GetService("GuiService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")

-- ======= Helpers =======
local function createUICorner(parent, scale, offset)
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(scale, offset)
    corner.Parent = parent
    return corner
end

-- ======= UIListLayout helpers =========
local HCenter = Enum.HorizontalAlignment.Center
local VCenter = Enum.VerticalAlignment.Center
local HLeft = Enum.HorizontalAlignment.Left
local VTop = Enum.VerticalAlignment.Top
local FillH = Enum.FillDirection.Horizontal
local FillV = Enum.FillDirection.Vertical

local function createUIListLayout(parent, scale, offset, HZ, VT, FILL)
    local list = Instance.new("UIListLayout")
    list.Padding = UDim.new(scale or 0, offset or 0)
    list.FillDirection = FILL or FillH
    list.HorizontalAlignment = HZ or HCenter
    list.VerticalAlignment = VT or VCenter
    list.Parent = parent
    return list
end

local function tweenObject(obj, props, time, style, direction)
    time = time or 0.28
    style = style or Enum.EasingStyle.Sine
    direction = direction or Enum.EasingDirection.Out
    local info = TweenInfo.new(time, style, direction)
    local t = TweenService:Create(obj, info, props)
    t:Play()
    return t
end

-- safe tween wrapper (ไม่ล่มถ้าหาก obj หาย)
local function safeTween(obj, props, time, style, dir)
    if not obj or not obj.Parent then return end
    pcall(function()
        tweenObject(obj, props, time, style, dir)
    end)
end

-- Safe Find path to the HealthBar where we parent our Menu
local function getHealthBar()
    local ok, topBar = pcall(function() return CoreGui:WaitForChild("TopBarApp", 5) end)
    if not ok or not topBar then return nil end
    local inner = topBar:FindFirstChild("TopBarApp")
    local left = inner and inner:FindFirstChild("UnibarLeftFrame")
    local healthBar = left and left:FindFirstChild("HealthBar")
    return healthBar
end

local healthBar = getHealthBar()
if not healthBar then
    warn("ExperienceUI: HealthBar not found. Aborting UI creation.")
    return
end

-- ======= Build UI =======
local expSettings = Instance.new("Folder")
expSettings.Name = "ExperienceSettings"
expSettings.Parent = healthBar

local menuGui = Instance.new("ScreenGui")
menuGui.Name = "Menu"
menuGui.ResetOnSpawn = false
menuGui.IgnoreGuiInset = true
menuGui.DisplayOrder = 2147483645
menuGui.Parent = expSettings

-- Main TopBar holder (small)
local mtb = Instance.new("Frame")
mtb.Name = "TopBar"
mtb.Position = UDim2.new(1, 0, 0.02, 0)
mtb.Size = UDim2.new(1, 0, 0, 43)
mtb.BackgroundTransparency = 1
mtb.Parent = menuGui
createUIListLayout(mtb, 0.005, 0, HCenter, VTop, FillH)

-- Holder image area (left)
local hr = Instance.new("Frame")
hr.Name = "Holder"
hr.Size = UDim2.new(0, 300, 1, 0)
hr.BackgroundColor3 = Color3.fromRGB(18, 18, 21)
hr.BackgroundTransparency = 0.08
hr.Parent = mtb
createUICorner(hr, 1, 0)
createUIListLayout(hr, 0, 10, HCenter, VCenter, FillH)

-- Top bar buttons container (tb) — starts collapsed
local tb = Instance.new("ScrollingFrame")
tb.Name = "TopButtons"
tb.Size = UDim2.new(0, 0, 0, 0) -- start collapsed
tb.BackgroundColor3 = Color3.fromRGB(18, 18, 21)
tb.BackgroundTransparency = 0.08
tb.ScrollBarThickness = 4
tb.CanvasSize = UDim2.new(0, 0, 0, 0)
tb.ScrollingDirection = Enum.ScrollingDirection.XY
tb.Visible = false
tb.Parent = mtb
createUICorner(tb, 0.02, 0)

-- AFTER TB
local grid = Instance.new("UIGridLayout")
grid.CellSize = UDim2.new(0, 70, 0, 70)   -- ขนาดแต่ละ cell (70x70 px)
grid.CellPadding = UDim2.new(0, 5, 0, 5)  -- ระยะห่างระหว่าง cell
grid.FillDirection = Enum.FillDirection.Horizontal
grid.SortOrder = Enum.SortOrder.Name
grid.Parent = tb
-- update visibility function for tb and its children
local function updateButtonsVisibility()
    local scale = tb.Size.X.Scale or 0

grid:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    tb.CanvasSize = UDim2.new(0, grid.AbsoluteContentSize.X, 0, grid.AbsoluteContentSize.Y)
end)
-- END TB

    -- ซ่อนปุ่มลูกถ้า scale เล็กกว่า 0.364
    if scale <= 0.364 then
        for _, child in ipairs(tb:GetChildren()) do
            if child:IsA("ImageButton") or child:IsA("TextButton") then
                child.Visible = false
            end
        end
    else
        for _, child in ipairs(tb:GetChildren()) do
            if child:IsA("ImageButton") or child:IsA("TextButton") then
                child.Visible = true
            end
        end
    end

    -- ซ่อน tb ทั้งกล่องเมื่อ scale == 0
    if scale <= 0 then
        tb.Visible = false
    else
        tb.Visible = true
    end
end

tb:GetPropertyChangedSignal("Size"):Connect(updateButtonsVisibility)

-- Settings button (on hr)
local Set = Instance.new("ImageButton")
Set.Name = "a3_Settings"
Set.Size = UDim2.new(0, 34, 0.8, 0)
Set.Image = "rbxassetid://130405026545116"
Set.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Set.BackgroundTransparency = 1
Set.Active = true
Set.Visible = false
Set.Parent = hr
createUICorner(Set, 1, 0)

-- Hamburger menu (hbm)
local hbm = Instance.new("ImageButton")
hbm.Name = "z9_HamburgerMenu"
hbm.Size = UDim2.new(0, 34, 0.8, 0)
hbm.Image = "rbxassetid://90812069520281"
hbm.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
hbm.BackgroundTransparency = 1
hbm.Active = true
hbm.Visible = false
hbm.Parent = hr
createUICorner(hbm, 1, 0)

-- Open/Close
local OC = Instance.new("ImageButton")
OC.Name = "a1_Open/Close"
OC.Size = UDim2.new(0, 34, 0.8, 0)
OC.Image = "rbxassetid://124801473660620"
OC.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
OC.BackgroundTransparency = 1
OC.Active = true
OC.Visible = false
OC.Parent = hr
createUICorner(OC, 1, 0)

-- GPT
local gpt = Instance.new("ImageButton")
gpt.Name = "z8_ChatGPT"
gpt.Size = UDim2.new(0, 34, 0.8, 0)
gpt.Image = "rbxassetid://100183093743893"
gpt.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
gpt.BackgroundTransparency = 1
gpt.Active = true
gpt.Visible = false
gpt.Parent = hr
createUICorner(gpt, 1, 0) --

-- Seacrh
local scrh = Instance.new("ImageButton")
scrh.Name = "a4_Search"
scrh.Size = UDim2.new(0, 34, 0.8, 0)
scrh.Image = "rbxassetid://115316941207686"
scrh.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
scrh.BackgroundTransparency = 1
scrh.Active = true
scrh.Visible = false
scrh.Parent = hr
createUICorner(scrh, 1, 0)

-- Wait1
local wa1 = Instance.new("Frame")
wa1.Name = "LoadFrame"
wa1.BackgroundTransparency = 1
wa1.Size = UDim2.new(0.5, 0, 1, 0)
wa1.Active = false
wa1.Visible = true
wa1.Parent = hr

-- Warning & Load
local wl = Instance.new("TextLabel")
wl.Name = "Warning & Load"
wl.BackgroundTransparency = 1
wl.Size = UDim2.new(1, 0, 1, 0)
wl.Text = "The ExperienceSettings has been deactivated for some reason."
wl.TextScaled = true
wl.Active = false
wl.TextColor3 = Color3.fromRGB(255, 255, 255)
wl.Visible = true
wl.Parent = wa1

print("[ Important Guis ] Successful loaded.")
-- ===== OC (Open/Close) wiring - REPLACEMENT =====
local OC_OPEN_IMAGE  = "rbxassetid://103716616779537" -- image when OPEN (show Set/hbm)
local OC_CLOSE_IMAGE = "rbxassetid://133900380566355"  -- image when CLOSED (hide Set/hbm)

-- target mtb X for "closed" (was 0.515 -> change to 0.48 as requested)
local MTB_X_CLOSED = 0.46
local MTB_X_OPEN   = 0 -- open => X = 0

-- hr sizes in px for open/close
local HR_WIDTH_OPEN  = 311
local HR_WIDTH_CLOSE = 90

-- safe setter for mtb position (uses tweenObject if available)
local function setMtbX(open, instant)
    local targetX = open and MTB_X_OPEN or MTB_X_CLOSED
    -- prefer existing mtb variable; fallback to searching inside menuGui
    local targetMtb = mtb or (menuGui and menuGui:FindFirstChild("TopBar", true))

    if OC and (OC:IsA("GuiObject")) then
        OC.Image = open and OC_OPEN_IMAGE or OC_CLOSE_IMAGE
    end

    if not targetMtb then
        return
    end

    local newPos = UDim2.new(targetX, 0, targetMtb.Position.Y.Scale, targetMtb.Position.Y.Offset)
    if instant then
        pcall(function() targetMtb.Position = newPos end)
    else
        pcall(function()
            if safeTween then
                safeTween(targetMtb, { Position = newPos }, 0.28, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            else
                tweenObject(targetMtb, { Position = newPos }, 0.28, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            end
        end)
    end
end

-- safe setter for hr width
local function setHrWidth(px, instant)
    if not hr then return end
    local newSize = UDim2.new(0, px, hr.Size.Y.Scale, hr.Size.Y.Offset)
    if instant then
        pcall(function() hr.Size = newSize end)
    else
        pcall(function()
            if safeTween then
                safeTween(hr, { Size = newSize }, 0.28, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            else
                tweenObject(hr, { Size = newSize }, 0.28, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
            end
        end)
    end
end

-- determine initial state from current UI (if Set/hbm visible => consider OPEN)
local ocState = false
if (Set and Set:IsA("GuiObject")) and Set.Visible then
    ocState = true
end

-- ensure OC image set
if OC and OC:IsA("ImageButton") then
    OC.Image = ocState and OC_OPEN_IMAGE or OC_CLOSE_IMAGE
end

-- function to apply the state
local function applyOCState(open, instant)
    ocState = open
    -- set mtb position
    setMtbX(open, instant)
    -- set hr size
    setHrWidth(open and HR_WIDTH_OPEN or HR_WIDTH_CLOSE, instant)
    -- toggle Set/hbm visibility (pcall to be safe)
    pcall(function()
        if Set and Set:IsA("GuiObject") then Set.Visible = open end
        if hbm and hbm:IsA("GuiObject") then hbm.Visible = open end
        if gpt and gpt:IsA("GuiObject") then gpt.Visible = open end
    end)
    -- update OC image
    if OC and OC:IsA("GuiObject") then
        OC.Image = open and OC_OPEN_IMAGE or OC_CLOSE_IMAGE
    end
end

-- initialize (instant)
applyOCState(ocState, true)

-- OC click toggles
if OC and OC:IsA("GuiObject") then
    OC.MouseButton1Click:Connect(function()
        -- toggle and animate
        ocState = not ocState
        applyOCState(ocState, false)
    end)
end

-- KEEP FORCING 

mtb.Position = UDim2.new(1, 0, 0.02, 0)
hr.Size = UDim2.new(0, 300, 1, 0)

--========================================================--
-- [ HRP Watcher System - No Debug / Clean Production ]
--========================================================--

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")

local gui_aV2 = CoreGui
	:WaitForChild("TopBarApp")
	:WaitForChild("TopBarApp")
	:WaitForChild("UnibarLeftFrame")
	:WaitForChild("HealthBar")
	:WaitForChild("ValueFolder")
	:WaitForChild("ValueGui")

local CHECK_INTERVAL = 0.05
local TIMEOUT = 3

local wa1_initial_pos, hr_initial_size, mtb_initial_pos
pcall(function()
	if wa1 then wa1_initial_pos = wa1.Position end
	if hr  then hr_initial_size = hr.Size end
	if mtb then mtb_initial_pos = mtb.Position end
end)

local function safeTween(obj, props, time, style)
	if not obj or not obj.Parent then
		return
	end
	pcall(function()
		local tweenInfo = TweenInfo.new(time or 0.25, style or Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
		local tw = TweenService:Create(obj, tweenInfo, props)
		tw:Play()
	end)
end

if type(resolveUI) ~= "function" then
	function resolveUI()
		local root = menuGui or expSettings or healthBar or CoreGui
		if not root then return nil end

		local function find(name)
			local ok, res = pcall(function()
				return root:FindFirstChild(name, true)
			end)
			if ok then return res end
			return nil
		end

		local rc_tb  = find("TopButtons") or find("TopBar") or find("TopBarApp")
		local rc_hr  = find("Holder") or find("HolderFrame") or find("a_holder")
		local rc_Set = find("a3_Settings") or find("Set") or find("SettingsButton")
		local rc_hbm = find("z9_HamburgerMenu") or find("Hamburger") or find("hbm")
		local rc_OC  = find("a1_Open/Close") or find("OC") or find("OpenClose")
		local rc_wa1 = find("LoadFrame") or find("wa1") or find("WarningFrame")
		local rc_wl  = find("Warning & Load") or find("wl") or find("WaitLabel")
		local rc_bg  = find("Background") or find("bg")
		local rc_mtb = find("TopBar") or find("MainTopBar") or find("mtb")
		local rc_gpt = find("z8_ChatGPT") or find("gpt") or find("ChatGPTButton")

		return rc_tb, rc_hr, rc_Set, rc_hbm, rc_OC, rc_wa1, rc_wl, rc_bg, rc_mtb, rc_gpt
	end
end

local ui_tb, ui_hr, ui_Set, ui_hbm, ui_OC, ui_wa1, ui_wl, ui_bg, ui_mtb, ui_gpt = resolveUI()
local timerMissing = 0
local isOpen = false

local function characterHasHRP(plr)
	if not plr or not plr.Character then return false end
	local char = plr.Character
	if char:FindFirstChild("HumanoidRootPart") then return true end
	if char.PrimaryPart and char.PrimaryPart:IsA("BasePart") then return true end
	for _, c in ipairs(char:GetChildren()) do
		if c:IsA("BasePart") and (string.find(c.Name, "Root") or string.find(c.Name, "Humanoid") or string.find(c.Name, "Head")) then
			return true
		end
	end
	return false
end

task.spawn(function()
	while true do
		task.wait(CHECK_INTERVAL)

		if not (ui_tb and ui_hr and ui_Set and ui_hbm and ui_OC and ui_gpt and ui_wa1 and ui_wl) then
			ui_tb, ui_hr, ui_Set, ui_hbm, ui_OC, ui_wa1, ui_wl, ui_bg, ui_mtb, ui_gpt = resolveUI()

			if not (ui_tb and ui_hr and ui_Set and ui_hbm and ui_OC and ui_gpt and ui_wa1 and ui_wl) then
				timerMissing += CHECK_INTERVAL
				if timerMissing >= TIMEOUT then end
				continue
			else
				timerMissing = 0
			end
		end

		local pl = Players.LocalPlayer
		local hasHRP = characterHasHRP(pl)

		if hasHRP then
			timerMissing = 0
			if not isOpen then
				if ui_mtb and ui_mtb.Parent then
					safeTween(ui_mtb, { Position = UDim2.new(0.46, 0, ui_mtb.Position.Y.Scale, ui_mtb.Position.Y.Offset) }, 0.28)
				elseif ui_tb and ui_tb.Parent then
					safeTween(ui_tb, { Position = UDim2.new(0, 0, ui_tb.Position.Y.Scale, ui_tb.Position.Y.Offset) }, 0.28)
				end

				safeTween(ui_hr, { Size = UDim2.new(0, 90, ui_hr.Size.Y.Scale, ui_hr.Size.Y.Offset) }, 0.28)
				pcall(function()
					if ui_Set  then ui_Set.Visible  = false end
					if ui_hbm then ui_hbm.Visible = false end
					if ui_OC  then ui_OC.Visible  = true end
					if ui_gpt then ui_gpt.Visible = false end
					if gui_aV2 then gui_aV2.Enabled = true end
				end)

				if ui_wa1 and ui_wa1.Parent then
					ui_wa1.Visible = false
					if wa1_initial_pos then
						pcall(function()
							safeTween(ui_wa1, { Position = wa1_initial_pos }, 0.22)
						end)
					end
				end
				if ui_wl and ui_wl.Parent then
					ui_wl.Visible = false
				end

				isOpen = true
			end
		else
			timerMissing += CHECK_INTERVAL
			if timerMissing >= TIMEOUT then
				if isOpen then
					if ui_mtb and ui_mtb.Parent then
						safeTween(ui_mtb, { Position = UDim2.new(0, 0, ui_mtb.Position.Y.Scale, ui_mtb.Position.Y.Offset) }, 0.28)
					elseif ui_tb and ui_tb.Parent then
						safeTween(ui_tb, { Position = UDim2.new(-5, 0, ui_tb.Position.Y.Scale, ui_tb.Position.Y.Offset) }, 0.28)
					end

					safeTween(ui_hr, { Size = UDim2.new(0, 300, ui_hr.Size.Y.Scale, ui_hr.Size.Y.Offset) }, 0.28)
					pcall(function()
						if ui_Set  then ui_Set.Visible  = false end
						if ui_hbm then ui_hbm.Visible = false end
						if ui_OC  then ui_OC.Visible  = false end
						if ui_gpt then ui_gpt.Visible = false end
						if gui_aV2 then gui_aV2.Enabled = false end
					end)

					if ui_wa1 and ui_wa1.Parent then
						ui_wa1.Visible = true
					end
					if ui_wl and ui_wl.Parent then
						ui_wl.Visible = true
					end

					isOpen = false
				end
			end
		end
	end
end)

print("[ Script_1 ] HRP-Watcher loaded successfully.")
-- ========= END ==========

-- Background panel (start OFFscreen to right)
local background = Instance.new("Frame")
background.Name = "Background"
background.Size = UDim2.new(0.35, 0, 0.9, 0)
background.Position = UDim2.new(1, 0, 0.1, 0) -- offscreen
background.BackgroundColor3 = Color3.new(0, 0, 0)
background.BackgroundTransparency = 0.5
background.Active = true
background.Parent = menuGui
createUICorner(background, 0.02, 0)

-- Experience image (thumbnail)
local experienceImage = Instance.new("ImageLabel")
experienceImage.Name = "ExperienceImage"
experienceImage.Size = UDim2.new(0.3, 0, 0.235, 0)
experienceImage.Position = UDim2.new(0.02, 0, 0.02, 0)
experienceImage.BackgroundTransparency = 0.5
experienceImage.Image = "rbxassetid://15057690464" -- default fallback
experienceImage.Parent = background
createUICorner(experienceImage, 0.05, 0)

-- Experience name label
local experienceName = Instance.new("TextLabel")
experienceName.Name = "ExperienceName"
experienceName.Size = UDim2.new(0.65, 0, 0.235, 0)
experienceName.Position = UDim2.new(0.34, 0, 0.02, 0)
experienceName.BackgroundTransparency = 1
experienceName.Text = "Getting API experience..."
experienceName.TextScaled = true
experienceName.TextColor3 = Color3.new(1, 1, 1)
experienceName.TextStrokeTransparency = 0
experienceName.Parent = background

-- Settings frame inside background
local settings = Instance.new("Frame")
settings.Name = "Settings"
settings.Size = UDim2.new(0.96, 0, 0.7, 0)
settings.Position = UDim2.new(0.02, 0, 0.28, 0)
settings.BackgroundColor3 = Color3.new(0, 0, 0)
settings.BackgroundTransparency = 0.5
settings.Active = true
settings.Parent = background
createUICorner(settings, 0.02, 0)

-- Pmax (players info)
local pmax = Instance.new("Folder"); pmax.Name = "Pmax"; pmax.Parent = settings
local playersLabel = Instance.new("TextLabel")
playersLabel.Name = "Players"; playersLabel.Size = UDim2.new(0.36,0,0.05,0)
playersLabel.Position = UDim2.new(0.02,0,0.02,0)
playersLabel.BackgroundColor3 = Color3.new(1,1,1); playersLabel.BackgroundTransparency = 0.2
playersLabel.Text = "Player :"; playersLabel.TextColor3 = Color3.new(0,0,0)
playersLabel.TextScaled = true; playersLabel.Parent = pmax; createUICorner(playersLabel,0.3,0)

local playerCount = Instance.new("TextLabel")
playerCount.Name = "PlayerCount"; playerCount.Size = UDim2.new(0.58,0,0.05,0)
playerCount.Position = UDim2.new(0.4,0,0.02,0)
playerCount.BackgroundColor3 = Color3.new(1,1,1); playerCount.BackgroundTransparency = 0.2
playerCount.Text = "0/0"; playerCount.TextColor3 = Color3.new(0,0,0)
playerCount.TextScaled = true; playerCount.Parent = pmax; createUICorner(playerCount,0.3,0)

-- SeeAll button
local seeAll = Instance.new("TextButton")
seeAll.Name = "SeeAll"
seeAll.Size = UDim2.new(0.96,0,0.05,0)
seeAll.Position = UDim2.new(0.02,0,0.08,0)
seeAll.BackgroundTransparency = 0.2
seeAll.TextScaled = true
seeAll.Text = "Open Roblox Settings"
seeAll.Parent = pmax
createUICorner(seeAll,0.3,0)

-- Buttons folder (Leave / Reset / Resume)
local Bs = Instance.new("Folder"); Bs.Name = "Buttons"; Bs.Parent = settings

local LE = Instance.new("TextButton")
LE.Name="Leave"; LE.Size=UDim2.new(0.96,0,0.05,0); LE.Position=UDim2.new(0.02,0,0.94,0)
LE.BackgroundTransparency=0.2; LE.BackgroundColor3=Color3.fromRGB(255,0,0)
LE.TextScaled=true; LE.Text="Leave The Experience"; LE.TextColor3=Color3.fromRGB(255,255,255)
LE.Parent = Bs; createUICorner(LE,0.3,0)

local Re = Instance.new("TextButton")
Re.Name="Reset character"; Re.Size=UDim2.new(0.96,0,0.05,0); Re.Position=UDim2.new(0.02,0,0.88,0)
Re.BackgroundTransparency=0.2; Re.BackgroundColor3=Color3.fromRGB(255,84,84)
Re.TextScaled=true; Re.Text="Reset character"; Re.TextColor3=Color3.fromRGB(255,255,255)
Re.Parent = Bs; createUICorner(Re,0.3,0)

local Rm = Instance.new("TextButton")
Rm.Name="Resume"; Rm.Size=UDim2.new(0.96,0,0.05,0); Rm.Position=UDim2.new(0.02,0,0.82,0)
Rm.BackgroundTransparency=0.2; Rm.BackgroundColor3=Color3.fromRGB(170,170,170)
Rm.TextScaled=true; Rm.Text="Resume"; Rm.TextColor3=Color3.fromRGB(255,255,255)
Rm.Parent = Bs; createUICorner(Rm,0.3,0)

-- Lines and toggles container
local L1 = Instance.new("Frame"); L1.Name="Line"; L1.Position=UDim2.new(0.02,0,0.15,0); L1.Size=UDim2.new(0.96,0,0.01,0); L1.Parent = settings
local L2 = Instance.new("Frame"); L2.Name="Line2"; L2.Position=UDim2.new(0.02,0,0.79,0); L2.Size=UDim2.new(0.96,0,0.01,0); L2.Parent = settings

local BFrame = Instance.new("Frame")
BFrame.Name = "B_Frame"; BFrame.Position = UDim2.new(0.02,0,0.18,0); BFrame.Size = UDim2.new(0.96,0,0.6,0)
BFrame.BackgroundTransparency = 1; BFrame.Parent = settings

local UIList = Instance.new("UIListLayout"); UIList.Padding = UDim.new(0.01,0); UIList.Parent = BFrame

-- Toggle builder
local toggleCount = 0
local function createToggle(parent, text, callback, defaultState)
    toggleCount += 1

    local f = Instance.new("Frame")
    f.Name = "Frame" .. toggleCount
    f.Size = UDim2.new(1,0,0.1,0)
    f.BackgroundTransparency = 0
    f.Parent = parent
    createUICorner(f, 0.3, 0)

    local bar = Instance.new("Frame")
    bar.Name = "Bar"
    bar.Size = UDim2.new(0.4,0,1,0)
    bar.Position = UDim2.new(0.6,0,0,0)
    bar.BackgroundColor3 = Color3.fromRGB(66,66,66)
    bar.Parent = f
    createUICorner(bar,0.3,0)

    local but = Instance.new("TextButton")
    but.Name = "ToggleButton"
    but.Size = UDim2.new(0.5,0,1,0)
    but.Parent = bar
    createUICorner(but,0.3,0)

    local txt = Instance.new("TextLabel")
    txt.Name = "Label"
    txt.Size = UDim2.new(0.6,0,1,0)
    txt.BackgroundTransparency = 1
    txt.TextScaled = true
    txt.TextXAlignment = Enum.TextXAlignment.Left
    txt.Text = text
    txt.Parent = f

    -- Toggle Logic
    local toggle = defaultState or false
    local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

    local function updateToggle(state, instant)
        toggle = state
        local props
        if toggle then
            props = {
                Position = UDim2.new(0,0,0,0),
                BackgroundColor3 = Color3.fromRGB(0,200,0)
            }
            but.Text = "ON"
        else
            props = {
                Position = UDim2.new(0.5,0,0,0),
                BackgroundColor3 = Color3.fromRGB(255,0,0)
            }
            but.Text = "OFF"
        end
        if instant then
            for k,v in pairs(props) do
                pcall(function() but[k] = v end)
            end
        else
            pcall(function() TweenService:Create(but, tweenInfo, props):Play() end)
        end
        if callback then
            pcall(callback, toggle)
        end
    end

    updateToggle(toggle, true)

    but.MouseButton1Click:Connect(function()
        updateToggle(not toggle)
    end)

    return f
end

-- ===== Functions loadstring() =====
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/ExperienceSettings-(loadstring)/Functions.lua"))()
print("[ Functions Loadstring ] Successful loaded.")

local lder = game:GetService("CoreGui"):WaitForChild("TopBarApp")
                                      :WaitForChild("TopBarApp")
                                      :WaitForChild("UnibarLeftFrame")
                                      :WaitForChild("HealthBar")
                                      :WaitForChild("ExperienceSettings")
                                      :WaitForChild("Menu")
                                      :WaitForChild("Load_Background")
                                      :WaitForChild("Skip")
                                      :WaitForChild("Loader")

local bkload = game:GetService("CoreGui"):WaitForChild("TopBarApp")
                                      :WaitForChild("TopBarApp")
                                      :WaitForChild("UnibarLeftFrame")
                                      :WaitForChild("HealthBar")
                                      :WaitForChild("ExperienceSettings")
                                      :WaitForChild("Menu")
                                      :WaitForChild("Load_Background")

task.wait(0.1)
lder.Size = UDim2.new(0.22,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.24,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.26,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.28,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.3,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.33,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.36,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.39,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.42,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.46,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.5,0,1,0)

-- ======== TOGGLE SWITCHS ===========

-- ValueLabels toggle (safe wait)
local valueGuiOK, ValueGui = pcall(function()
    return CoreGui:WaitForChild("TopBarApp"):WaitForChild("TopBarApp"):WaitForChild("UnibarLeftFrame"):WaitForChild("HealthBar"):WaitForChild("ValueFolder"):WaitForChild("ValueGui")
end)

createToggle(BFrame, "Enable ValueLabels", function(state)
    if valueGuiOK and ValueGui then
        pcall(function() ValueGui.Enabled = state end)
    end
end, (valueGuiOK and ValueGui and ValueGui.Enabled) or true)

-- Save / restore Lighting backup
local lightingBackup = {}
local function saveLightingSettings()
    lightingBackup = {
        ClockTime = Lighting.ClockTime,
        GeographicLatitude = Lighting.GeographicLatitude,
        ColorShift_Bottom = Lighting.ColorShift_Bottom,
        ColorShift_Top = Lighting.ColorShift_Top,
        Ambient = Lighting.Ambient
    }
end
local function restoreLightingSettings()
    if lightingBackup.ClockTime ~= nil then
        pcall(function()
            Lighting.ClockTime = lightingBackup.ClockTime
            Lighting.GeographicLatitude = lightingBackup.GeographicLatitude
            Lighting.ColorShift_Bottom = lightingBackup.ColorShift_Bottom
            Lighting.ColorShift_Top = lightingBackup.ColorShift_Top
            Lighting.Ambient = lightingBackup.Ambient
        end)
    end
end

-- Shaders toggle
createToggle(BFrame, "Shaders - Recommend graphics 5+", function(state)
    if state then
        pcall(saveLightingSettings)
        -- Sky
        if not Lighting:FindFirstChild("Shader-sky") then
            local sky = Instance.new("Sky")
            sky.Name = "Shader-sky"
            sky.SkyboxBk = "rbxassetid://600830446"
            sky.SkyboxDn = "rbxassetid://600831635"
            sky.SkyboxFt = "rbxassetid://600832720"
            sky.SkyboxLf = "rbxassetid://600886090"
            sky.SkyboxRt = "rbxassetid://600833862"
            sky.SkyboxUp = "rbxassetid://600835177"
            sky.SunTextureId = "rbxassetid://6281397906"
            sky.MoonTextureId = "rbxassetid://102013024637283"
            sky.SunAngularSize = 11
            sky.MoonAngularSize = 11
            sky.Parent = Lighting
        end
        -- DepthOfField
        if not Lighting:FindFirstChild("Shader-Field") then
            local dof = Instance.new("DepthOfFieldEffect")
            dof.Name = "Shader-Field"; dof.InFocusRadius = 25; dof.NearIntensity = 0.15; dof.Parent = Lighting
        end
        -- ColorCorrection
        if not Lighting:FindFirstChild("Shader-Correction") then
            local cc = Instance.new("ColorCorrectionEffect")
            cc.Name = "Shader-Correction"; cc.Brightness = -0.1; cc.Contrast = 0.3; cc.Parent = Lighting
        end
        -- SunRays
        if not Lighting:FindFirstChild("Shader-SunRays") then
            local sr = Instance.new("SunRaysEffect")
            sr.Name = "Shader-SunRays"; sr.Intensity = 0.103; sr.Spread = 0.88; sr.Parent = Lighting
        end
        -- Atmosphere (safe create)
        if not Lighting:FindFirstChild("Shader-Atmosphere") then
            pcall(function()
                local a = Instance.new("Atmosphere")
                a.Name = "Shader-Atmosphere"
                a.Density = 0.419
                pcall(function() a.Color = Color3.fromRGB(75,47,29) end)
                a.Parent = Lighting
            end)
        end
        -- lighting props
        Lighting.ClockTime = 6.509
        Lighting.GeographicLatitude = 33.91
        Lighting.ColorShift_Bottom = Color3.fromRGB(0,0,0)
        Lighting.ColorShift_Top = Color3.fromRGB(255,166,0)
        Lighting.Ambient = Color3.fromRGB(75,47,20)
    else
        local targets = {"Shader-sky","Shader-Field","Shader-Correction","Shader-SunRays","Shader-Atmosphere"}
        for _, name in ipairs(targets) do
            local obj = Lighting:FindFirstChild(name)
            if obj then pcall(function() obj:Destroy() end) end
        end
        pcall(restoreLightingSettings)
    end
end, false)

-- ต้องมี service เหล่านี้
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local localPlayer = Players.LocalPlayer

-- เก็บ coroutine / task ของ rainbow per-part เพื่อให้เราหยุดได้
local _rainbowTasks = {}

-- ลิสต์ part ที่จะพยายามหา (ลำดับความสำคัญ)
local ATTACH_CANDIDATES = {
    "HumanoidRootPart",
    "LowerTorso",
    "UpperTorso",
    "Torso",
    "Head"
}

-- หา part ที่จะ parent light ให้ (คืน BasePart หรือ nil)
local function findAttachPart(character)
    if not character then return nil end
    for _, name in ipairs(ATTACH_CANDIDATES) do
        local p = character:FindFirstChild(name)
        if p and p:IsA("BasePart") then
            return p
        end
    end
    return nil
end

-- สต็อกชื่อ light
local LIGHT_NAME = "Experience_Lighter"

-- หยุด rainbow loop สำหรับ part นั้น (ถ้ามี)
local function stopRainbowFor(part)
    if not part then return end
    local taskFlag = _rainbowTasks[part]
    if taskFlag and taskFlag._stop then
        taskFlag._stop = true
        _rainbowTasks[part] = nil
    end
end

-- สร้างหรือเปิด light
-- params:
--  state: boolean (true = on, false = off)
--  rainbow: boolean (true => ไล่สี)
--  opts (optional): table { Brightness = number, Range = number, Color = Color3 }
local function setLighterForCharacter(char, state, rainbow, opts)
    if not char then return false, "no char" end
    local attach = findAttachPart(char)
    if not attach then return false, "no attach part found" end

    -- ถ้ามี light อยู่แล้ว จัดการตาม state
    local existing = attach:FindFirstChild(LIGHT_NAME)
    if not state then
        -- ปิด/ลบ
        if existing then
            stopRainbowFor(attach)
            existing:Destroy()
        end
        return true, "turned off"
    end

    -- เปิด: สร้าง light ถ้ายังไม่มี
    if not existing then
        local light = Instance.new("PointLight")
        light.Name = LIGHT_NAME
        light.Brightness = (opts and opts.Brightness) or 2
        light.Range = (opts and opts.Range) or 25
        light.Color = (opts and opts.Color) or Color3.new(1,1,1)
        -- PointLight ไม่มี Angle property — เป็น omnidirectional อยู่แล้ว (เทียบกับ SpotLight)
        light.Parent = attach

        -- ถ้าผู้ใช้ทำลาย part เราควรหยุด task ด้วย (เช็ค AncestryChanged)
        attach.AncestryChanged:Connect(function(child, parent)
            if not parent then
                -- part ถูกลบ -> หยุด rainbow (ถ้ามี)
                stopRainbowFor(attach)
            end
        end)
    else
        -- update properties ถ้ามี
        existing.Brightness = (opts and opts.Brightness) or existing.Brightness
        existing.Range = (opts and opts.Range) or existing.Range
        existing.Color = (opts and opts.Color) or existing.Color
    end

    -- rainbow handling
    stopRainbowFor(attach) -- ทำความสะอาดก่อน
    if rainbow then
        -- สร้าง task flag เพื่อให้หยุดได้
        local flag = { _stop = false }
        _rainbowTasks[attach] = flag
        task.spawn(function()
            local t0 = tick()
            while not flag._stop and attach.Parent do
                local h = (tick() - t0) * 0.12 -- speed factor (ปรับได้)
                local color = Color3.fromHSV(h % 1, 1, 1)
                local light = attach:FindFirstChild(LIGHT_NAME)
                if light then
                    -- pcall ป้องกัน error ถ้า light ถูกทำลายในระหว่าง loop
                    pcall(function() light.Color = color end)
                else
                    break
                end
                task.wait(0.06)
            end
        end)
    else
        -- ตั้งสีปกติ (ขาว) เมื่อไม่ใช่ rainbow
        local light = attach:FindFirstChild(LIGHT_NAME)
        if light then
            pcall(function() light.Color = Color3.fromRGB(255,255,255) end)
        end
    end

    return true, "turned on"
end

-- ตัวช่วย: toggle สำหรับ local player (เรียกจาก toggle callback)
local function toggleForLocalPlayer(state, rainbow)
    local pl = Players.LocalPlayer
    if not pl then return end
    local char = pl.Character or pl.CharacterAdded:Wait()
    local ok, msg = pcall(function()
        return setLighterForCharacter(char, state, rainbow)
    end)
    if not ok then
        warn("[Lighter] failed:", msg)
    end
end

createToggle(BFrame, "White Light", function(state)
    toggleForLocalPlayer(state, false) -- ปกติ ขาว
end, false)

createToggle(BFrame, "RGB Light", function(state)
    toggleForLocalPlayer(state, true) -- RGB (เดิม rainbow)
end, false)



createToggle(BFrame, "ESP (Beta)", function(state)
    if state then
        if _G.EnableESP then pcall(_G.EnableESP) end
    else
        if _G.DisableESP then pcall(_G.DisableESP) end
    end
end, false)

-- ESP LocalScript (no FPS, with Team and plaID; Age & JoinDate unchanged)
-- วางใน StarterPlayerScripts (client-side)

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer

local ESPEnabled = false
local ESPData = {} -- [player] = { highlight = Instance, billboard = Instance, label = Instance, conn = RBXScriptConnection }

-- Config
local HIGHLIGHT_NAME = "ESPHighlight"
local BILLBOARD_NAME  = "ESPInfo"
local UPDATE_INTERVAL = 0.12 -- ไม่อัปเดตเร็วจนเกินไป (text/props)

-- Helpers
local function safeDestroy(obj)
	if obj and obj.Destroy then
		pcall(function() obj:Destroy() end)
	end
end

local function getTeamColor(player)
	if player and player.TeamColor and player.TeamColor.Color then
		return player.TeamColor.Color
	end
	return Color3.fromRGB(170,170,170) -- neutral gray
end

local function getTeamName(player)
	if player and player.Team and player.Team.Name and player.Team.Name ~= "" then
		return player.Team.Name
	end
	return "Neutral"
end

local function fmtNum(n, f)
	f = f or "%.2f"
	if type(n) ~= "number" then return string.format(f, 0) end
	return string.format(f, n)
end

-- Build info string (ตามที่ขอ: ห้ามแก้ Age/JoinDate logic)
local function buildInfoString(player, character)
	local username = player and player.Name or "Unknown"
	local userId = tostring(player and player.UserId or 0)

	-- health
	local hum = character and character:FindFirstChildOfClass("Humanoid")
	local health = hum and hum.Health or 0

	-- studs (ระยะจาก local player's HRP ถ้ามี)
	local studs = 0
	local localRoot = LocalPlayer and LocalPlayer.Character and (LocalPlayer.Character:FindFirstChild("HumanoidRootPart") or LocalPlayer.Character:FindFirstChild("Head"))
	local targetPart = character and (character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Head"))
	if localRoot and targetPart then
		studs = (targetPart.Position - localRoot.Position).Magnitude
	end

	-- team
	local teamName = getTeamName(player)

	-- Age & JoinDate (ใช้ logic เดิมของคุณ)
	local ageDays = player and player.AccountAge or 0 -- จำนวนวัน (ตาม Roblox property)
	local createdUnix = tick() - (ageDays * 86400) -- วิธีเดิมที่คุณใช้
	local joinDate = os.date("%Y-%m-%d", createdUnix)

	-- สร้าง string (ตามฟอร์แมตที่ขอรวม Team และ plaID)
	local info = string.format(
		"👤%s | 🕓Age: %d | 🗓️JoinDate: %s | ♥️Health: %s | 📏Studs: %s | 🏳️: %s | 🆔PlayerID: %s",
		username,
		ageDays,
		joinDate,
		string.format("%.2f", health),
		fmtNum(studs),
		teamName,
		userId
	)

	return info
end

-- Ensure Highlight
local function ensureHighlight(player, character)
	if not character then return nil end
	-- ค้นหา highlight ชื่อ HIGHLIGHT_NAME ภายใน character ก่อน
	local hi = character:FindFirstChild(HIGHLIGHT_NAME)
	if not hi then
		-- ถ้าไม่มี สร้างใหม่
		hi = Instance.new("Highlight")
		hi.Name = HIGHLIGHT_NAME
		hi.Parent = character
	end
	-- อัปเดต properties
	pcall(function()
		hi.Adornee = character
		local col = getTeamColor(player)
		hi.OutlineTransparency = 0
		hi.FillTransparency = 0.8
		hi.OutlineColor = col
		hi.FillColor = col
	end)
	return hi
end

-- Ensure BillboardGui + Label
local function ensureBillboard(player, character)
	if not character then return nil, nil end
	local adornee = character:FindFirstChild("Head") or character:FindFirstChild("HumanoidRootPart")
	if not adornee then return nil, nil end

	local bg = character:FindFirstChild(BILLBOARD_NAME)
	if not bg or not bg:IsA("BillboardGui") then
		-- ถ้ามี object แต่ไม่ใช่ Billboard ให้ลบทิ้งก่อน
		if bg and bg.Parent then
			pcall(function() bg:Destroy() end)
		end
		bg = Instance.new("BillboardGui")
		bg.Name = BILLBOARD_NAME
		bg.Parent = character
		bg.Adornee = adornee
		bg.AlwaysOnTop = true
		bg.Size = UDim2.new(0,250,0,40)
		bg.StudsOffset = Vector3.new(0,3,0)
	end

	local tl = bg:FindFirstChild("ESP_Label")
	if not tl or not tl:IsA("TextLabel") then
		if tl and tl.Parent then pcall(function() tl:Destroy() end) end
		tl = Instance.new("TextLabel")
		tl.Name = "ESP_Label"
		tl.Parent = bg
		tl.Size = UDim2.new(1,0,1,0)
		tl.BackgroundTransparency = 1
		tl.TextScaled = true
		tl.TextStrokeTransparency = 0
		tl.Font = Enum.Font.SourceSans
		tl.TextXAlignment = Enum.TextXAlignment.Left
		tl.TextYAlignment = Enum.TextYAlignment.Center
	end

	-- set text color to team color
	pcall(function()
		tl.TextColor3 = getTeamColor(player)
	end)

	return bg, tl
end

-- add ESP to a single player
local function addESPToPlayer(player)
	if not player or player == LocalPlayer then return end
	if ESPData[player] then return end

	ESPData[player] = { highlight = nil, billboard = nil, label = nil, conn = nil }

	local function setupCharacter(character)
		if not character then return end
		-- wait for HRP or head at most a short time
		local hrp = character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Head")
		-- ensure highlight + billboard
		local hi = ensureHighlight(player, character)
		local bg, tl = ensureBillboard(player, character)
		ESPData[player].highlight = hi
		ESPData[player].billboard = bg
		ESPData[player].label = tl
		-- initial text
		if tl then
			pcall(function() tl.Text = buildInfoString(player, character) end)
		end
	end

	-- If character exists now
	if player.Character then
		setupCharacter(player.Character)
	end

	-- Connect CharacterAdded to re-setup
	local charConn = player.CharacterAdded:Connect(function(char)
		-- wait briefly for parts to load
		char:WaitForChild("HumanoidRootPart", 2)
		setupCharacter(char)
	end)

	-- store connection so we can disconnect later (also we will create updater connection below)
	ESPData[player].charConn = charConn

	-- Updater (Heartbeat) per-player: update text/colors & highlight color each interval
	local acc = 0
	local blinkAcc = 0
	local blinkState = false
	local updConn
	updConn = RunService.Heartbeat:Connect(function(dt)
		if not ESPEnabled then return end
		acc = acc + dt
		blinkAcc = blinkAcc + dt
		-- update at UPDATE_INTERVAL
		if acc >= UPDATE_INTERVAL then
			acc = 0
			local rec = ESPData[player]
			if not rec then
				-- safety
				if updConn then updConn:Disconnect() end
				return
			end
			local char = player.Character
			if not char then
				-- no character: ensure UI removed
				if rec.highlight and rec.highlight.Parent then safeDestroy(rec.highlight) end
				if rec.billboard and rec.billboard.Parent then safeDestroy(rec.billboard) end
				rec.highlight, rec.billboard, rec.label = nil, nil, nil
				return
			end

			-- ensure created if missing
			if not (rec.highlight and rec.highlight.Parent) then
				rec.highlight = ensureHighlight(player, char)
			end
			if not (rec.billboard and rec.billboard.Parent and rec.label) then
				local bg, tl = ensureBillboard(player, char)
				rec.billboard, rec.label = bg, tl
			end

			-- update text
			if rec.label then
				pcall(function()
					rec.label.Text = buildInfoString(player, char)
					rec.label.TextColor3 = getTeamColor(player)
				end)
			end

			-- update highlight color
			pcall(function()
				if rec.highlight then
					local col = getTeamColor(player)
					rec.highlight.FillColor = col
					rec.highlight.OutlineColor = col
				end
			end)
		end

		-- blink logic: (ไม่ใช่ส่วนหลัก แต่เก็บไว้ถ้าต้องการในอนาคต)
		-- ถ้าต้องการกระพริบเมื่อเลือดต่ำ ใช้ blinkAcc เปรียบกับ 0.05 แล้วสลับสี
	end)

	ESPData[player].conn = updConn
end

local function removeESPForPlayer(player)
	local rec = ESPData[player]
	if not rec then return end
	pcall(function()
		if rec.conn then rec.conn:Disconnect() end
		if rec.charConn then rec.charConn:Disconnect() end
		if rec.highlight and rec.highlight.Parent then safeDestroy(rec.highlight) end
		if rec.billboard and rec.billboard.Parent then safeDestroy(rec.billboard) end
	end)
	ESPData[player] = nil
end

-- Public controls
local function addAll()
	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= LocalPlayer then
			addESPToPlayer(p)
		end
	end
end

local function removeAll()
	for p, _ in pairs(ESPData) do
		removeESPForPlayer(p)
	end
end

-- Hook player join/leave to keep realtime
Players.PlayerAdded:Connect(function(p)
	if not ESPEnabled then return end
	if p ~= LocalPlayer then addESPToPlayer(p) end
end)
Players.PlayerRemoving:Connect(function(p)
	removeESPForPlayer(p)
end)

-- enable / disable functions
function enableESP()
	if ESPEnabled then return end
	ESPEnabled = true
	addAll()
end

function disableESP()
	if not ESPEnabled then return end
	ESPEnabled = false
	removeAll()
end

-- optionally expose to _G if you want toggling from elsewhere:
_G.EnableESP = enableESP
_G.DisableESP = disableESP

-- (example) start disabled by default — caller should call enableESP()
-- enableESP()

lder.Size = UDim2.new(0.65,0,1,0)
-- ===== END ESP implementation =====

-- ===== เข้าถึง DamageOverlay โดยตรงใน CoreGui =====
local DamageOverlay = game:GetService("CoreGui"):WaitForChild("DamageOverlay")

-- ใช้ฟังก์ชัน createToggle ที่คุณมีอยู่แล้ว
createToggle(BFrame, "Damage Overlay", function(state)
	DamageOverlay.Enabled = state
end, false) -- true = เปิดเริ่มต้น

-- ===== END DAMAGEOVERLAY =====

task.spawn(function()
    local core = game:GetService("CoreGui")
    local bg = core:WaitForChild("TopBarApp"):WaitForChild("TopBarApp")
        :WaitForChild("UnibarLeftFrame"):WaitForChild("HealthBar")
        :WaitForChild("ExperienceSettings"):WaitForChild("Menu")
        :WaitForChild("Background")

    local inner = bg:WaitForChild("Inner_Background")
    inner.Visible = false

    createToggle(
        BFrame, -- Parent = ตัวแปรที่นายกำหนดไว้
        "MoreToggles",
        function(state)
            pcall(function()
                inner.Visible = state
            end)
        end,
        false -- Default = OFF
    )
end)

-- ===== END MORETOGGLES =====

-- ============== IMAGE BUTTONS ==============
-- helper: ImageButton creation (parented to tb by default)
local function createImageButton(name, r, g, b, bt, imageId, visible, parentFrame)
    parentFrame = parentFrame or tb
    local im = Instance.new("ImageButton")
    im.Name = tostring(name)
    im.Size = UDim2.new(0, 70, 0, 70)
    im.BackgroundTransparency = bt or 1
    im.BackgroundColor3 = Color3.fromRGB(r or 255, g or 255, b or 255)
    im.Visible = (visible ~= false)
    if imageId then
        local idstr = tostring(imageId)
        if idstr:match("^rbxassetid://") then
            im.Image = idstr
        else
            im.Image = "rbxassetid://" .. idstr
        end
    end
    im.Parent = parentFrame
    createUICorner(im, 1, 0)
    return im
end

-- CTB1
local ctb1 = createImageButton("a1_INF YIELD", 50, 50, 50, 0.2, "139665085719816", false)
if ctb1 then
    ctb1.MouseButton1Click:Connect(function()
        pcall(function()
            loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
        end)
    end)
end

-- CTB2
local ctb2 = createImageButton("a2_Dex", 50, 50, 50, 0.2, "112062985804784", false)
if ctb2 then
    ctb2.MouseButton1Click:Connect(function()
        pcall(function()
            loadstring(game:HttpGet("https://github.com/AZYsGithub/DexPlusPlus/releases/latest/download/out.lua"))()
        end)
    end)
end

-- CTB3
local ctb3 = createImageButton("a3_Keyboard", 50, 50, 50, 0.2, "76210662677344", false)
if ctb3 then
    ctb3.MouseButton1Click:Connect(function()
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Xxtan31/Ata/main/deltakeyboardcrack.txt", true))()
        end)
    end)
end

-- CTB4
local ctb4 = createImageButton("a4_Rochips", 50, 50, 50, 0.2, "111409127543607", false)
if ctb4 then
    ctb4.MouseButton1Click:Connect(function()
        pcall(function()
            loadstring(game:HttpGet("https://glot.io/snippets/gzrux646yj/raw/main.ts"))()
        end)
    end)
end

-- CTB5
local ctb5 = createImageButton("a5_Ketamine", 50, 50, 50, 0.2, "73739594473443", false)
if ctb5 then
    ctb5.MouseButton1Click:Connect(function()
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/InfernusScripts/Ketamine/refs/heads/main/Ketamine.lua"))()
        end)
    end)
end

-- CTB6
local ctb6 = createImageButton("a6_AFEM", 50, 50, 50, 0.2, "70633192931522", false)
if ctb6 then
    ctb6.MouseButton1Click:Connect(function()
        pcall(function()
            loadstring(game:HttpGet("https://yarhm.mhi.im/scr?channel=afem", true))()
        end)
    end)
end

-- CTB7
local ctb7 = createImageButton("a7_Chat", 50, 50, 50, 0.2, "78687742773593", false)
if ctb7 then
    ctb7.MouseButton1Click:Connect(function()
        pcall(function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/ThacG/Xyros/refs/heads/main/test"))()
        end)
    end)
end

-- CTB8
local ctb8 = createImageButton("a8_ExeTest", 50, 50, 50, 0.2, "7128117167", false)
if ctb8 then
    ctb8.MouseButton1Click:Connect(function()
        pcall(function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/InfernusScripts/Executor-Tests/main/Identity/Test.lua"))()
        end)
    end)
end

-- CTB9
local ctb9 = createImageButton("a9_REM", 50, 50, 50, 0.2, "91782079102380", false)
if ctb9 then
    ctb9.MouseButton1Click:Connect(function()
        pcall(function()
    loadstring(game:HttpGet("https://e-vil.com/anbu/rem.lua"))()
        end)
    end)
end

-- CTB10
local ctb10 = createImageButton("b1_GameProber", 50, 50, 50, 0.2, "14944375078", false)
if ctb10 then
    ctb10.MouseButton1Click:Connect(function()
        pcall(function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/Joystickplays/gameprober-lua/main/gp.lua"))()
        end)
    end)
end

-- CTB11
local ctb11 = createImageButton("b2_AudioPlayer", 50, 50, 50, 0.2, "93917898537008", false)
if ctb11 then
	ctb11.MouseButton1Click:Connect(function()
			pcall(function()
loadstring(game:HttpGet('https://raw.githubusercontent.com/Emerson2-creator/Scripts-Roblox/refs/heads/main/audioPlayer.lua'))()
		end)
	end)
end

-- CTB12
local ctb12 = createImageButton("b3_ExperienceSettings_Executor", 64, 255, 74, 0.2, "116278455133074", false)
if ctb12 then
	ctb12.MouseButton1Click:Connect(function()
			pcall(function()
loadstring(game:HttpGet("https://bit.ly/4tJ4Jbn"))()
		end)
	end)
end

-- ===== TEXT BUTTONS =======
-- helper: TextButton creation (parented to tb by default)
local function createTextButton(name, r, g, b, bt, text, scaled, visible, tr, tg, tb2, parentFrame)
    parentFrame = parentFrame or tb
    local txtb = Instance.new("TextButton")
    txtb.Name = tostring(name)
    -- fixed size: width 34 px (avoid stretching), full height fraction
    txtb.Size = UDim2.new(0, 70, 0, 70)
    txtb.BackgroundTransparency = bt or 1
    txtb.BackgroundColor3 = Color3.fromRGB(r or 255, g or 255, b or 255)
    txtb.Visible = (visible ~= false)
    txtb.Text = tostring(text or "")
    -- text color separate (defaults to white)
    txtb.TextColor3 = Color3.fromRGB(tr or 255, tg or 255, tb2 or 255)
    txtb.Font = Enum.Font.Legacy
    txtb.TextScaled = (scaled ~= false)
    txtb.Parent = parentFrame
    createUICorner(txtb, 1, 0)
    return txtb
end
-- ========

-- CTL1
local ctl1 = createTextButton("b3_EmoteSelect", 50, 50, 50, 0.2, "Emote Select", true, false)
if ctl1 then
	ctl1.MouseButton1Click:Connect(function()
			pcall(function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/7yd7/Hub/refs/heads/Branch/GUIS/Emotes.lua"))()
		end)
	end)
end

-- CTL2
local ctl2 = createTextButton("b4_UniversalChat", 50, 50, 50, 0.2, "Universal Chat", true, false)
if ctl2 then
	ctl2.MouseButton1Click:Connect(function()
			pcall(function()
loadstring(game:HttpGet("https://raw.githubusercontent.com/theneutral0ne/UniversalChat/main/installer.lua"))()
		end)
	end)
end



-- ========
task.wait(0.1)
lder.Size = UDim2.new(0.55,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.6,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.65,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.7,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.71,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.72,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.74,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.78,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.79,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.8,0,1,0)

-- ============== Background open/close functions ==============
local BG_X_ON = 0.65
local BG_X_OFF = 1.6
local function backgroundIsOpen()
    return math.abs(background.Position.X.Scale - BG_X_ON) < 0.01
end
local function setBackgroundState(open, instant)
    local target = open and BG_X_ON or BG_X_OFF
    local newPos = UDim2.new(target, 0, background.Position.Y.Scale, 0)
    if instant then
        background.Position = newPos
    else
        tweenObject(background, { Position = newPos }, 0.32, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    end
    pcall(function()
        if open then tweenObject(Set, { Rotation = 0 }, 0.22) else tweenObject(Set, { Rotation = 180 }, 0.22) end
    end)
end

Set.MouseButton1Click:Connect(function()
    setBackgroundState(not backgroundIsOpen(), false)
end)

-- ============== Thumbnail + ExperienceName load ==========
pcall(function()
    local ok, info = pcall(function() return MarketplaceService:GetProductInfo(game.PlaceId) end)
    if ok and type(info) == "table" then
        if info.IconImageAssetId and info.IconImageAssetId ~= 0 then
            experienceImage.Image = "rbxassetid://" .. tostring(info.IconImageAssetId)
        else
            -- try GetGameThumbnailAsync fallback
            local sOk, content = pcall(function()
                return Players:GetGameThumbnailAsync(game.PlaceId, Enum.ThumbnailType.Icon, Enum.ThumbnailSize.Size512x512)
            end)
            if sOk and type(content) == "string" and #content > 4 then
                experienceImage.Image = content
            else
                experienceImage.Image = "rbxassetid://15057690464"
            end
        end
        if info.Name then experienceName.Text = tostring(info.Name) end
    else
        -- fallback: try thumbnail call directly
        local sOk, content = pcall(function()
            return Players:GetGameThumbnailAsync(game.PlaceId, Enum.ThumbnailType.Icon, Enum.ThumbnailSize.Size512x512)
        end)
        if sOk and type(content) == "string" and #content > 4 then
            experienceImage.Image = content
        else
            experienceImage.Image = "rbxassetid://15057690464"
        end
        pcall(function() experienceName.Text = tostring(game.Name or ("Place " .. tostring(game.PlaceId))) end)
    end
end)

-- ============== playerCount realtime ==========
local function updatePlayerCounter()
    local count = #Players:GetPlayers()
    local maxPlayers = Players.MaxPlayers or 0
    playerCount.Text = tostring(count) .. "/" .. tostring(maxPlayers)
end
updatePlayerCounter()
Players.PlayerAdded:Connect(updatePlayerCounter)
Players.PlayerRemoving:Connect(updatePlayerCounter)
do
    local tick = 0
    RunService.Heartbeat:Connect(function(dt)
        tick += dt
        if tick >= 2 then tick = 0 updatePlayerCounter() end
    end)
end

-- ============== LE / Re / Rm behaviors ==========
LE.MouseButton1Click:Connect(function()
	pcall(function()
		if game then
			game:Shutdown()
		end
	end)
end)

Re.MouseButton1Click:Connect(function()
    local plr = localPlayer
    if not plr then return end
    local char = plr.Character
    if char then
        local humanoid = char:FindFirstChildOfClass("Humanoid")
        if humanoid then
            pcall(function() humanoid.Health = 0 end)
        else
            pcall(function() char:BreakJoints() end)
        end
    end
end)

Rm.MouseButton1Click:Connect(function()
    setBackgroundState(false, false)
end)

-- Keep Set rotation consistent
RunService.Heartbeat:Connect(function()
    local open = backgroundIsOpen()
    if open then
        if math.abs(Set.Rotation - 0) > 1 then Set.Rotation = 0 end
    else
        if math.abs(Set.Rotation - 180) > 1 then Set.Rotation = 180 end
    end
end)

-- ============== Hamburger toggle (tb size + icons) ==============
local tbOpen = false
local function toggleTB()
    tbOpen = not tbOpen
    if tbOpen then
        tweenObject(tb, { Size = UDim2.new(0.365, 0, 10, 0) }, 0.28, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        hbm.Image = "rbxassetid://120294896493053"
    else
        tweenObject(tb, { Size = UDim2.new(0, 0, 0, 0) }, 0.28, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
        hbm.Image = "rbxassetid://90812069520281"
    end
end
hbm.MouseButton1Click:Connect(toggleTB)

print("[ Functions ] Successful loaded.")

-- IMAGE IDS
local IMAGE_CLOSED = "rbxassetid://115316941207686"
local IMAGE_OPEN   = "rbxassetid://108649442107108"

-- STATE
local isOpen = false

-- FUNCTION: CHECK HR SIZE
local function updateVisibility()
	if hr.Size.X.Offset < 300 then
		scrh.Visible = false
	else
		scrh.Visible = true
	end
end

-- FUNCTION: TOGGLE IMAGE
local function toggleImage()
	isOpen = not isOpen
	scrh.Image = isOpen and IMAGE_OPEN or IMAGE_CLOSED
end

-- INITIAL
scrh.Image = IMAGE_CLOSED
updateVisibility()

-- LISTEN SIZE CHANGE
hr:GetPropertyChangedSignal("Size"):Connect(updateVisibility)

-- BUTTON CLICK
scrh.MouseButton1Click:Connect(toggleImage)

-- ====FUNCTION CORNER=====
local function Corner(Scale, Offset, Parent)
  local Corner = Instance.new("UICorner")
  Corner.CornerRadius = UDim.new(Scale or 0, Offset or 0)
  Corner.Parent = Parent
  return Corner
end
-- =====END FUNCTION CORNER====

-- =====FUNCTION UILISTLAYOUT=====
local HCenter = Enum.HorizontalAlignment.Center
local VCenter = Enum.VerticalAlignment.Center
local HLeft = Enum.HorizontalAlignment.Left
local VTop = Enum.VerticalAlignment.Top
local HRight = Enum.HorizontalAlignment.Right
local VBottom = Enum.VerticalAlignment.Bottom
local FillH = Enum.FillDirection.Horizontal
local FillV = Enum.FillDirection.Vertical
local SCustom = Enum.SortOrder.Custom
local SLayout = Enum.SortOrder.LayoutOrder
local SName = Enum.SortOrder.Name

local function UIList(parent, scale, offset, HZ, VT, SO, FILL)
    local list = Instance.new("UIListLayout")
    list.Padding = UDim.new(scale or 0, offset or 0)
    list.FillDirection = FILL or FillH
    list.HorizontalAlignment = HZ or HCenter
    list.VerticalAlignment = VT or VCenter
    list.SortOrder = SO or SName
    list.Parent = parent
    return list
end
-- =====END FUNCTION UILISTLAYOUT=====

-- ====FUNCTION UISTROKE=====
local ASMBorder = Enum.ApplyStrokeMode.Border
local ASMContextual = Enum.ApplyStrokeMode.Contextual

local LJMBevel = Enum.LineJoinMode.Bevel
local LJMMiter = Enum.LineJoinMode.Miter
local LJMRound = Enum.LineJoinMode.Round

local function Stroke(parent, ASM, R, G, B, LJM, Tn, Transy)
    local stroke = parent:FindFirstChildOfClass("UIStroke") or Instance.new("UIStroke")
    stroke.ApplyStrokeMode = ASM or ASMBorder
    stroke.Color = Color3.fromRGB(R or 255, G or 255, B or 255)
    stroke.LineJoinMode = LJM or LJMRound
    stroke.Thickness = Tn or 1
    stroke.Transparency = Transy or 0
    stroke.Parent = parent
    return stroke
end
-- =====END FUNCTION UISTROKE=====

-- ====FUNCTION UIGRADIENT=====
local function Gradient(parent, rotation, offsetX, offsetY, ...)
    local grad = parent:FindFirstChildOfClass("UIGradient") or Instance.new("UIGradient")
    grad.Rotation = rotation or 0
    grad.Offset = Vector2.new(offsetX or 0, offsetY or 0)

    local colors = {...}
    local keypoints = {}

    if #colors == 0 then
        keypoints = { ColorSequenceKeypoint.new(0, Color3.new(1,1,1)), ColorSequenceKeypoint.new(1, Color3.new(1,1,1)) }
    elseif #colors == 1 then
        keypoints = { ColorSequenceKeypoint.new(0, colors[1]), ColorSequenceKeypoint.new(1, colors[1]) }
    else
        for i, c in ipairs(colors) do
            local t = (i-1) / (#colors-1)
            table.insert(keypoints, ColorSequenceKeypoint.new(t, c))
        end
    end

    grad.Color = ColorSequence.new(keypoints)
    grad.Parent = parent
    return grad
end
-- =====END FUNCTION UIGRADIENT=====

-- ====FUNCTION UIPADDING (ตามลำดับ Roblox)=====
local function Padding(parent, bottom, left, right, top)
    local pad = parent:FindFirstChildOfClass("UIPadding") or Instance.new("UIPadding")
    local function toUDim(value)
        if typeof(value) == "UDim" then
            return value
        elseif type(value) == "number" then
            return UDim.new(0, value)
        elseif type(value) == "table" and #value >= 2 then
            return UDim.new(value[1] or 0, value[2] or 0)
        else
            return UDim.new(0, 0)
        end
    end

    pad.PaddingBottom = toUDim(bottom)
    pad.PaddingLeft   = toUDim(left)
    pad.PaddingRight  = toUDim(right)
    pad.PaddingTop    = toUDim(top)

    pad.Parent = parent
    return pad
end
-- =====END FUNCTION UIPADDING=====bbbk

lder.Size = UDim2.new(0.9,0,1,0)

-- ===== ChatGPT =====
local bk = Instance.new("Frame")
bk.Name = "ChatGPT"
bk.Size = UDim2.new(0.4,0,0.4,0)
bk.Position = UDim2.new(0.3,0,0.3,0)
bk.BackgroundColor3 = Color3.fromRGB(0,0,0)
bk.BackgroundTransparency = 0.5
bk.Visible = true
bk.Parent = menuGui
Corner(0, 10, bk)
Stroke(bk, ASMBorder, 255, 255, 255, LSMRound, 1, 0)

local line = Instance.new("Frame")
line.Name = "Line"
line.Position = UDim2.new(0.02,0,0.2,0)
line.Size = UDim2.new(0.96,0,0,1)
line.BackgroundColor3 = Color3.fromRGB(255,255,255)
line.Parent = bk
Corner(1,0,line)

local lg = Instance.new("TextLabel")
lg.Name = "Disconnected"
lg.BackgroundTransparency = 1
lg.Position = UDim2.new(0.02,0,0.02,0)
lg.Size = UDim2.new(0.96,0,0.2,0)
lg.Text = "loadstring failed"
lg.TextSize = 20
lg.TextColor3 = Color3.fromRGB(255,255,255)
lg.Parent = bk

local wt = Instance.new("TextLabel")
wt.Name = "warn"
wt.BackgroundTransparency = 1
wt.Position = UDim2.new(0.02,0,0.25,0)
wt.Size = UDim2.new(0.96,0,0.35,0)
wt.Text = [[ 
Something was wrong with AI-OpenSource.
please wait for next update!
]]
wt.TextScaled = true
wt.TextColor3 = Color3.fromRGB(255,255,255)
wt.Parent = bk

local wl = Instance.new("TextLabel")
wl.Name = "error"
wl.BackgroundTransparency = 1
wl.Position = UDim2.new(0.02,0,0.65,0)
wl.Size = UDim2.new(0.96,0,0.13,0)
wl.Text = "Error Code: -3"
wl.TextScaled = true
wl.TextColor3 = Color3.fromRGB(255,255,255)
wl.Parent = bk

-- 🔸 ตำแหน่ง
local POS_OFF = UDim2.new(0.25, 0, 1, 0)    -- ปิด
local POS_ON  = UDim2.new(0.25, 0, 0.18, 0)  -- เปิด

-- 🔸 ตั้งค่า Tween
local tweenInfo = TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)

-- 🔸 ฟังก์ชันเช็กว่า bk เปิดอยู่ไหม
local function isBkOn()
	local y = bk.Position.Y.Scale
	return y <= 0.31 -- ถ้าอยู่ประมาณ 0.3 ถือว่าเปิด
end

-- 🔸 ฟังก์ชันสลับสถานะ
local function toggleBk()
	local goal = isBkOn() and POS_OFF or POS_ON
	TweenService:Create(bk, tweenInfo, { Position = goal }):Play()
end

-- 🔸 ตั้งค่าเริ่มต้นให้ปิดไว้ก่อน
bk.Position = POS_OFF

-- 🔸 เมื่อกดที่ gpt ให้สลับตำแหน่งได้เรื่อย ๆ
gpt.MouseButton1Click:Connect(toggleBk)

task.wait(0.1)
lder.Size = UDim2.new(0.81,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.82,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.83,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.84,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.85,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.86,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.87,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.88,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.89,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.9,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.91,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.92,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.95,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.98,0,1,0)
task.wait(0.1)
lder.Size = UDim2.new(0.99,0,1,0)
task.wait(0.5)
lder.Size = UDim2.new(1,0,1,0)
task.wait(0.5)
bkload.Visible = false
