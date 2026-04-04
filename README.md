# Mossfield UI LIBRARY

---

## Loading

```lua
local Mossfield = loadstring(game:HttpGet("YOUR_RAW_URL_HERE"))()
```

---

## Creating a Window

```lua
local Window = Mossfield:CreateWindow({
    Title     = "My Script",                 -- shown in the title bar
    Size      = UDim2.new(0, 580, 0, 400),   -- optional, this is the default
    ToggleKey = Enum.KeyCode.RightAlt,        -- changeable in UI Settings tab
    Icon      = nil,                          -- rbxassetid://... for mobile circle icon
    IconLabel = "MS",                         -- text shown in mobile circle if no icon
})
```

### Mobile toggle
Pressing **X** collapses the UI into a small circle in the corner. Tapping the circle reopens it.

### PC toggle
Pressing **X** (or the toggle key(RightAlt as default)) shrinks the window to its center and hides it. Same key brings it back.

---

## Tabs

```lua
local Tab = Window:AddTab({ Name = "Aimbot" })
```

Tabs appear in the left sidebar. The first tab added is selected automatically.

---

## Sub-Tabs (mini tabs inside a tab)

```lua
local ESPTab = Window:AddTab({ Name = "ESP" })

local SubTabs = ESPTab:AddSubTabBar()
local Visuals = SubTabs:Add("Visuals")   -- returns a component API
local Config  = SubTabs:Add("Config")    -- returns a component API

Visuals:AddToggle({ Name = "Boxes",   Default = false })
Visuals:AddToggle({ Name = "Tracers", Default = false })
Config:AddButton({ Name = "Reset Config", Callback = function() end })
```

Sub-tabs render as a small pill bar at the top of the tab's content area — like `Visuals | Config`.

---

## Components

All components can be added to a **Tab** or a **Sub-Tab**.

---

### Label
```lua
Tab:AddLabel({
    Text  = "This is some info text",
    Color = Color3.fromRGB(160, 158, 190),  -- optional
})
```

---

### Separator
```lua
Tab:AddSeparator()                       -- plain line
Tab:AddSeparator({ Text = "Settings" })  -- line with a label
```

---

### Button
```lua
Tab:AddButton({
    Name       = "Teleport to Spawn",
    ButtonText = "Run",          -- optional, default "Run"
    Callback   = function()
        -- your code here
    end,
})
```

---

### Toggle
```lua
local myToggle = Tab:AddToggle({
    Name     = "Enable Aimbot",
    Default  = false,
    Callback = function(state)
        print("Aimbot:", state)
    end,
})

-- Programmatic control
myToggle:SetValue(true)
print(myToggle:GetValue())
```

---

### Slider
```lua
local mySlider = Tab:AddSlider({
    Name     = "FOV",
    Min      = 10,
    Max      = 360,
    Default  = 90,
    Suffix   = "°",     -- optional unit label
    Callback = function(value)
        print("FOV:", value)
    end,
})

mySlider:SetValue(120)
print(mySlider:GetValue())
```

---

### TextBox
```lua
local myBox = Tab:AddTextBox({
    Name         = "Player Name",
    Placeholder  = "Enter name...",
    Default      = "",
    ClearOnFocus = true,   -- optional, default true
    Callback     = function(text)
        print("Entered:", text)
    end,
})

myBox:SetValue("Roblox")
print(myBox:GetValue())
```

---

### Dropdown
```lua
local myDrop = Tab:AddDropdown({
    Name     = "Team",
    Options  = { "Red", "Blue", "Green" },
    Default  = "Red",
    Callback = function(selected)
        print("Selected:", selected)
    end,
})

myDrop:SetValue("Blue")
myDrop:SetOptions({ "Alpha", "Beta", "Delta" })  -- replace the option list
print(myDrop:GetValue())
```

---

### Keybind
```lua
local myKey = Tab:AddKeybind({
    Name     = "Toggle ESP",
    Default  = Enum.KeyCode.E,
    Callback = function(key)
        print("New key:", key.Name)
    end,
})

print(myKey:GetValue())  -- returns an Enum.KeyCode
myKey:SetValue(Enum.KeyCode.F)
```

> Click the keybind button in-game, then press any key to rebind it.

---

### Color Picker
```lua
local myColor = Tab:AddColorPicker({
    Name     = "ESP Color",
    Default  = Color3.fromRGB(128, 75, 225),
    Callback = function(color)
        print("Color:", color)
    end,
})

myColor:SetValue(Color3.fromRGB(255, 80, 80))
print(myColor:GetValue())  -- returns a Color3
```

---

## Notifications

```lua
Window:Notify({
    Title       = "Script Loaded",
    Description = "Everything is ready.",
    Type        = "success",   -- "info" | "success" | "warning" | "error"
    Duration    = 4,           -- seconds, default 4
})
```

---

## Config Save & Load

Saves and loads a JSON file using `writefile` / `readfile` (executor must support these).

```lua
-- Store a value in the config
Window:SetConfigValue("fov", 120)
Window:SetConfigValue("esp", true)

-- Save to file  (creates "Mossfield_default.json")
Window:SaveConfig("default")

-- Load from file
local data = Window:LoadConfig("default")
if data then
    print(data.fov)
end

-- Read a single value
print(Window:GetConfigValue("fov"))
```

> The built-in **UI Settings** tab also has Save / Load buttons for convenience.

---

## UI Settings Tab

Mossfield automatically adds a **UI Settings** tab at the bottom of the sidebar. It includes:

- **Toggle key rebind** — change the key that shows/hides the UI (default: RightAlt)
- **Save Config** / **Load Config** buttons

---

## Full Example

```lua
local Mossfield = loadstring(game:HttpGet("YOUR_RAW_URL_HERE"))()

local Window = Mossfield:CreateWindow({
    Title     = "My Script",
    ToggleKey = Enum.KeyCode.RightAlt,
})

-- ── Aimbot tab ──────────────────────────────────────────────────────────
local Aimbot = Window:AddTab({ Name = "Aimbot" })

Aimbot:AddToggle({
    Name     = "Enable",
    Default  = false,
    Callback = function(v) print("Aimbot:", v) end,
})

Aimbot:AddSlider({
    Name     = "FOV",
    Min      = 10,
    Max      = 360,
    Default  = 90,
    Suffix   = "°",
    Callback = function(v) print("FOV:", v) end,
})

Aimbot:AddKeybind({
    Name     = "Aim Key",
    Default  = Enum.KeyCode.E,
})

-- ── ESP tab with sub-tabs ───────────────────────────────────────────────
local ESP = Window:AddTab({ Name = "ESP" })

local Sub     = ESP:AddSubTabBar()
local Visuals = Sub:Add("Visuals")
local Config  = Sub:Add("Config")

Visuals:AddToggle({ Name = "Boxes",    Default = false })
Visuals:AddToggle({ Name = "Tracers",  Default = false })
Visuals:AddToggle({ Name = "Names",    Default = true  })

Visuals:AddColorPicker({
    Name    = "Box Color",
    Default = Color3.fromRGB(128, 75, 225),
})

Config:AddButton({
    Name     = "Save ESP Config",
    Callback = function() Window:SaveConfig("esp") end,
})

-- ── Misc tab ────────────────────────────────────────────────────────────
local Misc = Window:AddTab({ Name = "Misc" })

Misc:AddSeparator({ Text = "Player" })

Misc:AddSlider({
    Name    = "Walk Speed",
    Min     = 16,
    Max     = 200,
    Default = 16,
    Suffix  = " ws",
    Callback = function(v)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = v
    end,
})

Misc:AddTextBox({
    Name        = "Tag Prefix",
    Placeholder = "[MS]",
})

-- notify on load
Window:Notify({
    Title       = "Loaded!",
    Description = "My Script v1.0 is ready.",
    Type        = "success",
    Duration    = 5,
})
```

---

## Notes

- Mossfield tries to parent to `CoreGui` first, then falls back to `PlayerGui` if it can't.
- Config save/load requires `writefile` and `readfile` 
- The **UI Settings** tab is always the last tab in the sidebar.

