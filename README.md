-- ═══════════════════════════════════════════════════════════════════════
-- AR V2
-- ═══════════════════════════════════════════════════════════════════════

local VERSION = "2.0"
local sqrt, floor, min, max, abs = math.sqrt, math.floor, math.min, math.max, math.abs
local format = string.format

-- ═══════════════════════════════════════════════════════════════════════
-- UI ENGINE
-- ═══════════════════════════════════════════════════════════════════════

local UI = {}
UI.__index = UI

UI.COLORS_DEFAULT = {
    bg          = {0.05, 0.06, 0.10, 0.96},
    titlebar    = {0.08, 0.10, 0.16, 1.00},
    border      = {0.16, 0.22, 0.34, 1.00},
    border_soft = {0.12, 0.16, 0.24, 1.00},
    accent      = {0.25, 0.60, 1.00, 1.00},
    accent_dim  = {0.25, 0.60, 1.00, 0.25},
    text        = {0.92, 0.95, 1.00, 1.00},
    text_dim    = {0.55, 0.60, 0.72, 1.00},
    text_muted  = {0.35, 0.40, 0.50, 1.00},
    panel       = {0.08, 0.10, 0.15, 1.00},
    panel_alt   = {0.11, 0.14, 0.21, 1.00},
    hover       = {0.16, 0.22, 0.34, 1.00},
}
UI.COLORS = {}
for k, v in pairs(UI.COLORS_DEFAULT) do UI.COLORS[k] = {v[1], v[2], v[3], v[4]} end

UI.state = {
    open = true,
    x = 100, y = 60, w = 860, h = 700,
    dragging = false, drag_ox = 0, drag_oy = 0,
    active_tab = 1,
    tabs = {},
    values = {}, colors = {}, keys = {},
    callbacks = {}, visible = {},
    open_combo = nil, open_combo_rect = nil,
    picker = nil, drag_target = nil,
    combo_just_opened = false,
    picker_just_opened = false,
    listening_key = nil, listen_wait_lmb_up = false,
    prev_lmb = false,
    toggle_key = 0x2D,
    scroll = 0,
    btn_down = {},
    ui_settings = {
        accent   = {0.25, 0.60, 1.00, 1.00},
        opacity  = 96,
        window_w = 860,
        window_h = 700,
    },
}

function UI:apply_ui_settings()
    local us = self.state.ui_settings
    local op = (us.opacity or 96) / 100
    for k, v in pairs(self.COLORS_DEFAULT) do
        if type(v) == "table" then
            self.COLORS[k] = {v[1], v[2], v[3], (v[4] or 1) * op}
        end
    end
    local acc = us.accent
    self.COLORS.accent     = {acc[1], acc[2], acc[3], 1.00}
    self.COLORS.accent_dim = {acc[1], acc[2], acc[3], 0.25}
    self.state.w = us.window_w or 860
    self.state.h = us.window_h or 700
end

UI:apply_ui_settings()

local VK_NAMES = {
    [0x01]="LMB",[0x02]="RMB",[0x04]="MMB",[0x08]="Backspace",[0x09]="Tab",
    [0x0D]="Enter",[0x10]="Shift",[0x11]="Ctrl",[0x12]="Alt",[0x1B]="Escape",
    [0x20]="Space",[0x25]="Left",[0x26]="Up",[0x27]="Right",[0x28]="Down",
    [0x2D]="Insert",[0x2E]="Delete",
    [0x30]="0",[0x31]="1",[0x32]="2",[0x33]="3",[0x34]="4",
    [0x35]="5",[0x36]="6",[0x37]="7",[0x38]="8",[0x39]="9",
    [0x41]="A",[0x42]="B",[0x43]="C",[0x44]="D",[0x45]="E",[0x46]="F",
    [0x47]="G",[0x48]="H",[0x49]="I",[0x4A]="J",[0x4B]="K",[0x4C]="L",
    [0x4D]="M",[0x4E]="N",[0x4F]="O",[0x50]="P",[0x51]="Q",[0x52]="R",
    [0x53]="S",[0x54]="T",[0x55]="U",[0x56]="V",[0x57]="W",[0x58]="X",
    [0x59]="Y",[0x5A]="Z",
    [0x60]="Num0",[0x61]="Num1",[0x62]="Num2",[0x63]="Num3",[0x64]="Num4",
    [0x65]="Num5",[0x66]="Num6",[0x67]="Num7",[0x68]="Num8",[0x69]="Num9",
    [0x70]="F1",[0x71]="F2",[0x72]="F3",[0x73]="F4",[0x74]="F5",[0x75]="F6",
    [0x76]="F7",[0x77]="F8",[0x78]="F9",[0x79]="F10",[0x7A]="F11",[0x7B]="F12",
    [0xA0]="LShift",[0xA1]="RShift",[0xA2]="LCtrl",[0xA3]="RCtrl",
    [0xA4]="LAlt",[0xA5]="RAlt",
}

local function vk_name(vk)
    if not vk or vk == 0 then return "[none]" end
    return "[" .. (VK_NAMES[vk] or ("0x" .. format("%02X", vk))) .. "]"
end

local function in_rect(mx, my, x, y, w, h)
    return mx >= x and mx <= x + w and my >= y and my <= y + h
end

local function mouse_pos()
    if utility and utility.get_mouse_pos then
        local ok, mx, my = pcall(utility.get_mouse_pos)
        if ok and mx and my then return mx, my end
    end
    if input and input.get_mouse_position then
        local ok, mx, my = pcall(input.get_mouse_position)
        if ok and mx and my then return mx, my end
    end
    return 0, 0
end

local function key_down(vk)
    if input and input.is_key_down then
        local ok, d = pcall(input.is_key_down, vk)
        return ok and d == true
    end
    return false
end

local function text_w(s, size)
    if draw and draw.get_text_size then
        local ok, w = pcall(draw.get_text_size, tostring(s or ""), size or 13)
        if ok and w then return w end
    end
    return #tostring(s or "") * (size or 13) * 0.55
end

function UI:add_tab(name)
    self.state.tabs[#self.state.tabs + 1] = { name = name, groups = {} }
end

function UI:add_group(tab_name, group_name)
    for _, t in ipairs(self.state.tabs) do
        if t.name == tab_name then
            for _, g in ipairs(t.groups) do
                if g.name == group_name then return end
            end
            t.groups[#t.groups + 1] = { name = group_name, widgets = {} }
            return
        end
    end
    self:add_tab(tab_name)
    self:add_group(tab_name, group_name)
end

local function reg(self, tab, group, id, data)
    for _, t in ipairs(self.state.tabs) do
        if t.name == tab then
            for _, g in ipairs(t.groups) do
                if g.name == group then
                    data.id = id
                    g.widgets[#g.widgets + 1] = data
                    return
                end
            end
        end
    end
    self:add_group(tab, group)
    reg(self, tab, group, id, data)
end

function UI:add_checkbox(tab, group, id, label, default, opts)
    opts = opts or {}
    self.state.values[id] = default == true
    if opts.colorpicker then self.state.colors[id] = opts.colorpicker end
    reg(self, tab, group, id, { type="checkbox", label=label, default=default==true, color=opts.colorpicker, parent=opts.parent })
end

function UI:add_slider_int(tab, group, id, label, mn, mx, default, opts)
    opts = opts or {}
    self.state.values[id] = default or mn
    reg(self, tab, group, id, { type="slider_int", label=label, min=mn, max=mx, default=default, parent=opts.parent, fmt="%d" })
end

function UI:add_slider_float(tab, group, id, label, mn, mx, default, fmt, opts)
    opts = opts or {}
    self.state.values[id] = default or mn
    reg(self, tab, group, id, { type="slider_float", label=label, min=mn, max=mx, default=default, parent=opts.parent, fmt=fmt or "%.2f" })
end

function UI:add_combo(tab, group, id, label, items, default_idx, opts)
    opts = opts or {}
    self.state.values[id] = default_idx or 0
    reg(self, tab, group, id, { type="combo", label=label, items=items, default=default_idx or 0, parent=opts.parent })
end

function UI:add_colorpicker(tab, group, id, label, default, opts)
    opts = opts or {}
    self.state.colors[id] = default or {1,1,1,1}
    reg(self, tab, group, id, { type="color", label=label, default=default, parent=opts.parent })
end

function UI:add_button(tab, group, id, label, callback)
    self.state.callbacks[id] = callback
    reg(self, tab, group, id, { type="button", label=label })
end

function UI:add_hotkey(tab, group, id, label, default_key, opts)
    opts = opts or {}
    self.state.keys[id] = default_key or 0
    reg(self, tab, group, id, { type="hotkey", label=label, default=default_key, parent=opts.parent })
end

function UI:get(id) return self.state.values[id] end
function UI:set(id, v)
    self.state.values[id] = v
    if self.state.callbacks[id] then pcall(self.state.callbacks[id], v) end
end
function UI:get_color(id) return self.state.colors[id] or {1,1,1,1} end
function UI:set_color(id, c) self.state.colors[id] = c end
function UI:get_key(id) return self.state.keys[id] or 0 end
function UI:set_key(id, k) self.state.keys[id] = k end
function UI:set_callback(id, cb) self.state.callbacks[id] = cb end
function UI:set_visible(id, v) self.state.visible[id] = v == true end

local function hsv_to_rgb(h, s, v)
    h = h * 6
    local i = floor(h)
    local f = h - i
    local p, q, t = v*(1-s), v*(1-f*s), v*(1-(1-f)*s)
    if i == 0 then return v, t, p end
    if i == 1 then return q, v, p end
    if i == 2 then return p, v, t end
    if i == 3 then return p, q, v end
    if i == 4 then return t, p, v end
    return v, p, q
end

local function rgb_to_hsv(r, g, b)
    local mx = max(r, g, b)
    local mn = min(r, g, b)
    local d = mx - mn
    local h = 0
    if d > 1e-6 then
        if mx == r then h = ((g - b) / d) % 6
        elseif mx == g then h = (b - r) / d + 2
        else h = (r - g) / d + 4 end
        h = h / 6
    end
    local s = (mx > 0) and (d / mx) or 0
    return h, s, mx
end

local picker_state = { hue = nil, sat = nil, val = nil, dragging_sv = false, dragging_hue = false }

local function draw_color_picker(self, id, x, y, w, h)
    local c = self:get_color(id)
    draw.rect_filled(x + 3, y + 3, w - 6, h - 6, {0,0,0,0.5}, 6)
    local sq = min(w - 60, h - 60)
    local sx, sy = x + 12, y + 30
    local hue, sat, val = rgb_to_hsv(c[1], c[2], c[3])
    if picker_state.hue then hue, sat, val = picker_state.hue, picker_state.sat, picker_state.val end
    local steps = 12
    local cell = sq / steps
    for iy = 0, steps - 1 do
        for ix = 0, steps - 1 do
            local s = ix / (steps - 1)
            local v = 1 - iy / (steps - 1)
            local r, g, b = hsv_to_rgb(hue, s, v)
            draw.rect_filled(sx + ix*cell, sy + iy*cell, cell + 0.5, cell + 0.5, {r,g,b,1}, 0)
        end
    end
    draw.rect(sx, sy, sq, sq, self.COLORS.border, 0, 1)
    local hx, hy, hw, hh = sx + sq + 8, sy, 14, sq
    for i = 0, 17 do
        local t = i / 17
        local r, g, b = hsv_to_rgb(t, 1, 1)
        draw.rect_filled(hx, hy + i*(hh/18), hw, hh/18 + 0.5, {r,g,b,1}, 0)
    end
    draw.rect(hx, hy, hw, hh, self.COLORS.border, 0, 1)
    draw.circle(sx + sat * sq, sy + (1 - val) * sq, 6, {1,1,1,1}, 16, 2)
    draw.circle(sx + sat * sq, sy + (1 - val) * sq, 6, {0,0,0,1}, 16, 1)
    local hue_y = hy + hue * hh
    draw.rect_filled(hx - 2, hue_y - 2, hw + 4, 4, {1,1,1,1}, 2)
    draw.rect(hx - 2, hue_y - 2, hw + 4, 4, {0,0,0,1}, 2, 1)
    local px, py = x + 12, sy + sq + 8
    draw.rect_filled(px, py, sq, 20, c, 4)
    draw.rect(px, py, sq, 20, self.COLORS.border, 4, 1)
    local bx, by, bw, bh = x + w - 78, py, 66, 20
    draw.rect_filled(bx, by, bw, bh, self.COLORS.accent, 4)
    draw.text(bx + 22, by + 4, "OK", {1,1,1,1}, 12)

    local mx, my = mouse_pos()
    local lmb = key_down(0x01)
    if not lmb then picker_state.dragging_sv = false picker_state.dragging_hue = false end
    if self.state.picker_just_opened then return end
    if lmb then
        if picker_state.dragging_sv or in_rect(mx, my, sx, sy, sq, sq) then
            picker_state.dragging_sv = true
            sat = max(0, min(1, (mx - sx) / sq))
            val = max(0, min(1, 1 - (my - sy) / sq))
            local r, g, b = hsv_to_rgb(hue, sat, val)
            c[1], c[2], c[3] = r, g, b
            picker_state.hue, picker_state.sat, picker_state.val = hue, sat, val
            self:set_color(id, c)
            if self.state.callbacks[id] then pcall(self.state.callbacks[id], c) end
        elseif picker_state.dragging_hue or in_rect(mx, my, hx, hy, hw, hh) then
            picker_state.dragging_hue = true
            hue = max(0, min(1, (my - hy) / hh))
            local r, g, b = hsv_to_rgb(hue, sat, val)
            c[1], c[2], c[3] = r, g, b
            picker_state.hue, picker_state.sat, picker_state.val = hue, sat, val
            self:set_color(id, c)
            if self.state.callbacks[id] then pcall(self.state.callbacks[id], c) end
        elseif in_rect(mx, my, bx, by, bw, bh) then
            self.state.picker = nil
            picker_state.hue = nil
        end
    end
end

local function draw_widget(self, w, x, y, wd, h, lmb_down, lmb_click, block_input)
    local mx, my = mouse_pos()
    local hovered = in_rect(mx, my, x, y, wd, h) and not block_input

    if w.type == "checkbox" then
        local on = self.state.values[w.id] == true
        if hovered then draw.rect_filled(x, y, wd, h, self.COLORS.hover, 4) end
        local sw, sh = 30, 16
        local sx, sy = x + 4, y + (h - sh) / 2
        local track = on and self.COLORS.accent or self.COLORS.border_soft
        draw.rect_filled(sx, sy, sw, sh, track, sh/2)
        local knob = sh - 4
        local kx = on and (sx + sw - knob - 2) or (sx + 2)
        draw.circle_filled(kx + knob/2, sy + sh/2, knob/2, self.COLORS.text, 16)
        draw.text(sx + sw + 8, y + (h - 13)/2, w.label, on and self.COLORS.text or self.COLORS.text_dim, 13)
        if self.state.colors[w.id] ~= nil then
            local c = self:get_color(w.id)
            local cx = x + wd - 20
            local cy = y + (h - 14) / 2
            draw.rect_filled(cx, cy, 14, 14, c, 4)
            draw.rect(cx, cy, 14, 14, self.COLORS.border, 4, 1)
            if lmb_click and hovered and in_rect(mx, my, cx - 3, cy - 3, 20, 20) then
                self.state.picker = { id = w.id, x = cx - 100, y = y + h + 4, w = 200, h = 200 }
                self.state.picker_just_opened = true
                picker_state.hue = nil
            end
        end
        if lmb_click and hovered and not (self.state.colors[w.id] and in_rect(mx, my, x + wd - 20, y, 20, h)) then
            self.state.values[w.id] = not on
            if self.state.callbacks[w.id] then pcall(self.state.callbacks[w.id], self.state.values[w.id]) end
        end

    elseif w.type == "slider_int" or w.type == "slider_float" then
        local v = tonumber(self.state.values[w.id]) or w.default or w.min
        local slider_y = y + h - 9
        local slider_h = 5
        local slider_w = wd - 8
        local track_x = x + 4
        local vtxt = format(w.fmt or "%d", v)
        local vw = text_w(vtxt, 12)
        draw.text(x + 4, y + 2, w.label, self.COLORS.text, 12)
        draw.text(x + wd - vw - 4, y + 2, vtxt, self.COLORS.accent, 12)
        draw.rect_filled(track_x, slider_y, slider_w, slider_h, self.COLORS.border_soft, slider_h/2)
        local t = (w.max > w.min) and ((v - w.min) / (w.max - w.min)) or 0
        draw.rect_filled(track_x, slider_y, slider_w * t, slider_h, self.COLORS.accent, slider_h/2)
        draw.circle_filled(track_x + slider_w * t, slider_y + slider_h/2, 6, self.COLORS.text, 16)
        local hot = in_rect(mx, my, track_x, slider_y - 6, slider_w, slider_h + 12) and not block_input
        if lmb_down then
            if not self.state.drag_target and hot then self.state.drag_target = w.id end
            if self.state.drag_target == w.id then
                local nt = max(0, min(1, (mx - track_x) / slider_w))
                local nv = w.min + (w.max - w.min) * nt
                if w.type == "slider_int" then nv = floor(nv + 0.5) end
                self.state.values[w.id] = nv
                if self.state.callbacks[w.id] then pcall(self.state.callbacks[w.id], nv) end
            end
        else
            if self.state.drag_target == w.id then self.state.drag_target = nil end
        end

    elseif w.type == "combo" then
        local idx = tonumber(self.state.values[w.id]) or 0
        if hovered then draw.rect_filled(x, y, wd, h, self.COLORS.hover, 4) end
        draw.text(x + 6, y + (h - 13)/2, w.label, self.COLORS.text_dim, 12)
        local cur = w.items[idx + 1] or "-"
        local cw = text_w(cur, 12)
        draw.text(x + wd - cw - 16, y + (h - 13)/2, cur, self.COLORS.text, 12)
        draw.text(x + wd - 10, y + (h - 13)/2, "v", self.COLORS.text_dim, 10)
        if lmb_click and hovered then
            if self.state.open_combo == w.id then
                self.state.open_combo = nil
                self.state.open_combo_rect = nil
            else
                self.state.open_combo = w.id
                self.state.combo_just_opened = true
                self.state.open_combo_rect = { x = x, y = y + h, w = wd, widget = w }
            end
        end

    elseif w.type == "button" then
        local bg = hovered and self.COLORS.hover or self.COLORS.panel_alt
        draw.rect_filled(x, y + 2, wd, h - 4, bg, 4)
        draw.rect(x, y + 2, wd, h - 4, self.COLORS.border_soft, 4, 1)
        local tw = text_w(w.label, 12)
        draw.text(x + (wd - tw)/2, y + (h - 13)/2, w.label, self.COLORS.text, 12)

        local btn_key = w.id
        if hovered and lmb_down then
            if not self.state.btn_down[btn_key] then
                self.state.btn_down[btn_key] = true
                if self.state.callbacks[w.id] then
                    pcall(self.state.callbacks[w.id])
                end
            end
        else
            self.state.btn_down[btn_key] = false
        end

    elseif w.type == "hotkey" then
        local k = self.state.keys[w.id] or 0
        if hovered then draw.rect_filled(x, y, wd, h, self.COLORS.hover, 4) end
        draw.text(x + 6, y + (h - 13)/2, w.label, self.COLORS.text, 12)
        local kname = (self.state.listening_key == w.id) and "[press...]" or vk_name(k)
        local kw = text_w(kname, 12)
        draw.rect_filled(x + wd - kw - 14, y + 4, kw + 10, h - 8, self.COLORS.panel_alt, 4)
        draw.rect(x + wd - kw - 14, y + 4, kw + 10, h - 8, self.COLORS.border_soft, 4, 1)
        draw.text(x + wd - kw - 9, y + (h - 13)/2, kname, self.COLORS.accent, 12)
        if lmb_click and hovered then
            self.state.listening_key = w.id
            self.state.listen_wait_lmb_up = true
        end

    elseif w.type == "color" then
        local c = self:get_color(w.id)
        if hovered then draw.rect_filled(x, y, wd, h, self.COLORS.hover, 4) end
        draw.text(x + 6, y + (h - 13)/2, w.label, self.COLORS.text, 12)
        local cx = x + wd - 24
        draw.rect_filled(cx, y + 4, 16, h - 8, c, 3)
        draw.rect(cx, y + 4, 16, h - 8, self.COLORS.border, 3, 1)
        if lmb_click and in_rect(mx, my, cx - 3, y, 22, h) then
            self.state.picker = { id = w.id, x = cx - 100, y = y + h + 4, w = 200, h = 200 }
            self.state.picker_just_opened = true
            picker_state.hue = nil
        end
    end
end

local function draw_open_combo_overlay(self)
    if not self.state.open_combo then return end
    local r = self.state.open_combo_rect
    if not r then return end
    local w = r.widget
    if not w then return end

    local mx, my = mouse_pos()
    local lmb_down = key_down(0x01)
    local lmb_click = lmb_down and not self.state.prev_lmb

    local x, y, wd = r.x, r.y, r.w
    local item_h = 20
    local list_h = #w.items * item_h

    if w.type == "combo" then
        local idx = tonumber(self.state.values[w.id]) or 0
        draw.rect_filled(x, y, wd, list_h, self.COLORS.panel_alt, 4)
        draw.rect(x, y, wd, list_h, self.COLORS.border, 4, 1)
        for i, item in ipairs(w.items) do
            local iy = y + (i - 1) * item_h
            local ih = in_rect(mx, my, x, iy, wd, item_h)
            if ih then draw.rect_filled(x + 2, iy + 1, wd - 4, item_h - 2, self.COLORS.hover, 2) end
            local c = (i - 1 == idx) and self.COLORS.accent or self.COLORS.text
            draw.text(x + 8, iy + 4, item, c, 12)
            if lmb_click and ih and not self.state.combo_just_opened then
                self.state.values[w.id] = i - 1
                self.state.open_combo = nil
                self.state.open_combo_rect = nil
                if self.state.callbacks[w.id] then pcall(self.state.callbacks[w.id], i - 1) end
            end
        end
    end

    if lmb_click and not self.state.combo_just_opened then
        if not in_rect(mx, my, x, y, wd, list_h) then
            self.state.open_combo = nil
            self.state.open_combo_rect = nil
        end
    end
end

local function draw_window(self)
    local st = self.state
    if not st.open then st.prev_lmb = key_down(0x01) return end

    local mx, my = mouse_pos()
    local lmb_down = key_down(0x01)
    local lmb_click = lmb_down and not st.prev_lmb

    draw.rect_filled(st.x + 4, st.y + 4, st.w, st.h, {0,0,0,0.35}, 6)
    draw.rect_filled(st.x, st.y, st.w, st.h, self.COLORS.bg, 6)
    draw.rect(st.x, st.y, st.w, st.h, self.COLORS.border, 6, 1.5)

    local th = 34
    draw.rect_filled(st.x + 1, st.y + 1, st.w - 2, th, self.COLORS.titlebar, 5)
    draw.line(st.x, st.y + th, st.x + st.w, st.y + th, self.COLORS.border_soft, 1)
    draw.text(st.x + 14, st.y + 9, "AR V2", self.COLORS.accent, 16)
    local vtxt = "v2"
    local vw = text_w(vtxt, 11)
    draw.text(st.x + st.w - vw - 14, st.y + 11, vtxt, self.COLORS.text_muted, 11)

    if lmb_down then
        if not st.dragging and in_rect(mx, my, st.x, st.y, st.w, th) then
            st.dragging = true
            st.drag_ox = mx - st.x
            st.drag_oy = my - st.y
        elseif st.dragging then
            st.x = mx - st.drag_ox
            st.y = my - st.drag_oy
        end
    else
        st.dragging = false
    end

    local tab_y = st.y + th
    local tab_h = 34
    draw.rect_filled(st.x + 1, tab_y, st.w - 2, tab_h, self.COLORS.panel, 0)
    local tx = st.x + 8
    for i, tab in ipairs(st.tabs) do
        local tw = text_w(tab.name, 13) + 32
        local active = (i == st.active_tab)
        local hovered = in_rect(mx, my, tx, tab_y + 4, tw, tab_h - 8)
        if active then
            draw.rect_filled(tx, tab_y + 4, tw, tab_h - 8, self.COLORS.accent_dim, 4)
            draw.rect_filled(tx, tab_y + tab_h - 3, tw, 2, self.COLORS.accent, 0)
        elseif hovered then
            draw.rect_filled(tx, tab_y + 4, tw, tab_h - 8, self.COLORS.hover, 4)
        end
        local c = active and self.COLORS.text or self.COLORS.text_dim
        draw.text(tx + 16, tab_y + 12, tab.name, c, 13)
        if lmb_click and hovered and not st.combo_just_opened and not st.picker_just_opened then
            st.active_tab = i
            st.scroll = 0
            st.open_combo = nil
            st.open_combo_rect = nil
        end
        tx = tx + tw + 4
    end
    draw.line(st.x, tab_y + tab_h, st.x + st.w, tab_y + tab_h, self.COLORS.border_soft, 1)

    local body_y = tab_y + tab_h + 4
    local body_bottom = st.y + st.h - 8
    local cur_tab = st.tabs[st.active_tab]
    if not cur_tab then st.prev_lmb = lmb_down return end

    local block_input = (st.open_combo ~= nil) or (st.picker ~= nil)
    local pad = 8
    local col_w = floor((st.w - pad * 3) / 2)
    local cols = { st.x + pad, st.x + pad * 2 + col_w }

    local col_h = { 0, 0 }
    local col_assign = {}
    for i, g in ipairs(cur_tab.groups) do
        local hh = 26 + 10
        for _, w in ipairs(g.widgets) do
            if self.state.visible[w.id] ~= false then hh = hh + 26 + 4 end
        end
        local ci = (col_h[1] <= col_h[2]) and 1 or 2
        col_assign[i] = ci
        col_h[ci] = col_h[ci] + hh + 10
    end
    local total_h = max(col_h[1], col_h[2]) + pad * 2
    local view_h = body_bottom - body_y
    local max_scroll = max(0, total_h - view_h)
    st.scroll = max(0, min(max_scroll, st.scroll or 0))

    local ys = { body_y + pad - st.scroll, body_y + pad - st.scroll }

    for i, g in ipairs(cur_tab.groups) do
        local ci = col_assign[i]
        local gx = cols[ci]
        local gy = ys[ci]

        local header_h = 26
        local row_h = 26
        local content_h = 0
        for _, w in ipairs(g.widgets) do
            if self.state.visible[w.id] ~= false then content_h = content_h + row_h + 4 end
        end
        local group_h = header_h + content_h + 10

        if gy < body_bottom and gy + group_h > body_y then
            local clip_top = max(gy, body_y)
            local clip_bottom = min(gy + group_h, body_bottom)
            local clip_h = clip_bottom - clip_top
            if clip_h > 0 then
                draw.rect_filled(gx, clip_top, col_w, clip_h, self.COLORS.panel, 5)
                draw.rect(gx, clip_top, col_w, clip_h, self.COLORS.border_soft, 5, 1)

                if gy >= body_y and gy < body_bottom then
                    draw.rect_filled(gx + 1, gy + 1, col_w - 2, header_h - 2, self.COLORS.panel_alt, 4)
                    draw.rect_filled(gx + 1, gy + 1, 3, header_h - 2, self.COLORS.accent, 2)
                    draw.text(gx + 12, gy + 7, g.name, self.COLORS.text, 13)
                end

                local wy = gy + header_h + 2
                for _, w in ipairs(g.widgets) do
                    if self.state.visible[w.id] ~= false then
                        if wy >= body_y and wy + row_h <= body_bottom then
                            draw_widget(self, w, gx + 6, wy, col_w - 12, row_h, lmb_down, lmb_click, block_input)
                        end
                        wy = wy + row_h + 4
                    end
                end
            end
        end
        ys[ci] = gy + group_h + 10
    end

    if max_scroll > 0 then
        local sb_x = st.x + st.w - 6
        local sb_w = 4
        local bar_h = max(20, view_h * (view_h / total_h))
        local bar_y = body_y + (view_h - bar_h) * (st.scroll / max_scroll)
        draw.rect_filled(sb_x, body_y, sb_w, view_h, {1,1,1,0.05}, 2)
        draw.rect_filled(sb_x, bar_y, sb_w, bar_h, self.COLORS.accent, 2)
    end

    draw_open_combo_overlay(self)

    if st.picker then
        local p = st.picker
        draw.rect_filled(p.x - 2, p.y - 2, p.w + 4, p.h + 4, {0,0,0,0.6}, 8)
        draw_color_picker(self, p.id, p.x, p.y, p.w, p.h)
        if lmb_click and not st.picker_just_opened and not picker_state.dragging_sv and not picker_state.dragging_hue then
            if not in_rect(mx, my, p.x - 10, p.y - 10, p.w + 20, p.h + 20) then
                st.picker = nil
                picker_state.hue = nil
            end
        end
    end

    st.combo_just_opened = false
    st.picker_just_opened = false
    st.prev_lmb = lmb_down
end

local function process_hotkey_listening(self)
    if not self.state.listening_key then return end
    if self.state.listen_wait_lmb_up then
        if not key_down(0x01) then self.state.listen_wait_lmb_up = false end
        return
    end
    if key_down(0x1B) then
        self.state.listening_key = nil
        return
    end
    for vk = 1, 254 do
        if vk ~= 0x01 and key_down(vk) then
            self.state.keys[self.state.listening_key] = vk
            if self.state.callbacks[self.state.listening_key] then
                pcall(self.state.callbacks[self.state.listening_key], vk)
            end
            self.state.listening_key = nil
            break
        end
    end
end

local _prev_toggle = false
local function process_toggle(self)
    local tk = self.state.toggle_key
    if not tk or tk == 0 then _prev_toggle = false return end
    if self.state.listening_key == "menu_toggle_key" then
        _prev_toggle = key_down(tk)
        return
    end
    local kd = key_down(tk)
    if kd and not _prev_toggle then
        self.state.open = not self.state.open
    end
    _prev_toggle = kd
end

-- ═══════════════════════════════════════════════════════════════════════
-- WEAPON DATABASE
-- ═══════════════════════════════════════════════════════════════════════

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
    -- ▼▼▼ FIX: MP5 (sem K) — estava faltando, caía em mp5-k por substring ▼▼▼
    ["mp5"]={speed=2700,gravity=nil,name="MP5 SMG"},
    ["mp5 smg"]={speed=2700,gravity=nil,name="MP5 SMG"},
    ["mp5a2"]={speed=2700,gravity=nil,name="MP5A2 SMG"},
    ["mp5a3"]={speed=2700,gravity=nil,name="MP5A3 SMG"},
    ["mp5a4"]={speed=2700,gravity=nil,name="MP5A4 SMG"},
    ["mp5a5"]={speed=2700,gravity=nil,name="MP5A5 SMG"},
    ["mp5sd"]={speed=2850,gravity=nil,name="MP5SD SMG"},
    ["mp5sd6"]={speed=2850,gravity=nil,name="MP5SD6 SMG"},
    -- ▲▲▲ FIM DO FIX ▲▲▲
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

local function extract_item_name(json)
    if not json or json == "" then return nil end
    local name = string.match(json, '"ItemName"%s*:%s*"([^"]*)"')
    if name and name ~= "" then return name end
    return nil
end

local function get_friendly_weapon_name(item_name)
    if not item_name then return nil end
    local key = string.lower(item_name)
    local entry = WEAPON_DB[key]
    if entry then return entry.name end
    for db_key, db_entry in pairs(WEAPON_DB) do
        if string.find(key, db_key, 1, true) then
            return db_entry.name
        end
    end
    return item_name
end

local function get_enemy_weapon(player)
    local char = player.character
    if not char then return nil end
    local animator = char:find_first_child("Animator")
    if not animator then return nil end
    local eq = animator:find_first_child("EquippedItem")
    if not eq then return nil end
    return extract_item_name(eq.value)
end

-- ═══════════════════════════════════════════════════════════════════════
-- FIX: get_weapon_info — match EXATO primeiro, depois substring
--      UNIDIRECIONAL (só "key contém db_key"). Removido o inverso que
--      causava mp5 -> mp5-k e m16a1 -> m16a2.
-- ═══════════════════════════════════════════════════════════════════════
local function get_weapon_info(player)
    local item_name = get_enemy_weapon(player)
    if not item_name then return 1500, nil, "fallback", nil end
    local key = string.lower(item_name)

    -- 1) Match EXATO primeiro
    local entry = WEAPON_DB[key]
    if entry then return entry.speed, entry.gravity, "db:"..key, item_name end

    -- 2) Match por substring UNIDIRECIONAL (key contém db_key).
    --    Escolhe o db_key MAIS LONGO que casa.
    local best_key, best_entry, best_len = nil, nil, 0
    for db_key, db_entry in pairs(WEAPON_DB) do
        if string.find(key, db_key, 1, true) then
            if #db_key > best_len then
                best_key, best_entry, best_len = db_key, db_entry, #db_key
            end
        end
    end
    if best_entry then return best_entry.speed, best_entry.gravity, "db~"..best_key, item_name end

    return 1500, nil, "unknown:"..item_name, item_name
end

local function get_workspace()
    if game and game.workspace then return game.workspace end
    return nil
end

local function get_workspace_gravity()
    local ws = get_workspace()
    if not ws then return 120 end
    local ok, g = pcall(function() return ws.get_gravity() end)
    if ok and type(g) == "number" then return g end
    return 120
end

-- ═══════════════════════════════════════════════════════════════════════
-- MENU / REGISTRO DA UI
-- ═══════════════════════════════════════════════════════════════════════

local base_ui = setmetatable({}, UI)

base_ui:add_tab("ESP")
base_ui:add_tab("Aimbot")
base_ui:add_tab("Prediction")
base_ui:add_tab("Config")

base_ui:add_group("ESP", "ESP")
base_ui:add_checkbox("ESP", "ESP", "esp_enabled", "Enable ESP", true)
base_ui:add_checkbox("ESP", "ESP", "esp_box",     "Boxes",      true)
base_ui:add_checkbox("ESP", "ESP", "esp_name",    "Names",      true)
base_ui:add_checkbox("ESP", "ESP", "esp_health",  "Health",     true)
base_ui:add_checkbox("ESP", "ESP", "esp_dist",    "Distance",   true)
base_ui:add_checkbox("ESP", "ESP", "esp_weapon",  "Weapon",     true)
base_ui:add_checkbox("ESP", "ESP", "esp_skel",    "Skeleton",   true)
base_ui:add_slider_int("ESP", "ESP", "esp_maxdist", "Max dist", 50, 5000, 2000)

base_ui:add_group("ESP", "Corpse ESP")
base_ui:add_checkbox("ESP", "Corpse ESP", "corpse_enabled", "Enable Corpse ESP", true)
base_ui:add_checkbox("ESP", "Corpse ESP", "corpse_box",     "Corpse Boxes",      true)
base_ui:add_checkbox("ESP", "Corpse ESP", "corpse_name",    "Corpse Names",      true)
base_ui:add_checkbox("ESP", "Corpse ESP", "corpse_dist",    "Corpse Distance",   true)
base_ui:add_slider_int("ESP", "Corpse ESP", "corpse_maxdist", "Corpse Max dist", 10, 2000, 500)

base_ui:add_group("Aimbot", "Aimbot")
base_ui:add_checkbox("Aimbot", "Aimbot", "aim_enabled",  "Enable Aimbot", false)
base_ui:add_checkbox("Aimbot", "Aimbot", "aim_visible",  "Visible only",  false)
base_ui:add_checkbox("Aimbot", "Aimbot", "aim_fov_draw", "Show FOV",      true)
base_ui:add_checkbox("Aimbot", "Aimbot", "aim_lock",     "Target Lock",   true)
base_ui:add_hotkey  ("Aimbot", "Aimbot", "aim_key",      "Key", 0x02)
base_ui:add_slider_int("Aimbot", "Aimbot", "aim_fov",     "FOV", 10, 500, 120)
base_ui:add_slider_float("Aimbot", "Aimbot", "aim_speed", "Speed (0=instant)", 0.0, 0.99, 0.65, "%.2f")
base_ui:add_combo   ("Aimbot", "Aimbot", "aim_bone", "Bone", {"Head","UpperTorso","HumanoidRootPart"}, 0)

base_ui:add_group("Prediction", "Prediction")
base_ui:add_checkbox("Prediction", "Prediction", "pred_enabled", "Enable Predict", true)
base_ui:add_checkbox("Prediction", "Prediction", "pred_gravity", "Compensate Gravity", true)
base_ui:add_checkbox("Prediction", "Prediction", "pred_debug",   "Show Debug Window", false)
base_ui:add_slider_float("Prediction", "Prediction", "pred_gscale", "Gravity scale", 0.0, 2.0, 0.55, "%.2f")
base_ui:add_slider_float("Prediction", "Prediction", "pred_maxt",  "Max flight time", 0.05, 2.0, 1.0, "%.2fs")
base_ui:add_slider_int("Prediction", "Prediction", "pred_fallback", "Fallback speed", 100, 5000, 1500)

base_ui:add_group("Config", "Config")
base_ui:add_button("Config", "Config", "config_save", "Save Config", function()
    save_config()
end)
base_ui:add_button("Config", "Config", "config_load", "Load Config", function()
    load_config()
end)

base_ui:add_group("Config", "UI")
base_ui:add_hotkey("Config", "UI", "menu_toggle_key", "Menu Toggle", 0x2D)
base_ui:add_colorpicker("Config", "UI", "ui_accent", "Accent Color", {0.25, 0.60, 1.00, 1.00})
base_ui:add_slider_int("Config", "UI", "ui_opacity", "Opacity (%)", 30, 100, 96)
base_ui:add_slider_int("Config", "UI", "ui_width", "Window Width", 500, 1600, 860)
base_ui:add_slider_int("Config", "UI", "ui_height", "Window Height", 400, 1000, 700)

base_ui:set_callback("menu_toggle_key", function(k)
    base_ui.state.toggle_key = k
    _prev_toggle = key_down(k)
end)
base_ui:set_callback("ui_accent", function(c)
    base_ui.state.ui_settings.accent = c
    base_ui:apply_ui_settings()
end)
base_ui:set_callback("ui_opacity", function(v)
    base_ui.state.ui_settings.opacity = v
    base_ui:apply_ui_settings()
end)
base_ui:set_callback("ui_width", function(v)
    base_ui.state.ui_settings.window_w = v
    base_ui:apply_ui_settings()
end)
base_ui:set_callback("ui_height", function(v)
    base_ui.state.ui_settings.window_h = v
    base_ui:apply_ui_settings()
end)

-- ═══════════════════════════════════════════════════════════════════════
-- SAVE / LOAD
-- ═══════════════════════════════════════════════════════════════════════

local CONFIG_NAME = "ar_v2_config.txt"

local function get_config_paths()
    local paths = {}
    pcall(function()
        local ad = os.getenv("LOCALAPPDATA")
        if ad then
            paths[#paths+1] = ad .. "\\Project Vector\\Scripts\\" .. CONFIG_NAME
            paths[#paths+1] = ad .. "\\" .. CONFIG_NAME
        end
        local up = os.getenv("USERPROFILE")
        if up then
            paths[#paths+1] = up .. "\\Desktop\\" .. CONFIG_NAME
        end
    end)
    paths[#paths+1] = CONFIG_NAME
    paths[#paths+1] = ".\\" .. CONFIG_NAME
    return paths
end

local SAVE_ITEMS = {
    {"esp_enabled","bool"},{"esp_box","bool"},{"esp_name","bool"},{"esp_health","bool"},
    {"esp_dist","bool"},{"esp_weapon","bool"},{"esp_skel","bool"},{"esp_maxdist","int"},
    {"corpse_enabled","bool"},{"corpse_box","bool"},{"corpse_name","bool"},
    {"corpse_dist","bool"},{"corpse_maxdist","int"},
    {"aim_enabled","bool"},{"aim_visible","bool"},{"aim_fov_draw","bool"},{"aim_lock","bool"},
    {"aim_key","key"},
    {"aim_fov","int"},{"aim_speed","float"},{"aim_bone","int"},
    {"pred_enabled","bool"},{"pred_gravity","bool"},{"pred_debug","bool"},
    {"pred_gscale","float"},{"pred_maxt","float"},{"pred_fallback","int"},
    {"menu_toggle_key","key"},
    {"ui_accent","color"},
    {"ui_opacity","int"},{"ui_width","int"},{"ui_height","int"},
}

save_config = function()
    print("[AR V2] save_config() called")

    local ok, err = pcall(function()
        local content = {}
        content[#content+1] = "-- AR V2 Config\n"
        content[#content+1] = "version=" .. VERSION .. "\n"

        for _, item in ipairs(SAVE_ITEMS) do
            local id = item[1]
            local kind = item[2]

            if kind == "bool" then
                local v = base_ui.state.values[id]
                content[#content+1] = id .. "=" .. (v and "1" or "0") .. "\n"
            elseif kind == "int" then
                local v = base_ui.state.values[id]
                content[#content+1] = id .. "=" .. tostring(tonumber(v) or 0) .. "\n"
            elseif kind == "float" then
                local v = base_ui.state.values[id]
                content[#content+1] = id .. "=" .. tostring(tonumber(v) or 0) .. "\n"
            elseif kind == "combo" then
                local v = base_ui.state.values[id]
                content[#content+1] = id .. "=" .. tostring(tonumber(v) or 0) .. "\n"
            elseif kind == "key" then
                local k = base_ui.state.keys[id] or 0
                content[#content+1] = id .. "_key=" .. tostring(k) .. "\n"
            elseif kind == "color" then
                local c = base_ui.state.colors[id]
                if type(c) == "table" then
                    content[#content+1] = id .. "_color=" ..
                        format("%.4f,%.4f,%.4f,%.4f",
                            tonumber(c[1]) or 1,
                            tonumber(c[2]) or 1,
                            tonumber(c[3]) or 1,
                            tonumber(c[4]) or 1) .. "\n"
                end
            end
        end

        local text = table.concat(content)
        print("[AR V2] Conteudo gerado: " .. #text .. " bytes")

        local saved = false
        local last_error = nil

        for _, path in ipairs(get_config_paths()) do
            local ok_open, f = pcall(io.open, path, "w")
            if ok_open and f then
                local ok_write, werr = pcall(function()
                    f:write(text)
                    f:close()
                end)
                if ok_write then
                    print("[AR V2] Saved to: " .. path)
                    if notify and notify.success then
                        notify.success("AR V2", "Config saved!")
                    end
                    saved = true
                    break
                else
                    last_error = "write: " .. tostring(werr)
                    pcall(function() f:close() end)
                end
            else
                last_error = "open: " .. tostring(f)
            end
        end

        if not saved then
            print("[AR V2] Save failed: " .. tostring(last_error))
            if notify and notify.warning then
                notify.warning("AR V2", "Save failed")
            end
        end
    end)

    if not ok then
        print("[AR V2] save_config ERRO: " .. tostring(err))
        if notify and notify.error then
            notify.error("AR V2", "Save error: " .. tostring(err))
        end
    end
end

load_config = function()
    print("[AR V2] load_config() called")

    local ok, err = pcall(function()
        local content = nil
        local loaded_path = nil

        for _, path in ipairs(get_config_paths()) do
            local ok_r, c = pcall(function()
                local f = io.open(path, "r")
                if not f then return nil end
                local d = f:read("*a")
                f:close()
                return d
            end)
            if ok_r and c and #c > 0 then
                content = c
                loaded_path = path
                break
            end
        end

        if not content then
            print("[AR V2] No config file found")
            return
        end

        print("[AR V2] Config found at: " .. loaded_path)

        local data = {}
        for line in content:gmatch("[^\r\n]+") do
            local k, v = line:match("^([^=]+)=(.*)$")
            if k then data[k] = v end
        end

        local restored = 0

        for _, item in ipairs(SAVE_ITEMS) do
            local id = item[1]
            local kind = item[2]

            if kind == "bool" then
                if data[id] then
                    base_ui.state.values[id] = (data[id] == "1")
                    restored = restored + 1
                end
            elseif kind == "int" then
                if data[id] then
                    base_ui.state.values[id] = tonumber(data[id]) or 0
                    restored = restored + 1
                end
            elseif kind == "float" then
                if data[id] then
                    base_ui.state.values[id] = tonumber(data[id]) or 0
                    restored = restored + 1
                end
            elseif kind == "combo" then
                if data[id] then
                    base_ui.state.values[id] = tonumber(data[id]) or 0
                    restored = restored + 1
                end
            elseif kind == "key" then
                if data[id .. "_key"] then
                    base_ui.state.keys[id] = tonumber(data[id .. "_key"]) or 0
                    restored = restored + 1
                end
            elseif kind == "color" then
                local ck = id .. "_color"
                if data[ck] then
                    local r, g, b, a = data[ck]:match("([%d%.%-]+),([%d%.%-]+),([%d%.%-]+),([%d%.%-]+)")
                    if r then
                        base_ui.state.colors[id] = {tonumber(r), tonumber(g), tonumber(b), tonumber(a) or 1}
                        restored = restored + 1
                    end
                end
            end
        end

        if base_ui.state.colors["ui_accent"] then
            base_ui.state.ui_settings.accent = base_ui.state.colors["ui_accent"]
            base_ui:apply_ui_settings()
        end

        if base_ui.state.keys["menu_toggle_key"] then
            base_ui.state.toggle_key = base_ui.state.keys["menu_toggle_key"]
            _prev_toggle = key_down(base_ui.state.toggle_key)
        end

        print("[AR V2] Loaded " .. restored .. " items")
        print("[AR V2] === VERIFICACAO ===")
        print("  esp_enabled = " .. tostring(base_ui.state.values["esp_enabled"]))
        print("  aim_enabled = " .. tostring(base_ui.state.values["aim_enabled"]))
        print("  pred_gscale = " .. tostring(base_ui.state.values["pred_gscale"]))
        print("  pred_maxt   = " .. tostring(base_ui.state.values["pred_maxt"]))
        print("  pred_fallback = " .. tostring(base_ui.state.values["pred_fallback"]))
        print("===========================")

        if notify and notify.success then
            notify.success("AR V2", "Config loaded!")
        end
    end)

    if not ok then
        print("[AR V2] load_config ERRO: " .. tostring(err))
        if notify and notify.error then
            notify.error("AR V2", "Load error: " .. tostring(err))
        end
    end
end

-- ═══════════════════════════════════════════════════════════════════════
-- HELPERS
-- ═══════════════════════════════════════════════════════════════════════

local function is_enemy(p)
    if not p or p.is_local or not p.is_alive then return false end
    return true
end

local function get_targets()
    local max_dist = base_ui:get("esp_maxdist") or 2000
    local list = {}
    for _, p in ipairs(entity.get_players()) do
        if is_enemy(p) then
            local dist = p:DistanceTo()
            if dist <= max_dist then
                list[#list+1] = { player = p, dist = dist }
            end
        end
    end
    table.sort(list, function(a, b) return a.dist < b.dist end)
    return list
end

local function predict_position(origin, target_pos, target_vel, speed, gravity)
    if not base_ui:get("pred_enabled") or not speed or speed <= 0 then return target_pos end
    local g = 0
    if base_ui:get("pred_gravity") then
        local gs = base_ui:get("pred_gscale") or 0.55
        if gravity ~= nil then g = gravity * gs
        else g = get_workspace_gravity() * gs end
    end
    local D = target_pos - origin
    local Vt = target_vel
    local a = Vt:Dot(Vt) - speed * speed
    local b = 2 * D:Dot(Vt)
    local c = D:Dot(D)
    local t
    if abs(a) < 1e-6 then
        if abs(b) < 1e-6 then t = 0 else t = -c / b end
    else
        local disc = b*b - 4*a*c
        if disc < 0 then return target_pos end
        local sqrtD = sqrt(disc)
        local t1 = (-b - sqrtD) / (2*a)
        local t2 = (-b + sqrtD) / (2*a)
        if t1 > 0 and t2 > 0 then t = min(t1, t2)
        elseif t1 > 0 then t = t1
        elseif t2 > 0 then t = t2
        else return target_pos end
    end
    local maxT = base_ui:get("pred_maxt") or 1.0
    if t < 0 then t = 0 end
    if t > maxT then t = maxT end
    local predicted = target_pos + Vt * t
    if g > 0 then predicted = predicted + Vector3.New(0, 0.5 * g * t * t, 0) end
    return predicted
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

local function draw_skeleton(player)
    if not base_ui:get("esp_skel") then return end
    local bones = player:GetBonesScreen()
    if not bones then return end
    local col = {1, 0, 0, 1}
    for _, conn in ipairs(SKELETON_CONNECTIONS) do
        local a = bones[conn[1]]
        local b = bones[conn[2]]
        if a and b then draw.line(a[1],a[2],b[1],b[2],col,1.5) end
    end
end

local function draw_esp()
    if not base_ui:get("esp_enabled") then return end
    for _, entry in ipairs(get_targets()) do
        local p = entry.player
        draw_skeleton(p)
        local bounds = p:GetBounds()
        if bounds.valid then
            if base_ui:get("esp_box") then
                draw.corner_box(bounds.x, bounds.y, bounds.w, bounds.h, {1,1,1,1})
            end
            if base_ui:get("esp_name") or base_ui:get("esp_weapon") then
                local topY = bounds.y - 4
                if base_ui:get("esp_name") then
                    local tw, th = draw.get_text_size(p.name, 14)
                    draw.text(bounds.x + bounds.w*0.5 - tw*0.5, topY - th, p.name, {1,1,1,1}, 14)
                    topY = topY - th - 2
                end
                if base_ui:get("esp_weapon") then
                    local raw = get_enemy_weapon(p)
                    local friendly = get_friendly_weapon_name(raw)
                    if friendly then
                        local col = {0.7,0.7,0.7,1}
                        local lower = string.lower(friendly)
                        if string.find(lower,"sniper") or string.find(lower,"marksman")
                           or string.find(lower,"dragunov") or string.find(lower,"psg")
                           or string.find(lower,"m40") or string.find(lower,"mosin") then
                            col = {1,0.6,0.2,1}
                        end
                        local tw2, th2 = draw.get_text_size(friendly, 12)
                        draw.text(bounds.x + bounds.w*0.5 - tw2*0.5, topY - th2, friendly, col, 12)
                    end
                end
            end
            if base_ui:get("esp_health") then
                draw.health_bar(bounds.x - 6, bounds.y, bounds.h, p.health, p.max_health)
            end
            if base_ui:get("esp_dist") then
                draw.text(bounds.x + bounds.w + 4, bounds.y + bounds.h*0.5,
                    format("%dm", floor(entry.dist)), {0.8,0.8,0.8,1}, 12)
            end
        end
    end
end

-- ═══════════════════════════════════════════════════════════════════════
-- CORPSE ESP
-- ═══════════════════════════════════════════════════════════════════════

local corpse_cache = {}
local last_corpse_scan = 0
local player_history = {}

local function remember_players()
    local now = utility.get_time()
    for _, p in ipairs(entity.get_players()) do
        local pos = p.position
        if pos then
            local existing = player_history[p.name]
            if existing and existing.pos then
                local dx = pos.x - existing.pos.x
                local dy = pos.y - existing.pos.y
                local dz = pos.z - existing.pos.z
                if sqrt(dx*dx + dy*dy + dz*dz) > 5 then existing.consumed = false end
            end
            player_history[p.name] = {
                pos = pos, hp = p.health or 100, time = now,
                consumed = existing and existing.consumed or false,
            }
        end
    end
    for name, data in pairs(player_history) do
        if now - data.time > 300 then player_history[name] = nil end
    end
end

local function find_corpse_name(model, root)
    local tags = { "PlayerName", "OwnerName", "DeadPlayerName", "Owner" }
    for _, t in ipairs(tags) do
        local tag = model:find_first_child(t)
        if tag then
            local val = tag.value
            if type(val) == "string" and val ~= "" then return val end
            if val and type(val) == "userdata" and val.name then
                local ok, n = pcall(function() return val.name end)
                if ok and n and n ~= "" then return n end
            end
        end
    end
    local pos = root.position
    if pos and next(player_history) then
        local best_name, best_dist = nil, 30
        for name, data in pairs(player_history) do
            if data.pos and not data.consumed then
                local dx = pos.x - data.pos.x
                local dy = pos.y - data.pos.y
                local dz = pos.z - data.pos.z
                local d = sqrt(dx*dx + dy*dy + dz*dz)
                if d < best_dist then best_dist = d best_name = name end
            end
        end
        if best_name then
            player_history[best_name].consumed = true
            return best_name
        end
    end
    return nil
end

local function scan_corpses()
    if not base_ui:get("corpse_enabled") then
        if #corpse_cache > 0 then corpse_cache = {} end
        return
    end

    local now = utility.get_time()
    if now - last_corpse_scan < 0.5 then return end
    last_corpse_scan = now

    corpse_cache = {}

    local ws = get_workspace()
    if not ws then return end

    local folder = ws:find_first_child("Corpses")
    if not folder then return end

    local kids = folder:get_children()
    local limit = min(#kids, 50)

    for i = 1, limit do
        local m = kids[i]
        if m and m.class_name == "Model" then

            -- ============================================
            -- FILTRO: pula zumbis (nome contém "Infected")
            -- ============================================
            local name_lower = string.lower(m.name)
            local is_infected = string.find(name_lower, "infected", 1, true) ~= nil

            if not is_infected then
                local root = m:find_first_child("HumanoidRootPart")
                    or m:find_first_child("UpperTorso")
                    or m:find_first_child("Torso")
                    or m:find_first_child("Head")
                if root then
                    local dead_name = find_corpse_name(m, root)
                    corpse_cache[#corpse_cache+1] = {
                        model = m,
                        root = root,
                        name = m.name,
                        dead_name = dead_name,
                    }
                end
            end
        end
    end
end

local function draw_corpse_esp()
    if not base_ui:get("corpse_enabled") then return end
    if #corpse_cache == 0 then return end
    local col = {0.6, 0.2, 0.8, 1}
    local lp = entity.get_local_player()
    local max_dist = base_ui:get("corpse_maxdist") or 500
    for _, corpse in ipairs(corpse_cache) do
        if corpse.root and corpse.root.parent then
            local pos = corpse.root.position
            if pos then
                local dist = 0
                if lp then dist = lp:DistanceTo(pos) end
                if dist <= max_dist then
                    local sx, sy, on = draw.world_to_screen(pos.x, pos.y, pos.z)
                    if on then
                        local mnx, mny, mxx, mxy = 1e9, 1e9, -1e9, -1e9
                        local any = false
                        for _, c in ipairs(corpse.model:get_children()) do
                            if c.class_name == "MeshPart" or c.class_name == "Part" then
                                local p = c.position
                                if p then
                                    local cx, cy, vis = draw.world_to_screen(p.x, p.y, p.z)
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
                        local b = any and {x=mnx, y=mny, w=mxx-mnx, h=mxy-mny}
                                    or {x=sx-25, y=sy-30, w=50, h=30}
                        if base_ui:get("corpse_box") then
                            draw.corner_box(b.x, b.y, b.w, b.h, col)
                        end
                        if base_ui:get("corpse_name") then
                            local txt = corpse.dead_name or "Corpse"
                            local tw, th = draw.get_text_size(txt, 12)
                            draw.text(b.x + b.w*0.5 - tw*0.5, b.y - th - 2, txt, col, 12)
                        end
                        if base_ui:get("corpse_dist") then
                            local txt = format("%dm", floor(dist))
                            local tw, th = draw.get_text_size(txt, 11)
                            draw.text(b.x + b.w*0.5 - tw*0.5, b.y + b.h + 2, txt, col, 11)
                        end
                    end
                end
            end
        end
    end
end

-- ═══════════════════════════════════════════════════════════════════════
-- AIMBOT
-- ═══════════════════════════════════════════════════════════════════════

local aimbot_state = { locked = nil }

local function is_lock_valid()
    local t = aimbot_state.locked
    if not t then return false end
    if not t.is_alive then return false end
    if not t.character then return false end
    local sw, sh = draw.get_screen_size()
    local cx, cy = sw * 0.5, sh * 0.5
    local bones = {"Head","UpperTorso","HumanoidRootPart"}
    local idx = base_ui:get("aim_bone") or 0
    local bone = bones[idx + 1] or "Head"
    local x, y, vis = t:GetBoneScreen(bone)
    if not vis then return false end
    local dx, dy = x - cx, y - cy
    local d = sqrt(dx*dx + dy*dy)
    local fov = base_ui:get("aim_fov") or 120
    if d > fov * 1.5 then return false end
    return true
end

local function get_closest_to_crosshair()
    if base_ui:get("aim_lock") and aimbot_state.locked and is_lock_valid() then
        return aimbot_state.locked, true
    end
    local sw, sh = draw.get_screen_size()
    local cx, cy = sw * 0.5, sh * 0.5
    local fov = base_ui:get("aim_fov") or 120
    local best, best_dist = nil, fov
    local bones = {"Head","UpperTorso","HumanoidRootPart"}
    local idx = base_ui:get("aim_bone") or 0
    local bone = bones[idx + 1] or "Head"
    local visible_only = base_ui:get("aim_visible")
    for _, entry in ipairs(get_targets()) do
        local p = entry.player
        local x, y, vis = p:GetBoneScreen(bone)
        if vis then
            local dx, dy = x - cx, y - cy
            local d = sqrt(dx*dx + dy*dy)
            if d < best_dist then
                if not visible_only or raycast.IsPlayerVisible(p.character) then
                    best, best_dist = p, d
                end
            end
        end
    end
    return best, false
end

local function aimbot_frame()
    if not base_ui:get("aim_enabled") then
        aimbot_state.locked = nil
        return
    end
    local key = base_ui:get_key("aim_key") or 0x02
    if not input.is_key_down(key) then
        aimbot_state.locked = nil
        return
    end
    local target, from_lock = get_closest_to_crosshair()
    if not target then
        if aimbot_state.locked and not is_lock_valid() then
            aimbot_state.locked = nil
        end
        return
    end
    if base_ui:get("aim_lock") and not from_lock then
        aimbot_state.locked = target
    end
    local lp = entity.get_local_player()
    local speed, gravity = get_weapon_info(lp)
    local origin = camera.get_position()
    local target_vel = target.velocity or Vector3.New(0, 0, 0)
    local head_pos = target.head_position
    if not head_pos then return end
    local predicted = predict_position(origin, head_pos, target_vel, speed, gravity)
    local sx, sy, on = draw.world_to_screen(predicted.x, predicted.y, predicted.z)
    if not on then return end
    local sw, sh = draw.get_screen_size()
    local cx, cy = sw * 0.5, sh * 0.5
    local speed_val = base_ui:get("aim_speed") or 0.65
    local factor = 1.0 - speed_val
    input.move_mouse((sx - cx) * factor, (sy - cy) * factor)
end

local function draw_fov_circle()
    if not base_ui:get("aim_enabled") or not base_ui:get("aim_fov_draw") then return end
    local sw, sh = draw.get_screen_size()
    local fov = base_ui:get("aim_fov") or 120
    draw.circle(sw*0.5, sh*0.5, fov, {1,1,1,0.35}, 64, 1.0)
end

local function draw_debug()
    if not base_ui:get("pred_debug") then return end
    if base_ui.state.open then return end

    local lp = entity.get_local_player()
    local speed, gravity, source, weapon_name = get_weapon_info(lp)
    local lock_status = "off"
    if base_ui:get("aim_lock") then
        if aimbot_state.locked then lock_status = "LOCKED: " .. aimbot_state.locked.name
        else lock_status = "searching" end
    end
    local hist_count = 0
    for _ in pairs(player_history) do hist_count = hist_count + 1 end
    draw.window(10, 10, "debug", "Weapon Info", {
        "Weapon: " .. tostring(weapon_name or "none"),
        "Speed: " .. tostring(floor(speed)) .. " (" .. tostring(source) .. ")",
        "Gravity: " .. tostring(gravity or get_workspace_gravity()) .. " x " .. tostring(base_ui:get("pred_gscale") or 0.55),
        "Corpses: " .. tostring(#corpse_cache),
        "History: " .. tostring(hist_count),
        "Lock: " .. lock_status,
        "FPS: " .. format("%.0f", utility.get_fps()),
    })
end

-- ═══════════════════════════════════════════════════════════════════════
-- ONFRAME
-- ═══════════════════════════════════════════════════════════════════════

on_frame = function()
    if base_ui.state.keys["menu_toggle_key"] then
        base_ui.state.toggle_key = base_ui.state.keys["menu_toggle_key"]
    end
    process_hotkey_listening(base_ui)
    process_toggle(base_ui)
    remember_players()
    scan_corpses()
    draw_corpse_esp()
    draw_esp()
    draw_fov_circle()
    draw_debug()
    aimbot_frame()
    draw_window(base_ui)
end

OnFrame = on_frame
onFrame = on_frame

if notify and notify.success then
    notify.success("AR V2", "Loaded")
else
    print("[AR V2] Loaded v" .. VERSION)
end

-- ═══════════════════════════════════════════════════════════════════════
-- CARREGA CONFIG
-- ═══════════════════════════════════════════════════════════════════════
pcall(load_config)
