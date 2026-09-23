local Config = {
    ESP = {
        Enabled       = true,
        Boxes         = true,
        Names         = true,
        Health        = true,
        Distance      = true,
        Weapon        = true,
        Skeleton      = true,
        SkeletonColor = {1, 0, 0, 1},
        MaxDist       = 2000,
        Corpse        = true,
        CorpseBoxes   = true,
        CorpseNames   = true,
        CorpseDist    = true,
        CorpseMaxDist = 500,
        CorpseColor   = {0.6, 0.2, 0.8, 1},
    },
    Aimbot = {
        Enabled     = false,
        Key         = 0x02,
        FOV         = 120,
        Speed       = 0.65,
        Bone        = "Head",
        VisibleOnly = false,
        ShowFov     = true,
        Lock        = true,
    },
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
    ["desert eagle"]={speed=2550,gravity=nil,name="Desert Eagle"},
    ["sweeper desert eagle"]={speed=2390,gravity=nil,name="Sweeper Desert Eagle"},
    ["desert eaglemode1"]={speed=2390,gravity=nil,name="Sweeper Desert Eagle"},
    ["hi-power"]={speed=2520,gravity=nil,name="Hi-Power Pistol"},
    ["hipower"]={speed=2520,gravity=nil,name="Hi-Power Pistol"},
    ["mk23 socom"]={speed=2200,gravity=nil,name="SOCOM MK23"},
    ["socom mk23"]={speed=2200,gravity=nil,name="SOCOM MK23"},
    ["p220 sig"]={speed=2100,gravity=nil,name="P220 Pistol"},
    ["p220"]={speed=2100,gravity=nil,name="P220 Pistol"},
    ["mac-10"]={speed=2000,gravity=nil,name="MAC-10"},
    ["mac10"]={speed=2000,gravity=nil,name="MAC-10"},
    ["mac-10mod1"]={speed=2000,gravity=nil,name="Snake's MAC-10"},
    ["snake's mac-10"]={speed=2000,gravity=nil,name="Snake's MAC-10"},
    ["tec-9"]={speed=2500,gravity=nil,name="TEC-9"},
    ["tec9"]={speed=2500,gravity=nil,name="TEC-9"},
    ["m93r"]={speed=2600,gravity=nil,name="M93R Burst Machine Pistol"},
    ["m93r burst"]={speed=2600,gravity=nil,name="M93R Burst Machine Pistol"},
    ["skorpion vz.65"]={speed=2290,gravity=nil,name="Skorpion vz.65"},
    ["skorpion vz65"]={speed=2290,gravity=nil,name="Skorpion vz.65"},
    ["skorpion"]={speed=2290,gravity=nil,name="Skorpion vz.65"},
    ["makarovmod1"]={speed=2260,gravity=nil,name="Avtomat Makarov"},
    ["avtomat makarov"]={speed=2260,gravity=nil,name="Avtomat Makarov"},
    ["snubnose"]={speed=1815,gravity=nil,name="Snubnose Revolver"},
    ["snubnose revolver"]={speed=1815,gravity=nil,name="Snubnose Revolver"},
    ["model 29"]={speed=2475,gravity=nil,name="Model 29 Revolver"},
    ["python"]={speed=2735,gravity=nil,name="Python Revolver"},
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
    ["ots-14 groza"]={speed=1975,gravity=nil,name="OTs-14 Groza"},
    ["ots-14"]={speed=1975,gravity=nil,name="OTs-14 Groza"},
    ["ak-47"]={speed=3800,gravity=nil,name="AK-47 Assault Rifle"},
    ["ak-47 assault rifle"]={speed=3800,gravity=nil,name="AK-47 Assault Rifle"},
    ["ak47"]={speed=3800,gravity=nil,name="AK-47 Assault Rifle"},
    ["ak-47 draco"]={speed=3440,gravity=nil,name="Stunted AK-47"},
    ["stunted ak-47"]={speed=3440,gravity=nil,name="Stunted AK-47"},
    ["akm"]={speed=3900,gravity=nil,name="AKM Assault Rifle"},
    ["akm assault rifle"]={speed=3900,gravity=nil,name="AKM Assault Rifle"},
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
    ["an-94"]={speed=4650,gravity=nil,name="AN-94 Assault Rifle"},
    ["an-94 assault rifle"]={speed=4650,gravity=nil,name="AN-94 Assault Rifle"},
    ["an94"]={speed=4650,gravity=nil,name="AN-94 Assault Rifle"},
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
    ["m1903"]={speed=4425,gravity=nil,name="M1903 Springfield Rifle"},
    ["m1903 springfield"]={speed=4425,gravity=nil,name="M1903 Springfield Rifle"},
    ["springfield"]={speed=4425,gravity=nil,name="M1903 Springfield Rifle"},
    ["m21"]={speed=4425,gravity=nil,name="M21 Marksman Rifle"},
    ["m21 marksman rifle"]={speed=4425,gravity=nil,name="M21 Marksman Rifle"},
    ["mini-14"]={speed=4670,gravity=nil,name="Mini-14 Rifle"},
    ["mini-14 rifle"]={speed=4670,gravity=nil,name="Mini-14 Rifle"},
    ["mini 14"]={speed=4670,gravity=nil,name="Mini-14 Rifle"},
    ["m40a1"]={speed=4575,gravity=nil,name="M40A1 Sniper Rifle"},
    ["m40a1 sniper rifle"]={speed=4575,gravity=nil,name="M40A1 Sniper Rifle"},
    ["l96a1"]={speed=4650,gravity=nil,name="L96A1 Sniper Rifle"},
    ["l96a1 sniper rifle"]={speed=4650,gravity=nil,name="L96A1 Sniper Rifle"},
    ["l96"]={speed=4650,gravity=nil,name="L96A1 Sniper Rifle"},
    ["m1918 tankgewehr"]={speed=3700,gravity=nil,name="M1918 Tankgewehr"},
    ["tankgewehr"]={speed=3700,gravity=nil,name="M1918 Tankgewehr"},
    ["maverick 88"]={speed=2600,gravity=nil,name="Maverick 88 Shotgun"},
    ["maverick 88 shotgun"]={speed=2600,gravity=nil,name="Maverick 88 Shotgun"},
    ["model 590"]={speed=2600,gravity=nil,name="Model 590 Shotgun"},
    ["model 590 shotgun"]={speed=2600,gravity=nil,name="Model 590 Shotgun"},
    ["auto-5"]={speed=2450,gravity=nil,name="Auto-5 Shotgun"},
    ["auto5"]={speed=2450,gravity=nil,name="Auto-5 Shotgun"},
    ["coach gun"]={speed=2530,gravity=nil,name="Coach Gun"},
    ["coach gunmod1"]={speed=2380,gravity=nil,name="Boomstick Coach Gun"},
    ["boomstick coach gun"]={speed=2380,gravity=nil,name="Boomstick Coach Gun"},
    ["spas-12"]={speed=2410,gravity=nil,name="SPAS-12 Combat Shotgun"},
    ["spas12"]={speed=2410,gravity=nil,name="SPAS-12 Combat Shotgun"},
    ["lupara"]={speed=2180,gravity=nil,name="Lupara Shotgun"},
    ["luparamod1"]={speed=2180,gravity=nil,name="Broadside Lupara"},
    ["broadside lupara"]={speed=2180,gravity=nil,name="Broadside Lupara"},
    ["luparamod2"]={speed=2250,gravity=nil,name="Vagrant Lupara"},
    ["vagrant lupara"]={speed=2250,gravity=nil,name="Vagrant Lupara"},
    ["mat-49"]={speed=2740,gravity=nil,name="MAT-49 SMG"},
    ["mat-49 smg"]={speed=2740,gravity=nil,name="MAT-49 SMG"},
    ["mat49"]={speed=2740,gravity=nil,name="MAT-49 SMG"},
    ["m1 thompson"]={speed=2215,gravity=nil,name="M1 Thompson SMG"},
    ["m1 thompson smg"]={speed=2215,gravity=nil,name="M1 Thompson SMG"},
    ["mp 40"]={speed=2765,gravity=nil,name="MP 40 SMG"},
    ["mp40"]={speed=2765,gravity=nil,name="MP 40 SMG"},
    ["m3a1"]={speed=2140,gravity=nil,name="M3A1 SMG"},
    ["m3a1 smg"]={speed=2140,gravity=nil,name="M3A1 SMG"},
    ["ump45"]={speed=2180,gravity=nil,name="UMP45 SMG"},
    ["ump-45"]={speed=2180,gravity=nil,name="UMP45 SMG"},
    ["pp-19 bizon"]={speed=2340,gravity=nil,name="PP-19 Bizon SMG"},
    ["pp19 bizon"]={speed=2340,gravity=nil,name="PP-19 Bizon SMG"},
    ["pp-19"]={speed=2340,gravity=nil,name="PP-19 Bizon SMG"},
    ["mp5k"]={speed=2600,gravity=nil,name="MP5K SMG"},
    ["mp5-k"]={speed=2600,gravity=nil,name="MP5K SMG"},
    ["uzi"]={speed=2665,gravity=nil,name="UZI SMG"},
    ["uzimod1"]={speed=2720,gravity=nil,name="Rogue UZI SMG"},
    ["rogue uzi"]={speed=2720,gravity=nil,name="Rogue UZI SMG"},
    ["ao-46"]={speed=3580,gravity=nil,name="AO-46 SMG"},
    ["ao-46 smg"]={speed=3580,gravity=nil,name="AO-46 SMG"},
    ["ao46"]={speed=3580,gravity=nil,name="AO-46 SMG"},
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
    ["m60mod1"]={speed=1100,gravity=nil,name="\"Santa's Pig\""},
    ["santa's pig"]={speed=1100,gravity=nil,name="\"Santa's Pig\""},
    ["m60"]={speed=4370,gravity=nil,name="M60 Machine Gun"},
    ["m60 machine gun"]={speed=4370,gravity=nil,name="M60 Machine Gun"},
    ["rpk"]={speed=3970,gravity=nil,name="RPK LMG"},
    ["rpk lmg"]={speed=3970,gravity=nil,name="RPK LMG"},
    ["pkm"]={speed=4240,gravity=nil,name="PKM Machine Gun"},
    ["pkm machine gun"]={speed=4240,gravity=nil,name="PKM Machine Gun"},
}

local function getWorkspace()
    if game and game.Workspace then return game.Workspace end
    return nil
end

local function getWorkspaceGravity()
    local ws = getWorkspace()
    if not ws then return 120 end
    local ok, g = pcall(function() return ws.GetGravity() end)
    if ok and type(g) == "number" then return g end
    local ok2, g2 = pcall(function() return ws.Gravity end)
    if ok2 and type(g2) == "number" then return g2 end
    return 120
end

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
    menu.Set("corpse_enabled", Config.ESP.Corpse)
    menu.Set("corpse_box",     Config.ESP.CorpseBoxes)
    menu.Set("corpse_name",    Config.ESP.CorpseNames)
    menu.Set("corpse_dist",    Config.ESP.CorpseDist)
    menu.Set("corpse_maxdist", Config.ESP.CorpseMaxDist)
    menu.Set("aim_enabled",  Config.Aimbot.Enabled)
    menu.Set("aim_visible",  Config.Aimbot.VisibleOnly)
    menu.Set("aim_fov_draw", Config.Aimbot.ShowFov)
    menu.Set("aim_fov",      Config.Aimbot.FOV)
    menu.Set("aim_speed",    Config.Aimbot.Speed)
    menu.Set("aim_lock",     Config.Aimbot.Lock)
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
        else g = getWorkspaceGravity() * Config.Predict.GravityScale end
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

local CorpseCache = {}
local lastCorpseScan = 0
local PlayerHistory = {}

local function rememberPlayers()
    local now = utility.GetTime()
    for _, p in ipairs(entity.GetPlayers()) do
        local pos = p.Position
        if pos then
            local existing = PlayerHistory[p.Name]
            if existing and existing.pos then
                local dx = pos.X - existing.pos.X
                local dy = pos.Y - existing.pos.Y
                local dz = pos.Z - existing.pos.Z
                if math.sqrt(dx*dx + dy*dy + dz*dz) > 5 then
                    existing.consumed = false
                end
            end
            PlayerHistory[p.Name] = {
                pos = pos,
                hp = p.Health or 100,
                time = now,
                consumed = existing and existing.consumed or false,
            }
        end
    end
    for name, data in pairs(PlayerHistory) do
        if now - data.time > 300 then
            PlayerHistory[name] = nil
        end
    end
end

local function findCorpseName(model, root)
    local tagNames = { "PlayerName", "OwnerName", "DeadPlayerName", "Owner" }
    for _, tagName in ipairs(tagNames) do
        local tag = model:FindFirstChild(tagName)
        if tag then
            local val = tag.Value
            if type(val) == "string" and val ~= "" then return val end
            if val and type(val) == "userdata" and val.Name then
                local ok, n = pcall(function() return val.Name end)
                if ok and n and n ~= "" then return n end
            end
        end
    end

    local pos = root.Position
    if pos and next(PlayerHistory) then
        local bestName = nil
        local bestDist = 30
        for name, data in pairs(PlayerHistory) do
            if data.pos and not data.consumed then
                local dx = pos.X - data.pos.X
                local dy = pos.Y - data.pos.Y
                local dz = pos.Z - data.pos.Z
                local d = math.sqrt(dx*dx + dy*dy + dz*dz)
                if d < bestDist then
                    bestDist = d
                    bestName = name
                end
            end
        end
        if bestName then
            PlayerHistory[bestName].consumed = true
            return bestName
        end
    end

    return nil
end

local function scanCorpses()
    if not Config.ESP.Corpse then
        if #CorpseCache > 0 then CorpseCache = {} end
        return
    end

    local now = utility.GetTime()
    if now - lastCorpseScan < 0.5 then return end
    lastCorpseScan = now

    local ws = getWorkspace()
    if not ws then return end

    local corpseFolder = ws:FindFirstChild("Corpses")
    if not corpseFolder then return end

    local kids = corpseFolder:GetChildren()
    local limit = math.min(#kids, 50)

    local live = {}
    for i = 1, limit do
        local m = kids[i]
        if m and m.ClassName == "Model" then live[m] = true end
    end

    local newCache = {}
    for _, entry in ipairs(CorpseCache) do
        if live[entry.model] and entry.model.Parent == corpseFolder then
            newCache[#newCache+1] = entry
        end
    end
    CorpseCache = newCache

    local cachedByModel = {}
    for _, entry in ipairs(CorpseCache) do
        cachedByModel[entry.model] = entry
    end

    for i = 1, limit do
        local m = kids[i]
        if m and m.ClassName == "Model" and not cachedByModel[m] then
            local root = m:FindFirstChild("HumanoidRootPart")
                or m:FindFirstChild("UpperTorso")
                or m:FindFirstChild("Torso")
                or m:FindFirstChild("Head")
            if root then
                local equip = m:FindFirstChild("Equipment")
                local isPlayer = false
                if equip then
                    for _, item in ipairs(equip:GetChildren()) do
                        local n = string.lower(item.Name)
                        if string.find(n, "backpack", 1, true)
                           or string.find(n, "accessory", 1, true)
                           or string.find(n, "vest", 1, true) then
                            isPlayer = true
                            break
                        end
                    end
                end

                if isPlayer then
                    local deadName = findCorpseName(m, root)
                    CorpseCache[#CorpseCache+1] = {
                        model = m,
                        root = root,
                        name = m.Name,
                        deadName = deadName,
                    }
                end
            end
        end
    end
end

local function drawCorpseESP()
    if not Config.ESP.Corpse then return end
    if #CorpseCache == 0 then return end

    local col = Config.ESP.CorpseColor or {0.6,0.2,0.8,1}
    local lp = entity.GetLocalPlayer()

    for _, corpse in ipairs(CorpseCache) do
        if corpse.root and corpse.root.Parent then
            local pos = corpse.root.Position
            if pos then
                local dist = 0
                if lp then dist = lp:DistanceTo(pos) end

                if dist <= Config.ESP.CorpseMaxDist then
                    local sx, sy, onScreen = draw.WorldToScreen(pos.X, pos.Y, pos.Z)
                    if onScreen then
                        local mnx, mny, mxx, mxy = 1e9, 1e9, -1e9, -1e9
                        local any = false
                        local kids = corpse.model:GetChildren()
                        for _, c in ipairs(kids) do
                            if c.ClassName == "MeshPart" or c.ClassName == "Part" then
                                local p = c.Position
                                if p then
                                    local cx, cy, vis = draw.WorldToScreen(p.X, p.Y, p.Z)
                                    if vis then
                                        any = true
                                        if cx < mnx then mnx = cx end
                                        if cx > mxx then mxx = cx end
                                        if cy < mny then mny = cy end
                                        if cy > mxy then mxy = cy end
                                    end
                                end
                            end
                        end

                        local bounds
                        if any then
                            bounds = {x=mnx, y=mny, w=mxx-mnx, h=mxy-mny, valid=true}
                        else
                            bounds = {x=sx-25, y=sy-30, w=50, h=30, valid=true}
                        end

                        if Config.ESP.CorpseBoxes then
                            draw.CornerBox(bounds.x, bounds.y, bounds.w, bounds.h, col)
                        end

                        if Config.ESP.CorpseNames then
                            local txt = corpse.deadName or "Corpse"
                            local tw, th = draw.GetTextSize(txt, 12)
                            draw.Text(bounds.x + bounds.w*0.5 - tw*0.5,
                                      bounds.y - th - 2, txt, col, 12)
                        end

                        if Config.ESP.CorpseDist then
                            local txt = string.format("%dm", math.floor(dist))
                            local tw, th = draw.GetTextSize(txt, 11)
                            draw.Text(bounds.x + bounds.w*0.5 - tw*0.5,
                                      bounds.y + bounds.h + 2, txt, col, 11)
                        end
                    end
                end
            end
        end
    end
end

local AimbotState = { locked = nil }

local function isLockValid()
    local t = AimbotState.locked
    if not t then return false end
    if not t.IsAlive then return false end
    if not t.Character then return false end

    local sw, sh = draw.GetScreenSize()
    local cx, cy = sw * 0.5, sh * 0.5
    local x, y, vis = t:GetBoneScreen(Config.Aimbot.Bone)
    if not vis then return false end
    local dx, dy = x - cx, y - cy
    local d = math.sqrt(dx * dx + dy * dy)
    if d > Config.Aimbot.FOV * 1.5 then return false end
    return true
end

local function getClosestToCrosshair()
    if Config.Aimbot.Lock and AimbotState.locked and isLockValid() then
        return AimbotState.locked, true
    end

    local sw, sh = draw.GetScreenSize()
    local cx, cy = sw * 0.5, sh * 0.5
    local best, bestDist = nil, Config.Aimbot.FOV
    for _, entry in ipairs(getTargets()) do
        local p = entry.player
        local x, y, vis = p:GetBoneScreen(Config.Aimbot.Bone)
        if vis then
            local dx, dy = x - cx, y - cy
            local d = math.sqrt(dx * dx + dy * dy)
            if d < bestDist then
                if not Config.Aimbot.VisibleOnly or raycast.IsPlayerVisible(p.Character) then
                    best, bestDist = p, d
                end
            end
        end
    end
    return best, false
end

local function aimbotFrame()
    if not Config.Aimbot.Enabled then
        AimbotState.locked = nil
        return
    end

    local keyDown = input.IsKeyDown(Config.Aimbot.Key)
    if not keyDown then
        AimbotState.locked = nil
        return
    end

    local target, fromLock = getClosestToCrosshair()
    if not target then
        if AimbotState.locked and not isLockValid() then
            AimbotState.locked = nil
        end
        return
    end

    if Config.Aimbot.Lock and not fromLock then
        AimbotState.locked = target
    end

    local lp = entity.GetLocalPlayer()
    local speed, gravity = getWeaponInfo(lp)
    local origin = camera.GetPosition()
    local targetVel = target.Velocity or Vector3.New(0,0,0)
    local headPos = target.HeadPosition
    if not headPos then return end
    local predicted = predictPosition(origin, headPos, targetVel, speed, gravity)
    local sx, sy, onScreen = draw.WorldToScreen(predicted.X, predicted.Y, predicted.Z)
    if not onScreen then return end
    local sw, sh = draw.GetScreenSize()
    local cx, cy = sw * 0.5, sh * 0.5
    local speedFactor = 1.0 - Config.Aimbot.Speed
    input.MoveMouse((sx-cx)*speedFactor, (sy-cy)*speedFactor)
end

local function drawFovCircle()
    if not Config.Aimbot.Enabled or not Config.Aimbot.ShowFov then return end
    local sw,sh = draw.GetScreenSize()
    draw.Circle(sw*0.5, sh*0.5, Config.Aimbot.FOV, {1,1,1,0.35}, 64, 1.0)
end

local function drawDebug()
    if not Config.Debug then return end
    local lp = entity.GetLocalPlayer()
    local speed, gravity, source, weaponName = getWeaponInfo(lp)
    local lockStatus = "off"
    if Config.Aimbot.Lock then
        if AimbotState.locked then lockStatus = "LOCKED: " .. AimbotState.locked.Name
        else lockStatus = "searching" end
    end
    local histCount = 0
    for _ in pairs(PlayerHistory) do histCount = histCount + 1 end
    draw.Window(10,10,"debug","Weapon Info",{
        "Weapon: " .. tostring(weaponName or "none"),
        "Speed: " .. tostring(math.floor(speed)) .. " (" .. tostring(source) .. ")",
        "Gravity: " .. tostring(gravity or getWorkspaceGravity()) .. " x " .. tostring(Config.Predict.GravityScale),
        "Corpses: " .. tostring(#CorpseCache),
        "History: " .. tostring(histCount),
        "Lock: " .. lockStatus,
        "FPS: " .. string.format("%.0f", utility.GetFPS()),
    })
end

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

menu.AddCheckbox("Vector", "ESP", "corpse_enabled", "Corpse ESP (Players)", true)
menu.AddCheckbox("Vector", "ESP", "corpse_box",     "Corpse Boxes",         true)
menu.AddCheckbox("Vector", "ESP", "corpse_name",    "Corpse Names",         true)
menu.AddCheckbox("Vector", "ESP", "corpse_dist",    "Corpse Distance",      true)
menu.AddSliderInt("Vector", "ESP", "corpse_maxdist", "Corpse Max dist", 10, 2000, 500, "%d")

menu.AddGroup("Vector", "Aimbot")
menu.AddCheckbox("Vector", "Aimbot", "aim_enabled",  "Enable Aimbot", false)
menu.AddCheckbox("Vector", "Aimbot", "aim_visible",  "Visible only",  false)
menu.AddCheckbox("Vector", "Aimbot", "aim_fov_draw", "Show FOV",      true)
menu.AddCheckbox("Vector", "Aimbot", "aim_lock",     "Target Lock",   true)
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
    local ok = saveConfigToFile()
    if ok then notify.Success("Config", "saved", 2)
    else notify.Warning("Config", "save failed", 2) end
end)
menu.AddButton("Vector", "Config", "config_load", "Load Config", function()
    local ok = loadConfigFromFile()
    if ok then notify.Success("Config", "loaded", 2)
    else notify.Warning("Config", "no file found", 2) end
end)

menu.SetCallback("esp_enabled", function(v) Config.ESP.Enabled = v end)
menu.SetCallback("esp_box",     function(v) Config.ESP.Boxes = v end)
menu.SetCallback("esp_name",    function(v) Config.ESP.Names = v end)
menu.SetCallback("esp_health",  function(v) Config.ESP.Health = v end)
menu.SetCallback("esp_dist",    function(v) Config.ESP.Distance = v end)
menu.SetCallback("esp_weapon",  function(v) Config.ESP.Weapon = v end)
menu.SetCallback("esp_skel",    function(v) Config.ESP.Skeleton = v end)
menu.SetCallback("esp_maxdist", function(v) Config.ESP.MaxDist = v end)

menu.SetCallback("corpse_enabled", function(v) Config.ESP.Corpse = v end)
menu.SetCallback("corpse_box",     function(v) Config.ESP.CorpseBoxes = v end)
menu.SetCallback("corpse_name",    function(v) Config.ESP.CorpseNames = v end)
menu.SetCallback("corpse_dist",    function(v) Config.ESP.CorpseDist = v end)
menu.SetCallback("corpse_maxdist", function(v) Config.ESP.CorpseMaxDist = v end)

menu.SetCallback("aim_enabled",  function(v)
    Config.Aimbot.Enabled = v
    if not v then AimbotState.locked = nil end
end)
menu.SetCallback("aim_visible",  function(v) Config.Aimbot.VisibleOnly = v end)
menu.SetCallback("aim_fov_draw", function(v) Config.Aimbot.ShowFov = v end)
menu.SetCallback("aim_lock",     function(v)
    Config.Aimbot.Lock = v
    if not v then AimbotState.locked = nil end
end)
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

pcall(loadConfigFromFile)

OnFrame = function()
    syncAimbotKey()
    rememberPlayers()
    scanCorpses()
    drawCorpseESP()
    drawESP()
    drawFovCircle()
    drawDebug()
    aimbotFrame()
end
