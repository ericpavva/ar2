local Config = {
    ESP = {Enabled=true,Boxes=true,Names=true,Health=true,Distance=true,Weapon=true,Skeleton=true,SkeletonColor={1,0,0,1},MaxDist=2000},
    Aimbot = {Enabled=false,Key=0x02,FOV=120,Speed=0.65,Bone="Head",VisibleOnly=true,ShowFov=true},
    Predict = {Enabled=true,UseGravity=true,GravityScale=0.55,MaxPredictTime=1.0,FallbackSpeed=1500},
    Debug = false,
}

local WEAPON_DB = {
    ["makarov"]={speed=2260,gravity=nil,name="Makarov Pistol"},
    ["makarov pistol"]={speed=2260,gravity=nil,name="Makarov Pistol"},
    ["model 459"]={speed=2490,gravity=nil,name="Model 459 Pistol"},
    ["model 459 pistol"]={speed=2490,gravity=nil,name="Model 459 Pistol"},
    ["m1911"]={speed=2040,gravity=nil,name="M1911 Pistol"},
    ["m1911 pistol"]={speed=2040,gravity=nil,name="M1911 Pistol"},
    ["m9"]={speed=2600,gravity=nil,name="M9 Pistol"},
    ["m9 pistol"]={speed=2600,gravity=nil,name="M9 Pistol"},
    ["g17"]={speed=2475,gravity=nil,name="G17 Pistol"},
    ["g17 pistol"]={speed=2475,gravity=nil,name="G17 Pistol"},
    ["snubnose"]={speed=1815,gravity=nil,name="Snubnose Revolver"},
    ["snubnose revolver"]={speed=1815,gravity=nil,name="Snubnose Revolver"},
    ["model 44"]={speed=3380,gravity=nil,name="Model 44 Carbine"},
    ["model 44 carbine"]={speed=3380,gravity=nil,name="Model 44 Carbine"},
    ["camp carbine"]={speed=2850,gravity=nil,name="Camp Carbine"},
    ["m1 carbine"]={speed=3475,gravity=nil,name="M1 Carbine"},
    ["m2 carbine"]={speed=3475,gravity=nil,name="M2 Carbine"},
    ["m4a1"]={speed=4415,gravity=nil,name="M4A1 Carbine"},
    ["m4a1 carbine"]={speed=4415,gravity=nil,name="M4A1 Carbine"},
    ["mosin m44 carbine"]={speed=3960,gravity=nil,name="Mosin-Nagant M44 Carbine"},
    ["mosin-nagant m44"]={speed=3960,gravity=nil,name="Mosin-Nagant M44 Carbine"},
    ["mosin obrez"]={speed=3280,gravity=nil,name="Obrez Mosin-Nagant"},
    ["obrez mosin-nagant"]={speed=3280,gravity=nil,name="Obrez Mosin-Nagant"},
    ["sks"]={speed=3960,gravity=nil,name="SKS Assault Carbine"},
    ["sks assault carbine"]={speed=3960,gravity=nil,name="SKS Assault Carbine"},
    ["xm177"]={speed=4415,gravity=nil,name="XM177 Assault Carbine"},
    ["xm177 assault carbine"]={speed=4415,gravity=nil,name="XM177 Assault Carbine"},
    ["aks-74u"]={speed=4000,gravity=nil,name="AKS-74U Assault Carbine"},
    ["aks-74u assault carbine"]={speed=4000,gravity=nil,name="AKS-74U Assault Carbine"},
    ["aks74u"]={speed=4000,gravity=nil,name="AKS-74U Assault Carbine"},
    ["patriot"]={speed=4075,gravity=nil,name="Patriot Assault Carbine"},
    ["patriot assault carbine"]={speed=4075,gravity=nil,name="Patriot Assault Carbine"},
    ["ak-47"]={speed=3800,gravity=nil,name="AK-47 Assault Rifle"},
    ["ak-47 assault rifle"]={speed=3800,gravity=nil,name="AK-47 Assault Rifle"},
    ["ak47"]={speed=3800,gravity=nil,name="AK-47 Assault Rifle"},
    ["ak-47 draco"]={speed=3440,gravity=nil,name="Stunted AK-47"},
    ["stunted ak-47"]={speed=3440,gravity=nil,name="Stunted AK-47"},
    ["m16a2"]={speed=4650,gravity=nil,name="M16A2 Assault Rifle"},
    ["m16a2 assault rifle"]={speed=4650,gravity=nil,name="M16A2 Assault Rifle"},
    ["m16a1"]={speed=4625,gravity=nil,name="M16A1 Assault Rifle"},
    ["m16a1 assault rifle"]={speed=4625,gravity=nil,name="M16A1 Assault Rifle"},
    ["ak-74"]={speed=4610,gravity=nil,name="AK-74 Assault Rifle"},
    ["ak-74 assault rifle"]={speed=4610,gravity=nil,name="AK-74 Assault Rifle"},
    ["as val"]={speed=2090,gravity=nil,name="AS Val Assault Rifle"},
    ["as-val"]={speed=2090,gravity=nil,name="AS Val Assault Rifle"},
    ["aug"]={speed=4670,gravity=nil,name="AUG Assault Rifle"},
    ["aug assault rifle"]={speed=4670,gravity=nil,name="AUG Assault Rifle"},
    ["famas"]={speed=4625,gravity=nil,name="FA-MAS Assault Rifle"},
    ["fa-mas"]={speed=4625,gravity=nil,name="FA-MAS Assault Rifle"},
    ["l85a1"]={speed=4700,gravity=nil,name="L85A1 Assault Rifle"},
    ["l85a1 assault rifle"]={speed=4700,gravity=nil,name="L85A1 Assault Rifle"},
    ["ac-556"]={speed=4630,gravity=nil,name="AC-556 Assault Rifle"},
    ["ac-556 assault rifle"]={speed=4630,gravity=nil,name="AC-556 Assault Rifle"},
    ["ac556"]={speed=4630,gravity=nil,name="AC-556 Assault Rifle"},
    ["m1 garand"]={speed=4370,gravity=nil,name="M1 Garand Battle Rifle"},
    ["m1 garand battle rifle"]={speed=4370,gravity=nil,name="M1 Garand Battle Rifle"},
    ["g3"]={speed=4270,gravity=nil,name="G3 Battle Rifle"},
    ["g3 battle rifle"]={speed=4270,gravity=nil,name="G3 Battle Rifle"},
    ["fal"]={speed=4370,gravity=nil,name="FAL Battle Rifle"},
    ["fal battle rifle"]={speed=4370,gravity=nil,name="FAL Battle Rifle"},
    ["m14"]={speed=4370,gravity=nil,name="M14 Battle Rifle"},
    ["m14 battle rifle"]={speed=4370,gravity=nil,name="M14 Battle Rifle"},
    ["svt-40"]={speed=4270,gravity=nil,name="SVT-40 Battle Rifle"},
    ["svt-40 battle rifle"]={speed=4270,gravity=nil,name="SVT-40 Battle Rifle"},
    ["svt40"]={speed=4270,gravity=nil,name="SVT-40 Battle Rifle"},
    ["msg-90"]={speed=4425,gravity=nil,name="MSG-90 Marksman Rifle"},
    ["msg-90 marksman rifle"]={speed=4425,gravity=nil,name="MSG-90 Marksman Rifle"},
    ["remington 788"]={speed=3600,gravity=nil,name="Model 788 Hunting Rifle"},
    ["model 788"]={speed=3600,gravity=nil,name="Model 788 Hunting Rifle"},
    ["model 788 hunting rifle"]={speed=3600,gravity=nil,name="Model 788 Hunting Rifle"},
    ["mosin pu"]={speed=4400,gravity=nil,name="Mosin-Nagant PU Sniper Rifle"},
    ["mosin-nagant pu"]={speed=4400,gravity=nil,name="Mosin-Nagant PU Sniper Rifle"},
    ["mosin nagant pu"]={speed=4400,gravity=nil,name="Mosin-Nagant PU Sniper Rifle"},
    ["mosin-nagant pu sniper rifle"]={speed=4400,gravity=nil,name="Mosin-Nagant PU Sniper Rifle"},
    ["psg-1"]={speed=4450,gravity=nil,name="PSG-1 Marksman Rifle"},
    ["psg-1 marksman rifle"]={speed=4450,gravity=nil,name="PSG-1 Marksman Rifle"},
    ["psg1"]={speed=4450,gravity=nil,name="PSG-1 Marksman Rifle"},
    ["dragunov"]={speed=4400,gravity=nil,name="Dragunov Marksman Rifle"},
    ["dragunov svd"]={speed=4400,gravity=nil,name="Dragunov Marksman Rifle"},
    ["dragunov marksman rifle"]={speed=4400,gravity=nil,name="Dragunov Marksman Rifle"},
    ["vss vintorez"]={speed=2275,gravity=nil,name="VSS Vintorez Marksman Rifle"},
    ["vss-vintorez"]={speed=2275,gravity=nil,name="VSS Vintorez Marksman Rifle"},
    ["mini-14"]={speed=4670,gravity=nil,name="Mini-14 Rifle"},
    ["mini-14 rifle"]={speed=4670,gravity=nil,name="Mini-14 Rifle"},
    ["mini 14"]={speed=4670,gravity=nil,name="Mini-14 Rifle"},
    ["m40a1"]={speed=4575,gravity=nil,name="M40A1 Sniper Rifle"},
    ["m40a1 sniper rifle"]={speed=4575,gravity=nil,name="M40A1 Sniper Rifle"},
    ["maverick 88"]={speed=2600,gravity=nil,name="Maverick 88 Shotgun"},
    ["maverick 88 shotgun"]={speed=2600,gravity=nil,name="Maverick 88 Shotgun"},
    ["model 590"]={speed=2600,gravity=nil,name="Model 590 Shotgun"},
    ["model 590 shotgun"]={speed=2600,gravity=nil,name="Model 590 Shotgun"},
    ["mat-49"]={speed=2740,gravity=nil,name="MAT-49 SMG"},
    ["mat-49 smg"]={speed=2740,gravity=nil,name="MAT-49 SMG"},
    ["mat49"]={speed=2740,gravity=nil,name="MAT-49 SMG"},
    ["m1 thompson"]={speed=2215,gravity=nil,name="M1 Thompson SMG"},
    ["m1 thompson smg"]={speed=2215,gravity=nil,name="M1 Thompson SMG"},
    ["mp 40"]={speed=2765,gravity=nil,name="MP 40 SMG"},
    ["mp40"]={speed=2765,gravity=nil,name="MP 40 SMG"},
    ["m3a1"]={speed=2140,gravity=nil,name="M3A1 SMG"},
    ["m3a1 smg"]={speed=2140,gravity=nil,name="M3A1 SMG"},
    ["m1918a2 bar"]={speed=4380,gravity=nil,name="M1918A2 BAR"},
    ["m1918a2"]={speed=4380,gravity=nil,name="M1918A2 BAR"},
    ["bar"]={speed=4380,gravity=nil,name="M1918A2 BAR"},
    ["m249"]={speed=4575,gravity=nil,name="M249 SAW"},
    ["m249 saw"]={speed=4575,gravity=nil,name="M249 SAW"},
    ["m249 paratrooper"]={speed=4415,gravity=nil,name="M249 Paratrooper LMG"},
    ["m1919a6"]={speed=4360,gravity=nil,name="M1919A6 LMG"},
    ["m1919a6mod1"]={speed=4350,gravity=nil,name="Trooper M1919A6 LMG"},
    ["trooper m1919a6"]={speed=4350,gravity=nil,name="Trooper M1919A6 LMG"},
    ["rpk-74m"]={speed=4700,gravity=nil,name="RPK-74M LMG"},
    ["rpk74m"]={speed=4700,gravity=nil,name="RPK-74M LMG"},
}

local function extractItemName(json)
    if not json or json == "" then return nil end
    local name = string.match(json, '"ItemName"%s*:%s*"([^"]*)"')
    if name and name ~= "" then return name end
    return nil
end

local function getFriendlyWeaponName(itemName)
    if not itemName then return nil end
    local key = string.lower(itemName)
    local entry = WEAPON_DB[key]
    if entry then return entry.name end
    for dbKey, dbEntry in pairs(WEAPON_DB) do
        if string.find(key, dbKey, 1, true) or string.find(dbKey, key, 1, true) then
            return dbEntry.name
        end
    end
    return itemName
end

local function getEnemyWeapon(player)
    local char = player.Character
    if not char then return nil end
    local animator = char:FindFirstChild("Animator")
    if not animator then return nil end
    local eq = animator:FindFirstChild("EquippedItem")
    if not eq then return nil end
    return extractItemName(eq.Value)
end

local function getWeaponInfo(player)
    local itemName = getEnemyWeapon(player)
    if not itemName then return Config.Predict.FallbackSpeed, nil, "fallback", nil end
    local key = string.lower(itemName)
    local entry = WEAPON_DB[key]
    if entry then return entry.speed, entry.gravity, "db:"..key, itemName end
    local bestKey, bestEntry, bestLen = nil, nil, 0
    for dbKey, dbEntry in pairs(WEAPON_DB) do
        if string.find(key, dbKey, 1, true) or string.find(dbKey, key, 1, true) then
            if #dbKey > bestLen then
                bestKey, bestEntry, bestLen = dbKey, dbEntry, #dbKey
            end
        end
    end
    if bestEntry then return bestEntry.speed, bestEntry.gravity, "db~"..bestKey, itemName end
    return Config.Predict.FallbackSpeed, nil, "unknown:"..itemName, itemName
end

-- ============================================
-- LUA TABLE PARSER
-- ============================================
local function parseLuaTable(s)
    local pos = 1
    local function skipWS()
        while pos <= #s do
            local c = s:sub(pos,pos)
            if c==" " or c=="\t" or c=="\n" or c=="\r" then pos=pos+1 else break end
        end
    end
    local parseValue
    local function parseString()
        local quote = s:sub(pos,pos)
        pos = pos + 1
        local buf = {}
        while pos <= #s do
            local ch = s:sub(pos,pos)
            if ch == "\\" then
                local nxt = s:sub(pos+1,pos+1)
                if nxt=="n" then buf[#buf+1]="\n"
                elseif nxt=="t" then buf[#buf+1]="\t"
                elseif nxt=="r" then buf[#buf+1]="\r"
                elseif nxt=='"' then buf[#buf+1]='"'
                elseif nxt=="'" then buf[#buf+1]="'"
                elseif nxt=="\\" then buf[#buf+1]="\\"
                else buf[#buf+1]=nxt end
                pos = pos + 2
            elseif ch == quote then
                pos = pos + 1
                return table.concat(buf)
            else
                buf[#buf+1] = ch
                pos = pos + 1
            end
        end
        return table.concat(buf)
    end
    parseValue = function()
        skipWS()
        if pos > #s then return nil end
        local c = s:sub(pos,pos)
        if c=='"' or c=="'" then return parseString()
        elseif c=="{" then
            pos = pos + 1
            local t = {}
            local arrayIndex = 1
            skipWS()
            if s:sub(pos,pos)=="}" then pos=pos+1 return t end
            while pos <= #s do
                skipWS()
                if s:sub(pos,pos)=="}" then pos=pos+1 return t end
                local savePos = pos
                local key
                if s:sub(pos,pos)=='"' or s:sub(pos,pos)=="'" then
                    key = parseString()
                else
                    local idStart = pos
                    while pos <= #s and s:sub(pos,pos):match("[%w_%-%.]") do pos=pos+1 end
                    local id = s:sub(idStart,pos-1)
                    if id ~= "" then key = id end
                end
                skipWS()
                if key and s:sub(pos,pos)=="=" then
                    pos = pos + 1
                    local val = parseValue()
                    local numKey = tonumber(key)
                    if numKey then t[numKey]=val else t[key]=val end
                else
                    pos = savePos
                    local val = parseValue()
                    if val ~= nil then t[arrayIndex]=val arrayIndex=arrayIndex+1 end
                end
                skipWS()
                if s:sub(pos,pos)=="," then pos=pos+1 end
            end
            return t
        elseif s:sub(pos,pos+3)=="true" then pos=pos+4 return true
        elseif s:sub(pos,pos+4)=="false" then pos=pos+5 return false
        elseif s:sub(pos,pos+2)=="nil" then pos=pos+3 return nil
        else
            local numStart = pos
            while pos <= #s and s:sub(pos,pos):match("[%d%.%-+eE]") do pos=pos+1 end
            return tonumber(s:sub(numStart,pos-1))
        end
    end
    local eqIdx = s:find("=", 1, true)
    if not eqIdx then return nil end
    pos = eqIdx + 1
    skipWS()
    if s:sub(pos,pos) ~= "{" then return nil end
    return parseValue()
end

-- ============================================
-- CONFIG SERIALIZE
-- ============================================
local function serializeValue(v)
    local t = type(v)
    if t == "boolean" then return tostring(v)
    elseif t == "number" then
        if v == math.floor(v) and math.abs(v) < 1e15 then return string.format("%d",v) end
        return string.format("%.6f", v)
    elseif t == "string" then return string.format("%q", v)
    elseif t == "table" then
        local parts = {}
        local isArray = true
        local maxN = 0
        for k,_ in pairs(v) do
            if type(k) ~= "number" then isArray=false break end
            if k > maxN then maxN = k end
        end
        for i=1,maxN do if v[i]==nil then isArray=false break end end
        if isArray and maxN > 0 then
            for i=1,maxN do parts[#parts+1] = serializeValue(v[i]) end
            return "{"..table.concat(parts,",").."}"
        else
            for k,val in pairs(v) do
                local key = type(k)=="string" and string.format("%q",k) or tostring(k)
                parts[#parts+1] = key.."="..serializeValue(val)
            end
            return "{"..table.concat(parts,",").."}"
        end
    end
    return "nil"
end

local function serializeConfig()
    return "Config = " .. serializeValue(Config)
end

-- ============================================
-- CONFIG FILE I/O
-- ============================================
local CONFIG_FILENAME = "vector_config.txt"

local function getConfigPaths()
    local paths = {}
    pcall(function()
        local appdata = os.getenv("LOCALAPPDATA")
        if appdata then
            paths[#paths+1] = appdata .. "\\" .. CONFIG_FILENAME
            paths[#paths+1] = appdata .. "\\Project Vector\\" .. CONFIG_FILENAME
        end
        local up = os.getenv("USERPROFILE")
        if up then
            paths[#paths+1] = up .. "\\" .. CONFIG_FILENAME
            paths[#paths+1] = up .. "\\Desktop\\" .. CONFIG_FILENAME
        end
    end)
    paths[#paths+1] = CONFIG_FILENAME
    paths[#paths+1] = ".\\" .. CONFIG_FILENAME
    return paths
end

local function saveConfigToFile()
    local content = serializeConfig()
    local paths = getConfigPaths()
    for _, path in ipairs(paths) do
        local ok = pcall(function()
            local file = io.open(path, "w")
            if not file then return false end
            file:write(content)
            file:close()
            return true
        end)
        if ok then
            print("[CONFIG] Saved to: " .. path)
            return true, path
        end
    end
    print("[CONFIG] Failed to save")
    return false
end

local function applyConfigFromString(code)
    if not code or code == "" then return false end
    local eqIdx = code:find("=", 1, true)
    if not eqIdx then return false end
    local tablePart = code:sub(eqIdx+1):gsub("^%s+","")
    local parsed
    local ok = pcall(function()
        parsed = parseLuaTable("Config=" .. tablePart)
    end)
    if not ok or not parsed then return false end
    local function merge(dst, src)
        for k,v in pairs(src) do
            if type(v)=="table" and type(dst[k])=="table" then merge(dst[k],v)
            else dst[k]=v end
        end
    end
    merge(Config, parsed)
    menu.Set("esp_enabled",  Config.ESP.Enabled)
    menu.Set("esp_box",      Config.ESP.Boxes)
    menu.Set("esp_name",     Config.ESP.Names)
    menu.Set("esp_health",   Config.ESP.Health)
    menu.Set("esp_dist",     Config.ESP.Distance)
    menu.Set("esp_weapon",   Config.ESP.Weapon)
    menu.Set("esp_skel",     Config.ESP.Skeleton)
    menu.Set("esp_maxdist",  Config.ESP.MaxDist)
    menu.Set("aim_enabled",  Config.Aimbot.Enabled)
    menu.Set("aim_visible",  Config.Aimbot.VisibleOnly)
    menu.Set("aim_fov_draw", Config.Aimbot.ShowFov)
    menu.Set("aim_fov",      Config.Aimbot.FOV)
    menu.Set("aim_speed",    Config.Aimbot.Speed)
    menu.Set("pred_enabled", Config.Predict.Enabled)
    menu.Set("pred_gravity", Config.Predict.UseGravity)
    menu.Set("pred_gscale",  Config.Predict.GravityScale)
    menu.Set("pred_maxt",    Config.Predict.MaxPredictTime)
    menu.Set("pred_fallback",Config.Predict.FallbackSpeed)
    menu.Set("pred_debug",   Config.Debug)
    return true
end

local function loadConfigFromFile()
    local paths = getConfigPaths()
    for _, path in ipairs(paths) do
        local ok, content = pcall(function()
            local file = io.open(path, "r")
            if not file then return nil end
            local data = file:read("*a")
            file:close()
            return data
        end)
        if ok and content and #content > 0 then
            if applyConfigFromString(content) then
                print("[CONFIG] Loaded from: " .. path)
                return true, path
            end
        end
    end
    return false
end

local function printConfigToConsole()
    print(serializeConfig())
end

-- ============================================
-- HELPERS
-- ============================================
local function isEnemy(p)
    if not p or p.IsLocal or not p.IsAlive then return false end
    return true
end

local function getTargets()
    local list = {}
    for _,p in ipairs(entity.GetPlayers()) do
        if isEnemy(p) then
            local dist = p:DistanceTo()
            if dist <= Config.ESP.MaxDist then
                list[#list+1] = {player=p, dist=dist}
            end
        end
    end
    table.sort(list, function(a,b) return a.dist < b.dist end)
    return list
end

local function predictPosition(origin, targetPos, targetVel, speed, gravity)
    if not Config.Predict.Enabled or not speed or speed <= 0 then return targetPos end
    local g = 0
    if Config.Predict.UseGravity then
        if gravity ~= nil then g = gravity * Config.Predict.GravityScale
        else g = workspace.GetGravity() * Config.Predict.GravityScale end
    end
    local D = targetPos - origin
    local Vt = targetVel
    local a = Vt:Dot(Vt) - speed*speed
    local b = 2 * D:Dot(Vt)
    local c = D:Dot(D)
    local t
    if math.abs(a) < 1e-6 then
        if math.abs(b) < 1e-6 then t = 0 else t = -c/b end
    else
        local disc = b*b - 4*a*c
        if disc < 0 then return targetPos end
        local sqrtD = math.sqrt(disc)
        local t1 = (-b - sqrtD) / (2*a)
        local t2 = (-b + sqrtD) / (2*a)
        if t1 > 0 and t2 > 0 then t = math.min(t1,t2)
        elseif t1 > 0 then t = t1
        elseif t2 > 0 then t = t2
        else return targetPos end
    end
    if t < 0 then t = 0 end
    if t > Config.Predict.MaxPredictTime then t = Config.Predict.MaxPredictTime end
    local predicted = targetPos + Vt * t
    if g > 0 then predicted = predicted + Vector3.New(0, 0.5*g*t*t, 0) end
    return predicted
end

local function syncAimbotKey()
    local k = menu.GetKey("aim_key")
    if k and k ~= 0 then Config.Aimbot.Key = k end
end

local SKELETON_CONNECTIONS = {
    {"Head","UpperTorso"},{"UpperTorso","LowerTorso"},
    {"UpperTorso","LeftUpperArm"},{"UpperTorso","RightUpperArm"},
    {"LeftUpperArm","LeftLowerArm"},{"RightUpperArm","RightLowerArm"},
    {"LeftLowerArm","LeftHand"},{"RightLowerArm","RightHand"},
    {"LowerTorso","LeftUpperLeg"},{"LowerTorso","RightUpperLeg"},
    {"LeftUpperLeg","LeftLowerLeg"},{"RightUpperLeg","RightLowerLeg"},
    {"LeftLowerLeg","LeftFoot"},{"RightLowerLeg","RightFoot"},
}

local function drawSkeleton(player)
    if not Config.ESP.Skeleton then return end
    local bones = player:GetBonesScreen()
    if not bones then return end
    local col = Config.ESP.SkeletonColor or {1,0,0,1}
    for _,conn in ipairs(SKELETON_CONNECTIONS) do
        local a = bones[conn[1]]
        local b = bones[conn[2]]
        if a and b then draw.Line(a[1],a[2],b[1],b[2],col,1.5) end
    end
end

local function drawESP()
    if not Config.ESP.Enabled then return end
    for _,entry in ipairs(getTargets()) do
        local p = entry.player
        drawSkeleton(p)
        local bounds = p:GetBounds()
        if bounds.valid then
            if Config.ESP.Boxes then
                draw.CornerBox(bounds.x,bounds.y,bounds.w,bounds.h,{1,1,1,1})
            end
            if Config.ESP.Names or Config.ESP.Weapon then
                local topY = bounds.y - 4
                if Config.ESP.Names then
                    local tw,th = draw.GetTextSize(p.Name,14)
                    draw.Text(bounds.x+bounds.w*0.5-tw*0.5, topY-th, p.Name, {1,1,1,1}, 14)
                    topY = topY - th - 2
                end
                if Config.ESP.Weapon then
                    local rawName = getEnemyWeapon(p)
                    local friendly = getFriendlyWeaponName(rawName)
                    if friendly then
                        local col = {0.7,0.7,0.7,1}
                        local lower = string.lower(friendly)
                        if string.find(lower,"sniper") or string.find(lower,"marksman")
                           or string.find(lower,"dragunov") or string.find(lower,"psg")
                           or string.find(lower,"m40") or string.find(lower,"mosin") then
                            col = {1,0.6,0.2,1}
                        end
                        local tw2,th2 = draw.GetTextSize(friendly,12)
                        draw.Text(bounds.x+bounds.w*0.5-tw2*0.5, topY-th2, friendly, col, 12)
                    end
                end
            end
            if Config.ESP.Health then
                draw.HealthBar(bounds.x-6,bounds.y,bounds.h,p.Health,p.MaxHealth)
            end
            if Config.ESP.Distance then
                draw.Text(bounds.x+bounds.w+4, bounds.y+bounds.h*0.5,
                    string.format("%dm",math.floor(entry.dist)), {0.8,0.8,0.8,1}, 12)
            end
        end
    end
end

local function drawFovCircle()
    if not Config.Aimbot.Enabled or not Config.Aimbot.ShowFov then return end
    local sw,sh = draw.GetScreenSize()
    draw.Circle(sw*0.5, sh*0.5, Config.Aimbot.FOV, {1,1,1,0.35}, 64, 1.0)
end

local function getClosestToCrosshair()
    local sw,sh = draw.GetScreenSize()
    local cx,cy = sw*0.5, sh*0.5
    local best, bestDist = nil, Config.Aimbot.FOV
    for _,entry in ipairs(getTargets()) do
        local p = entry.player
        local x,y,vis = p:GetBoneScreen(Config.Aimbot.Bone)
        if vis then
            local dx,dy = x-cx, y-cy
            local d = math.sqrt(dx*dx+dy*dy)
            if d < bestDist then
                if not Config.Aimbot.VisibleOnly or raycast.IsPlayerVisible(p.Character) then
                    best, bestDist = p, d
                end
            end
        end
    end
    return best
end

local function aimbotFrame()
    if not Config.Aimbot.Enabled then return end
    if not input.IsKeyDown(Config.Aimbot.Key) then return end
    local target = getClosestToCrosshair()
    if not target then return end
    local lp = entity.GetLocalPlayer()
    local speed, gravity = getWeaponInfo(lp)
    local origin = camera.GetPosition()
    local targetVel = target.Velocity or Vector3.New(0,0,0)
    local headPos = target.HeadPosition
    if not headPos then return end
    local predicted = predictPosition(origin, headPos, targetVel, speed, gravity)
    local sx,sy,onScreen = draw.WorldToScreen(predicted.X, predicted.Y, predicted.Z)
    if not onScreen then return end
    local sw,sh = draw.GetScreenSize()
    local cx,cy = sw*0.5, sh*0.5
    local speedFactor = 1.0 - Config.Aimbot.Speed
    input.MoveMouse((sx-cx)*speedFactor, (sy-cy)*speedFactor)
end

local function drawDebug()
    if not Config.Debug then return end
    local lp = entity.GetLocalPlayer()
    local speed, gravity, source, weaponName = getWeaponInfo(lp)
    draw.Window(10,10,"debug","Weapon Info",{
        "Weapon: " .. tostring(weaponName or "none"),
        "Speed: " .. tostring(math.floor(speed)) .. " (" .. tostring(source) .. ")",
        "Gravity: " .. tostring(gravity or workspace.GetGravity()) .. " x " .. tostring(Config.Predict.GravityScale),
        "FPS: " .. string.format("%.0f", utility.GetFPS()),
    })
end

-- ============================================
-- MENU
-- ============================================
menu.AddTab("Vector", "V")

menu.AddGroup("Vector", "ESP")
menu.AddCheckbox("Vector", "ESP", "esp_enabled", "Enable ESP", true)
menu.AddCheckbox("Vector", "ESP", "esp_box",     "Boxes",      true)
menu.AddCheckbox("Vector", "ESP", "esp_name",    "Names",      true)
menu.AddCheckbox("Vector", "ESP", "esp_health",  "Health",     true)
menu.AddCheckbox("Vector", "ESP", "esp_dist",    "Distance",   true)
menu.AddCheckbox("Vector", "ESP", "esp_weapon",  "Weapon",     true)
menu.AddCheckbox("Vector", "ESP", "esp_skel",    "Skeleton",   true)
menu.AddSliderInt("Vector", "ESP", "esp_maxdist", "Max dist", 50, 5000, 2000, "%d")

menu.AddGroup("Vector", "Aimbot")
menu.AddCheckbox("Vector", "Aimbot", "aim_enabled",  "Enable Aimbot", false)
menu.AddCheckbox("Vector", "Aimbot", "aim_visible",  "Visible only",  true)
menu.AddCheckbox("Vector", "Aimbot", "aim_fov_draw", "Show FOV",      true)
menu.AddHotkey  ("Vector", "Aimbot", "aim_key",      "Key", 0x02)
menu.AddSliderInt("Vector", "Aimbot", "aim_fov",     "FOV", 10, 500, 120, "%d°")
menu.AddSliderFloat("Vector", "Aimbot", "aim_speed", "Speed (0=instant)", 0.0, 0.99, 0.65, "%.2f")
menu.AddCombo   ("Vector", "Aimbot", "aim_bone", "Bone", {"Head","UpperTorso","HumanoidRootPart"}, 0)

menu.AddGroup("Vector", "Prediction")
menu.AddCheckbox("Vector", "Prediction", "pred_enabled", "Enable Predict", true)
menu.AddCheckbox("Vector", "Prediction", "pred_gravity", "Compensate Gravity", true)
menu.AddCheckbox("Vector", "Prediction", "pred_debug",   "Show Debug Window", false)
menu.AddSliderFloat("Vector", "Prediction", "pred_gscale", "Gravity scale", 0.0, 2.0, 0.55, "%.2f")
menu.AddSliderFloat("Vector", "Prediction", "pred_maxt",  "Max flight time", 0.05, 2.0, 1.0, "%.2fs")
menu.AddSliderInt("Vector", "Prediction", "pred_fallback", "Fallback speed", 100, 5000, 1500, "%d")

menu.AddGroup("Vector", "Config")
menu.AddButton("Vector", "Config", "config_save", "Save Config", function()
    local ok, path = saveConfigToFile()
    if ok then notify.Success("Config", "saved", 2)
    else notify.Warning("Config", "save failed", 2) end
end)
menu.AddButton("Vector", "Config", "config_load", "Load Config", function()
    local ok = loadConfigFromFile()
    if ok then notify.Success("Config", "loaded", 2)
    else notify.Warning("Config", "no file found", 2) end
end)

-- Callbacks
menu.SetCallback("esp_enabled", function(v) Config.ESP.Enabled = v end)
menu.SetCallback("esp_box",     function(v) Config.ESP.Boxes = v end)
menu.SetCallback("esp_name",    function(v) Config.ESP.Names = v end)
menu.SetCallback("esp_health",  function(v) Config.ESP.Health = v end)
menu.SetCallback("esp_dist",    function(v) Config.ESP.Distance = v end)
menu.SetCallback("esp_weapon",  function(v) Config.ESP.Weapon = v end)
menu.SetCallback("esp_skel",    function(v) Config.ESP.Skeleton = v end)
menu.SetCallback("esp_maxdist", function(v) Config.ESP.MaxDist = v end)

menu.SetCallback("aim_enabled",  function(v) Config.Aimbot.Enabled = v end)
menu.SetCallback("aim_visible",  function(v) Config.Aimbot.VisibleOnly = v end)
menu.SetCallback("aim_fov_draw", function(v) Config.Aimbot.ShowFov = v end)
menu.SetCallback("aim_fov",      function(v) Config.Aimbot.FOV = v end)
menu.SetCallback("aim_speed",    function(v) Config.Aimbot.Speed = v end)
menu.SetCallback("aim_bone",     function(idx)
    local bones = {"Head","UpperTorso","HumanoidRootPart"}
    Config.Aimbot.Bone = bones[idx+1] or "Head"
end)

menu.SetCallback("pred_enabled", function(v) Config.Predict.Enabled = v end)
menu.SetCallback("pred_gravity", function(v) Config.Predict.UseGravity = v end)
menu.SetCallback("pred_debug",   function(v) Config.Debug = v end)
menu.SetCallback("pred_gscale",  function(v) Config.Predict.GravityScale = v end)
menu.SetCallback("pred_maxt",    function(v) Config.Predict.MaxPredictTime = v end)
menu.SetCallback("pred_fallback",function(v) Config.Predict.FallbackSpeed = v end)

-- ============================================
-- STARTUP: auto-load config
-- ============================================
pcall(loadConfigFromFile)

-- ============================================
-- MAIN LOOP
-- ============================================
OnFrame = function()
    syncAimbotKey()
    drawESP()
    drawFovCircle()
    drawDebug()
    aimbotFrame()
end
