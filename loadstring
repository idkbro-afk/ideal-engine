-- Well 2.352

--[[
 HELLO SCRIPTBLOX AND HAXHELL USERSSSS WHAT THE ACTUALLY HELL YOU GUYS
 DOING IN THIS RAW SCRIPT? AND YES IT'S MIGHT LOOK TOO AI SLOP BUT -
 GUIS ARE NOT MADE BY AI, ANYWAY I'M STILL LEARNING SCRIPT!

 THANK YOU FOR READINGGGGG *COUGH* *COUGH* *COUGH* *Died adorably*
]]

-- Reset
getgenv().ES = nil

-- Load SetUp
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/loadstring/ExperienceSettings-SetUp.lua"))()

repeat task.wait() until getgenv().__ES_READY or getgenv().__ES_BANNED

if getgenv().__ES_BANNED then
	warn("You're banned from ExperienceSettings.")
	return
end


local ES = getgenv().ES

-- ===== ERROR SYSTEM =====
local function catch(err)
	ES.error = true
	ES.lastError = tostring(err)
	warn("[ ES ERROR ]:", err)
end

local function safe(f)
	local ok, err = pcall(f)
	if not ok then
		catch(err)
	end
end

-- ===== SCAN SYSTEM =====
local visited = {}
local queue = {}

local function extractURLs(src)
	local urls = {}

	for url in string.gmatch(src, 'HttpGet%(%s*"(.-)"%s*%)') do
		table.insert(urls, url)
	end

	return urls
end

local function scan(url)
	if visited[url] then return end
	visited[url] = true

	local ok, src = pcall(function()
		return game:HttpGet(url)
	end)

	if not ok then
		return catch("HttpGet failed: "..tostring(src))
	end

	if not src or src == "" then
		return catch("Empty script: "..url)
	end

	-- ✅ compile check
	local f, err = loadstring(src)
	if not f then
		return catch("Compile error: "..err.." | "..url)
	end

	-- 🔍 หา loadstring ต่อ
	for _, u in ipairs(extractURLs(src)) do
		table.insert(queue, u)
	end

	-- 📊 progress จริง
	local count = 0
	for _ in pairs(visited) do count += 1 end
	ES.progress = math.clamp(count * 15, 0, ES.max)
end

-- ===== MAIN =====
local function main()

	-- 🔥 ใช้ Loader ตัวใหม่
	table.insert(queue, "https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/loadstring/Loader.lua")

	while #queue > 0 do
		if ES.error then break end

		local url = table.remove(queue, 1)

		safe(function()
			scan(url)
		end)
	end

end

-- RUN
safe(main)

-- DONE
task.delay(0.5,function()
	if not ES.error then
		ES.progress = ES.max
		ES.done = true
	end
end)

-- Loader
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/ExperienceSettings-(loadstring)/Loader.lua"))()

local Players = game:GetService("Players")
local StarterGui = game:GetService("StarterGui")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

local hrp = character:WaitForChild("HumanoidRootPart")
StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.Health, true)


-- Monitor & auto-run (executor)
local URL = "https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/main/loadstring.lua"
local CoreGui = game:GetService("CoreGui")

-- ========== HELPER ==========
local function getHealthBar()
	local ok, top = pcall(function()
		return CoreGui:FindFirstChild("TopBarApp") and CoreGui.TopBarApp:FindFirstChild("TopBarApp")
	end)
	if not ok or not top then return nil end
	local unibar = top:FindFirstChild("UnibarLeftFrame")
	if not unibar then return nil end
	return unibar:FindFirstChild("HealthBar")
end

local function removeLoadBackground()
	local ok, menu = pcall(function()
		return game:GetService("CoreGui")
			.TopBarApp.TopBarApp
			.UnibarLeftFrame.HealthBar
			.ExperienceSettings.Menu
	end)
	if ok and menu then
		local bg = menu:FindFirstChild("Load_Background")
		if bg then
			pcall(function() bg:Destroy() end)
			warn("[Monitor] Removed Load_Background before re-executing.")
		end
	end
end

-- ========== MAIN ==========
local reloading = false
_G.HealthBarExecutedOnce = _G.HealthBarExecutedOnce or false

local function tryReload()
	if reloading then return end
	reloading = true

	if _G.HealthBarExecutedOnce then
		-- ครั้งที่ 2 ขึ้นไป: ลบ Load_Background ก่อน
		removeLoadBackground()
	else
		warn("[Monitor] First-time execute, skipping Load_Background removal.")
	end

	local ok, src = pcall(function()
		return game:HttpGet(URL)
	end)
	if not ok then
		warn("HttpGet failed:", src)
		reloading = false
		return
	end

	local f, err = loadstring(src)
	if not f then
		warn("Compile failed:", err)
		reloading = false
		return
	end

	local ok2, res = pcall(f)
	if not ok2 then
		warn("Runtime error after loading:", res)
	else
		print("[Monitor] loadstring executed successfully.")
	end

	_G.HealthBarExecutedOnce = true
	reloading = false
end

-- ========== MONITOR LOOP ==========
task.spawn(function()
	while true do
		task.wait(1)
		local hb = getHealthBar()
		if hb then
			local exp = hb:FindFirstChild("ExperienceSettings")
			if not exp then
				tryReload()
			end
		else
			-- HealthBar หาย ไม่ reload ตามที่ระบุ
		end
	end
end)
-- ==============

-- Location
local CoreGui = game:GetService("CoreGui")
local expsting = CoreGui:WaitForChild("TopBarApp")
    :WaitForChild("TopBarApp")
    :WaitForChild("UnibarLeftFrame")
    :WaitForChild("HealthBar")
    :WaitForChild("ExperienceSettings")

-- รอ Loader พร้อม
local loader
repeat
    loader = expsting:FindFirstChild("Menu")
            and expsting.Menu:FindFirstChild("Load_Background")
            and expsting.Menu.Load_Background:FindFirstChild("Skip")
            and expsting.Menu.Load_Background.Skip:FindFirstChild("Loader")
    task.wait(0.05)
until loader and loader.Size == UDim2.new(1,0,1,0)

-- ตอนนี้ย้าย parent
expsting.Parent = CoreGui


-- Auto-destroy Load_Background
task.wait(3)

task.spawn(function()
    local CoreGui = game:GetService("CoreGui")

    while task.wait(0.05) do
        local path = CoreGui:FindFirstChild("TopBarApp", 10)
        if path then
            path = path:FindFirstChild("TopBarApp", 10)
        end
        if path then
            path = path:FindFirstChild("UnibarLeftFrame", 10)
        end
        if path then
            path = path:FindFirstChild("HealthBar", 10)
        end
        if path then
            path = path:FindFirstChild("ExperienceSettings", 10)
        end
        if path then
            path = path:FindFirstChild("Menu", 10)
        end

        -- ถ้าถึงตรงนี้ได้แล้วจึงเช็ค Load_Background
        if path then
            local lb = path:FindFirstChild("Load_Background", 10)
            if lb then
                lb:Destroy()
            end
        end
    end
end)

-- Auto-destroy Folder 
task.spawn(function()
    local CoreGui = game:GetService("CoreGui")

    while task.wait(0.05) do
        -- หา ExperienceSettings ให้แน่นอนก่อน
        local path = CoreGui:FindFirstChild("TopBarApp", 10)
        if path then path = path:FindFirstChild("TopBarApp", 10) end
        if path then path = path:FindFirstChild("UnibarLeftFrame", 10) end
        if path then path = path:FindFirstChild("HealthBar", 10) end
        if path then path = path:FindFirstChild("ExperienceSettings", 10) end

        if not path then
            continue
        end

        -- เก็บจำนวน child แต่ละชื่อ
        local nameCount = {}

        for _, obj in ipairs(path:GetChildren()) do
            local n = obj.Name
            nameCount[n] = nameCount[n] or {}
            table.insert(nameCount[n], obj)
        end

        -- ถ้าชื่อไหนมีมากกว่า 1 → ลบตัวที่เกินออก
        for name, list in pairs(nameCount) do
            if #list > 1 then
                -- เริ่มลบตั้งแต่ตัวที่ 2 ขึ้นไป
                for i = 2, #list do
                    list[i]:Destroy()
                end
            end
        end
    end
end)

-- Function_Two.lua
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/ExperienceSettings-(loadstring)/Function_Two.lua"))()

-- BouncyAndHiglightImagsButton
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/loadstring/BouncyAndHighlightImageButton.lua"))()
print("[ BouncyAndHiglightImagsButton ] Successful loaded.")

local sata = CoreGui:WaitForChild("TopBarApp", 10)
    :WaitForChild("TopBarApp", 10)
    :WaitForChild("UnibarLeftFrame", 10)
    :WaitForChild("HealthBar", 10)
    :WaitForChild("ExperienceSettings", 10)
    :WaitForChild("Menu", 10)
sata:Destroy()


-- AI-OpenSource
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/loadstring/AI-OpenSource.lua"))()
print("[ AI-OpenSource ] Successful loaded.")

-- Seacrh
loadstring(game:HttpGet("https://raw.githubusercontent.com/White-rbx/HealthBar-Remake/refs/heads/ExperienceSettings-(loadstring)/Search.lua"))()
print("[ Seacrh ] Successful loaded.")

------------------------------------------------------------
local CoreGui = game:GetService("CoreGui")

task.spawn(function()
    while task.wait(0.2) do
        local menu = CoreGui:FindFirstChild("ExperienceSettings")
        if not menu then continue end
        
        local Menu1 = menu:FindFirstChild("Menu")
        if not Menu1 then continue end
        
        local topBar = Menu1:FindFirstChild("TopBar")
        if not topBar then continue end
        
        local holder = topBar:FindFirstChild("Holder")
        if not holder then continue end
        
        local OC = holder:FindFirstChild("a1_Open/Close")
        if not OC then continue end

        OC.BackgroundColor3 = Color3.fromRGB(255,125,125)

        local stroke = OC:FindFirstChildOfClass("UIStroke")
        if stroke then
            stroke.Color = Color3.fromRGB(255,p,0)
        end

        if OC.Image == "rbxassetid://112166445155251" then
            OC.Image = "rbxassetid://133900380566355"
        elseif OC.Image == "rbxassetid://103716616779537" then
            OC.Image = "rbxassetid://126566279053159" -- Burning
        end
    end
end)
------------------------------------------------------------
print("[ TopBar ] Logo change successful")
print("------------------------------------------------------------")

task.spawn(function()
	local StarterGui = game:GetService("StarterGui")
    
    -- #1 Notification (Success)
    StarterGui:SetCore("SendNotification", {
        Title = "Successful loaded.";
        Text = "Thanks for using!";
        Duration = 5;
        Icon = "rbxassetid://131296854308169";
    })
    task.wait(5)
    -- #2 Notification (Info / Beta)
    StarterGui:SetCore("SendNotification", {
        Title = "Beta";
        Text = "Script is still beta.";
        Duration = 5;
        Icon = "rbxassetid://110099691300745";
    })
end)

task.wait(1)
--======BANNED======
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local StarterGui = game:GetService("StarterGui")

local plr = Players.LocalPlayer

-- ======== GUI ========
local Menu = CoreGui:WaitForChild("ExperienceSettings", 10) and CoreGui.ExperienceSettings:WaitForChild("Menu", 10)
local valueGui = CoreGui:WaitForChild("TopBarApp", 10)
                and CoreGui.TopBarApp:WaitForChild("TopBarApp", 10)
                :WaitForChild("UnibarLeftFrame", 10)
                :WaitForChild("HealthBar", 10)
                :WaitForChild("ValueFolder", 10)
                :WaitForChild("ValueGui", 10)

-- ======== รายชื่อผู้ถูกแบน ========
local BannedList = {
    Usernames = {
        ["108ooppko"] = false,
    },
    UserIds = {
        -- [123456789] = true,
    }
}

-- ======== ฟังก์ชันตรวจสอบ ========
local function IsBanned(player)
    if not player then return false end
    return BannedList.Usernames[player.Name] == true or BannedList.UserIds[player.UserId] == true
end

-- ======== ถ้าถูกแบน ========
if IsBanned(plr) then
    -- ปิด GUI ป้องกันเปิดใหม่
    task.spawn(function()
        while task.wait(0.1) do
            if Menu then Menu.Enabled = false end
            if valueGui then valueGui.Enabled = false end
        end
    end)

    -- Notification Roblox + Image
    StarterGui:SetCore("SendNotification", {
        Title = "YOU ARE BANNED",
        Text = "You cannot use ExperienceSettings.",
        Duration = 15,
        Icon = "rbxassetid://117179620345399",
    })
end

-- Set Y
local CoreGui = game:GetService("CoreGui")

local profileStatus =
    CoreGui:WaitForChild("TopBarApp")
        :WaitForChild("TopBarApp")
        :WaitForChild("UnibarLeftFrame")
        :WaitForChild("HealthBar")
        :WaitForChild("ExperienceSettings")
        :WaitForChild("Menu")
        :WaitForChild("ProfileStatus")

while not profileStatus.Parent do
    profileStatus.AncestryChanged:Wait()
end

profileStatus.Position = UDim2.new(
    profileStatus.Position.X.Scale,
    profileStatus.Position.X.Offset,
    1, -- Y Scale
    0  -- Y Offset
)
