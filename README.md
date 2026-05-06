-- Project Vector - Fallen | Roblox CoreGui Script
-- Inject via executor (e.g. Synapse X, KRNL, Fluxus)

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer

-- ============================================================
-- CORES / PALETA
-- ============================================================
local C = {
    BG_DARK       = Color3.fromRGB(10, 8, 18),
    BG_PANEL      = Color3.fromRGB(14, 10, 24),
    BG_NAV        = Color3.fromRGB(8, 6, 18),
    BG_ITEM       = Color3.fromRGB(22, 16, 38),
    BG_SECTION    = Color3.fromRGB(18, 13, 32),
    PINK          = Color3.fromRGB(224, 64, 251),
    PINK_DIM      = Color3.fromRGB(156, 39, 176),
    GREEN         = Color3.fromRGB(0, 230, 118),
    RED           = Color3.fromRGB(244, 67, 54),
    WHITE         = Color3.fromRGB(238, 238, 238),
    TEXT_PRI      = Color3.fromRGB(224, 216, 240),
    TEXT_SEC      = Color3.fromRGB(158, 143, 181),
    TEXT_DIM      = Color3.fromRGB(107, 93, 130),
    BORDER        = Color3.fromRGB(90, 50, 150),
    TRANSPARENT   = Color3.fromRGB(0, 0, 0),
}

-- ============================================================
-- ESTADO DO PAINEL
-- ============================================================
local State = {
    Visible = true,
    ActiveTab = "Visuals",
    Dragging = false,
    DragOffset = Vector2.new(),
    Toggles = {
        PlayerToggle   = true,
        PlayerName     = true,
        PlayerBox      = true,
        PlayerHealth   = true,
        PlayerHeldItem = true,
        PlayerDistance = true,
        PlayerSkeleton = true,
        PlayerChams    = true,
        PlayerViewDir  = true,
        PlayerVisible  = true,
        ModeratorList  = true,
        ClothingList   = true,
        KeybindList    = true,
    },
    Colors = {
        PlayerName     = C.WHITE,
        PlayerBox      = C.WHITE,
        PlayerHeldItem = C.GREEN,
        PlayerSkeleton = C.GREEN,
        PlayerViewDir  = C.RED,
        PlayerVisible  = C.WHITE,
    },
    BoxStyle   = "Normal Box",
    ChamsStyle = "Flat",
    RenderDist = 1500,
}

-- ============================================================
-- HELPERS
-- ============================================================
local function New(class, props, children)
    local obj = Instance.new(class)
    for k, v in pairs(props or {}) do
        if k ~= "Parent" then
            obj[k] = v
        end
    end
    for _, child in ipairs(children or {}) do
        child.Parent = obj
    end
    if props and props.Parent then
        obj.Parent = props.Parent
    end
    return obj
end

local function Tween(obj, props, t, style, dir)
    local info = TweenInfo.new(t or 0.18, style or Enum.EasingStyle.Quad, dir or Enum.EasingDirection.Out)
    TweenService:Create(obj, info, props):Play()
end

local function MakeDivider(parent, yPos)
    New("Frame", {
        Parent = parent,
        Size = UDim2.new(1, -16, 0, 1),
        Position = UDim2.new(0, 8, 0, yPos),
        BackgroundColor3 = C.BORDER,
        BackgroundTransparency = 0.6,
        BorderSizePixel = 0,
    })
end

-- ============================================================
-- ROOT GUI
-- ============================================================
-- Remove versão antiga se existir
pcall(function()
    if CoreGui:FindFirstChild("ProjectVector_Fallen") then
        CoreGui:FindFirstChild("ProjectVector_Fallen"):Destroy()
    end
end)

local ScreenGui = New("ScreenGui", {
    Name = "ProjectVector_Fallen",
    ResetOnSpawn = false,
    ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
    Parent = CoreGui,
})

-- ============================================================
-- JANELA PRINCIPAL
-- ============================================================
local MainFrame = New("Frame", {
    Name = "MainFrame",
    Parent = ScreenGui,
    Size = UDim2.new(0, 660, 0, 460),
    Position = UDim2.new(0.5, -330, 0.5, -230),
    BackgroundColor3 = C.BG_PANEL,
    BorderSizePixel = 0,
    ClipsDescendants = true,
})

New("UICorner", { CornerRadius = UDim.new(0, 7), Parent = MainFrame })

-- Borda roxa sutil
New("UIStroke", {
    Parent = MainFrame,
    Color = C.BORDER,
    Transparency = 0.55,
    Thickness = 1,
})

-- Sombra/glow (simulada com frame atrás)
local Shadow = New("ImageLabel", {
    Parent = ScreenGui,
    Size = UDim2.new(0, 720, 0, 520),
    Position = UDim2.new(0.5, -360, 0.5, -260),
    BackgroundTransparency = 1,
    Image = "rbxassetid://6014261993",
    ImageColor3 = C.PINK_DIM,
    ImageTransparency = 0.75,
    ScaleType = Enum.ScaleType.Slice,
    SliceCenter = Rect.new(49, 49, 450, 450),
    ZIndex = 0,
})

-- ============================================================
-- NAVBAR
-- ============================================================
local NavBar = New("Frame", {
    Name = "NavBar",
    Parent = MainFrame,
    Size = UDim2.new(1, 0, 0, 40),
    BackgroundColor3 = C.BG_NAV,
    BorderSizePixel = 0,
    ZIndex = 5,
})

New("UIStroke", {
    Parent = NavBar,
    Color = C.BORDER,
    Transparency = 0.5,
    Thickness = 1,
    ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
})

-- Logo
local Brand = New("TextLabel", {
    Parent = NavBar,
    Size = UDim2.new(0, 200, 1, 0),
    Position = UDim2.new(0, 12, 0, 0),
    BackgroundTransparency = 1,
    Text = "Project Vector – Fallen",
    TextColor3 = C.PINK,
    Font = Enum.Font.GothamBold,
    TextSize = 13,
    TextXAlignment = Enum.TextXAlignment.Left,
    ZIndex = 6,
})

-- Tabs
local TabsHolder = New("Frame", {
    Parent = NavBar,
    Size = UDim2.new(0, 380, 1, 0),
    Position = UDim2.new(1, -385, 0, 0),
    BackgroundTransparency = 1,
    ZIndex = 6,
})

New("UIListLayout", {
    Parent = TabsHolder,
    FillDirection = Enum.FillDirection.Horizontal,
    HorizontalAlignment = Enum.HorizontalAlignment.Right,
    VerticalAlignment = Enum.VerticalAlignment.Center,
    Padding = UDim.new(0, 2),
})

local TabDefs = {
    { Name = "Aimbot",         Icon = "⊕" },
    { Name = "Visuals",        Icon = "◉" },
    { Name = "Miscellaneous",  Icon = "✦" },
    { Name = "Settings",       Icon = "⚙" },
}

local TabButtons = {}

local function StyleTab(btn, active)
    if active then
        Tween(btn, { BackgroundColor3 = Color3.fromRGB(60, 18, 80), BackgroundTransparency = 0.0 })
        btn.TextColor3 = C.PINK
        if btn:FindFirstChildOfClass("UIStroke") then
            btn:FindFirstChildOfClass("UIStroke").Transparency = 0.3
        end
    else
        Tween(btn, { BackgroundColor3 = C.BG_NAV, BackgroundTransparency = 1.0 })
        btn.TextColor3 = C.TEXT_SEC
        if btn:FindFirstChildOfClass("UIStroke") then
            btn:FindFirstChildOfClass("UIStroke").Transparency = 1
        end
    end
end

for _, tabDef in ipairs(TabDefs) do
    local btn = New("TextButton", {
        Parent = TabsHolder,
        Size = UDim2.new(0, 88, 0, 28),
        BackgroundColor3 = C.BG_NAV,
        BackgroundTransparency = 1,
        Text = tabDef.Icon .. " " .. tabDef.Name,
        TextColor3 = C.TEXT_SEC,
        Font = Enum.Font.GothamMedium,
        TextSize = 11,
        BorderSizePixel = 0,
        ZIndex = 7,
    })
    New("UICorner", { CornerRadius = UDim.new(0, 4), Parent = btn })
    New("UIStroke", {
        Parent = btn,
        Color = C.PINK,
        Transparency = 1,
        Thickness = 1,
    })

    TabButtons[tabDef.Name] = btn

    btn.MouseEnter:Connect(function()
        if State.ActiveTab ~= tabDef.Name then
            Tween(btn, { TextColor3 = C.TEXT_PRI })
        end
    end)

    btn.MouseLeave:Connect(function()
        if State.ActiveTab ~= tabDef.Name then
            Tween(btn, { TextColor3 = C.TEXT_SEC })
        end
    end)

    btn.MouseButton1Click:Connect(function()
        State.ActiveTab = tabDef.Name
        for name, b in pairs(TabButtons) do
            StyleTab(b, name == tabDef.Name)
        end
    end)
end

StyleTab(TabButtons["Visuals"], true)

-- ============================================================
-- CONTENT AREA
-- ============================================================
local Content = New("Frame", {
    Name = "Content",
    Parent = MainFrame,
    Size = UDim2.new(1, 0, 1, -40),
    Position = UDim2.new(0, 0, 0, 40),
    BackgroundTransparency = 1,
})

-- Left Panel
local LeftPanel = New("ScrollingFrame", {
    Name = "LeftPanel",
    Parent = Content,
    Size = UDim2.new(0, 400, 1, 0),
    BackgroundColor3 = C.BG_PANEL,
    BackgroundTransparency = 0.02,
    BorderSizePixel = 0,
    ScrollBarThickness = 3,
    ScrollBarImageColor3 = C.PINK_DIM,
    CanvasSize = UDim2.new(0, 0, 0, 0),
    AutomaticCanvasSize = Enum.AutomaticSize.Y,
})

-- Divider vertical
New("Frame", {
    Parent = Content,
    Size = UDim2.new(0, 1, 1, 0),
    Position = UDim2.new(0, 400, 0, 0),
    BackgroundColor3 = C.BORDER,
    BackgroundTransparency = 0.4,
    BorderSizePixel = 0,
})

-- Right Panel
local RightPanel = New("ScrollingFrame", {
    Name = "RightPanel",
    Parent = Content,
    Size = UDim2.new(0, 259, 1, 0),
    Position = UDim2.new(0, 401, 0, 0),
    BackgroundColor3 = C.BG_PANEL,
    BackgroundTransparency = 0.02,
    BorderSizePixel = 0,
    ScrollBarThickness = 3,
    ScrollBarImageColor3 = C.PINK_DIM,
    CanvasSize = UDim2.new(0, 0, 0, 0),
    AutomaticCanvasSize = Enum.AutomaticSize.Y,
})

-- ============================================================
-- FUNÇÕES DE CONSTRUÇÃO DE COMPONENTES
-- ============================================================

local function SectionTitle(parent, text, yPos)
    local lbl = New("TextLabel", {
        Parent = parent,
        Size = UDim2.new(1, -16, 0, 18),
        Position = UDim2.new(0, 8, 0, yPos),
        BackgroundTransparency = 1,
        Text = text,
        TextColor3 = C.TEXT_SEC,
        Font = Enum.Font.GothamBold,
        TextSize = 10,
        TextXAlignment = Enum.TextXAlignment.Left,
    })
    MakeDivider(parent, yPos + 20)
    return lbl
end

-- Cor para hex string (para exibir)
local function ColorDot(parent, xPos, yPos, color, key)
    local dot = New("TextButton", {
        Parent = parent,
        Size = UDim2.new(0, 20, 0, 20),
        Position = UDim2.new(1, xPos, 0, yPos),
        BackgroundColor3 = color,
        BorderSizePixel = 0,
        Text = "",
        ZIndex = 4,
    })
    New("UICorner", { CornerRadius = UDim.new(0, 3), Parent = dot })
    New("UIStroke", {
        Parent = dot,
        Color = Color3.fromRGB(255, 255, 255),
        Transparency = 0.75,
        Thickness = 1,
    })

    -- Color picker simples ao clicar
    local colors = { C.PINK, C.GREEN, C.RED, C.WHITE,
        Color3.fromRGB(33, 150, 243), Color3.fromRGB(255, 152, 0),
        Color3.fromRGB(255, 235, 59), Color3.fromRGB(0, 188, 212) }

    local pickerOpen = false
    local pickerFrame

    dot.MouseButton1Click:Connect(function()
        if pickerOpen and pickerFrame then
            pickerFrame:Destroy()
            pickerOpen = false
            return
        end
        pickerOpen = true

        pickerFrame = New("Frame", {
            Parent = ScreenGui,
            Size = UDim2.new(0, 138, 0, 60),
            Position = UDim2.new(0, dot.AbsolutePosition.X - 60, 0, dot.AbsolutePosition.Y + 26),
            BackgroundColor3 = Color3.fromRGB(15, 10, 28),
            BorderSizePixel = 0,
            ZIndex = 100,
        })
        New("UICorner", { CornerRadius = UDim.new(0, 6), Parent = pickerFrame })
        New("UIStroke", { Parent = pickerFrame, Color = C.BORDER, Transparency = 0.3, Thickness = 1 })
        New("UIPadding", {
            Parent = pickerFrame,
            PaddingTop = UDim.new(0, 6), PaddingBottom = UDim.new(0, 6),
            PaddingLeft = UDim.new(0, 6), PaddingRight = UDim.new(0, 6),
        })

        local grid = New("Frame", { Parent = pickerFrame, Size = UDim2.new(1, 0, 1, 0), BackgroundTransparency = 1 })
        New("UIGridLayout", {
            Parent = grid,
            CellSize = UDim2.new(0, 22, 0, 22),
            CellPaddingUniform = UDim.new(0, 4),
        })

        for _, c in ipairs(colors) do
            local opt = New("TextButton", {
                Parent = grid,
                BackgroundColor3 = c,
                Text = "",
                BorderSizePixel = 0,
                ZIndex = 101,
            })
            New("UICorner", { CornerRadius = UDim.new(0, 4), Parent = opt })
            opt.MouseButton1Click:Connect(function()
                dot.BackgroundColor3 = c
                if key and State.Colors[key] ~= nil then
                    State.Colors[key] = c
                end
                pickerFrame:Destroy()
                pickerOpen = false
            end)
        end
    end)

    return dot
end

-- Toggle Row
local function ToggleRow(parent, yPos, label, key, color, colorKey, badge)
    local row = New("TextButton", {
        Parent = parent,
        Size = UDim2.new(1, -8, 0, 28),
        Position = UDim2.new(0, 4, 0, yPos),
        BackgroundColor3 = C.BG_ITEM,
        BackgroundTransparency = 1,
        BorderSizePixel = 0,
        Text = "",
        ZIndex = 3,
    })
    New("UICorner", { CornerRadius = UDim.new(0, 4), Parent = row })

    -- Checkbox
    local cb = New("Frame", {
        Parent = row,
        Size = UDim2.new(0, 14, 0, 14),
        Position = UDim2.new(0, 8, 0.5, -7),
        BackgroundColor3 = color and C.PINK or C.PINK_DIM,
        BackgroundTransparency = State.Toggles[key] and 0.6 or 0.85,
        BorderSizePixel = 0,
        ZIndex = 4,
    })
    New("UICorner", { CornerRadius = UDim.new(0, 3), Parent = cb })
    New("UIStroke", {
        Parent = cb,
        Color = C.PINK,
        Transparency = State.Toggles[key] and 0.2 or 0.5,
        Thickness = 1.5,
    })

    -- Checkmark
    local check = New("TextLabel", {
        Parent = cb,
        Size = UDim2.new(1, 0, 1, 0),
        BackgroundTransparency = 1,
        Text = "✓",
        TextColor3 = C.PINK,
        Font = Enum.Font.GothamBold,
        TextSize = 10,
        Visible = State.Toggles[key],
        ZIndex = 5,
    })

    -- Label
    New("TextLabel", {
        Parent = row,
        Size = UDim2.new(1, -80, 1, 0),
        Position = UDim2.new(0, 30, 0, 0),
        BackgroundTransparency = 1,
        Text = label,
        TextColor3 = C.TEXT_PRI,
        Font = Enum.Font.Gotham,
        TextSize = 11,
        TextXAlignment = Enum.TextXAlignment.Left,
        ZIndex = 4,
    })

    -- Badge VISULS
    if badge then
        New("TextLabel", {
            Parent = row,
            Size = UDim2.new(0, 52, 0, 18),
            Position = UDim2.new(1, -60, 0.5, -9),
            BackgroundColor3 = Color3.fromRGB(40, 12, 55),
            BackgroundTransparency = 0.0,
            Text = "VISULS",
            TextColor3 = C.PINK,
            Font = Enum.Font.GothamBold,
            TextSize = 9,
            BorderSizePixel = 0,
            ZIndex = 4,
        })
    end

    -- Color dot
    if color and colorKey then
        ColorDot(row, -28, 4, color, colorKey)
    end

    -- Toggle logic
    local function refresh()
        local on = State.Toggles[key]
        Tween(cb, { BackgroundTransparency = on and 0.6 or 0.85 })
        if cb:FindFirstChildOfClass("UIStroke") then
            Tween(cb:FindFirstChildOfClass("UIStroke"), { Transparency = on and 0.2 or 0.6 })
        end
        check.Visible = on
    end

    row.MouseEnter:Connect(function()
        Tween(row, { BackgroundTransparency = 0.75 })
    end)
    row.MouseLeave:Connect(function()
        Tween(row, { BackgroundTransparency = 1 })
    end)
    row.MouseButton1Click:Connect(function()
        State.Toggles[key] = not State.Toggles[key]
        refresh()
    end)

    return row
end

-- ============================================================
-- POPULA O PAINEL ESQUERDO
-- ============================================================
local leftY = 10

SectionTitle(LeftPanel, "Project Vector Settings", leftY)
leftY = leftY + 28

local leftItems = {
    { label = "Enable Player Toggle",       key = "PlayerToggle",   color = nil,      colorKey = nil,        badge = true  },
    { label = "Enable Player Name",         key = "PlayerName",     color = C.WHITE,  colorKey = "PlayerName",  badge = false },
    { label = "Enable Player Box",          key = "PlayerBox",      color = C.WHITE,  colorKey = "PlayerBox",   badge = false },
    { label = "Enable Player Health",       key = "PlayerHealth",   color = nil,      colorKey = nil,        badge = false },
    { label = "Enable Player Held Item",    key = "PlayerHeldItem", color = C.GREEN,  colorKey = "PlayerHeldItem", badge = false },
    { label = "Enable Player Distance",     key = "PlayerDistance", color = C.WHITE,  colorKey = nil,        badge = false },
    { label = "Enable Player Skeleton",     key = "PlayerSkeleton", color = C.GREEN,  colorKey = "PlayerSkeleton", badge = false },
    { label = "Enable Player Chams",        key = "PlayerChams",    color = C.WHITE,  colorKey = nil,        badge = false },
    { label = "Enable Player View Direction",key = "PlayerViewDir", color = C.RED,    colorKey = "PlayerViewDir",  badge = false },
    { label = "Enable Player Visible Check",key = "PlayerVisible",  color = C.WHITE,  colorKey = "PlayerVisible",  badge = false },
}

for _, item in ipairs(leftItems) do
    ToggleRow(LeftPanel, leftY, item.label, item.key, item.color, item.colorKey, item.badge)
    leftY = leftY + 30
end

-- Slider Player Render Distance
leftY = leftY + 6
New("TextLabel", {
    Parent = LeftPanel,
    Size = UDim2.new(1, -24, 0, 14),
    Position = UDim2.new(0, 12, 0, leftY),
    BackgroundTransparency = 1,
    Text = "Player Render Distance",
    TextColor3 = C.TEXT_SEC,
    Font = Enum.Font.Gotham,
    TextSize = 10,
    TextXAlignment = Enum.TextXAlignment.Left,
})

local sliderVal = New("TextLabel", {
    Parent = LeftPanel,
    Size = UDim2.new(0, 60, 0, 14),
    Position = UDim2.new(1, -72, 0, leftY),
    BackgroundTransparency = 1,
    Text = tostring(State.RenderDist),
    TextColor3 = C.PINK,
    Font = Enum.Font.GothamBold,
    TextSize = 10,
    TextXAlignment = Enum.TextXAlignment.Right,
})
leftY = leftY + 16

-- Track
local sliderBg = New("Frame", {
    Parent = LeftPanel,
    Size = UDim2.new(1, -24, 0, 4),
    Position = UDim2.new(0, 12, 0, leftY),
    BackgroundColor3 = Color3.fromRGB(50, 30, 80),
    BorderSizePixel = 0,
})
New("UICorner", { CornerRadius = UDim.new(1, 0), Parent = sliderBg })

local sliderFill = New("Frame", {
    Parent = sliderBg,
    Size = UDim2.new(0.75, 0, 1, 0),
    BackgroundColor3 = C.PINK,
    BorderSizePixel = 0,
})
New("UICorner", { CornerRadius = UDim.new(1, 0), Parent = sliderFill })

local sliderThumb = New("Frame", {
    Parent = sliderBg,
    Size = UDim2.new(0, 12, 0, 12),
    Position = UDim2.new(0.75, -6, 0.5, -6),
    BackgroundColor3 = C.PINK,
    BorderSizePixel = 0,
})
New("UICorner", { CornerRadius = UDim.new(1, 0), Parent = sliderThumb })

-- Slider interaction
local sliderDragging = false
sliderBg.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        sliderDragging = true
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        sliderDragging = false
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if sliderDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local abs = sliderBg.AbsolutePosition
        local sz = sliderBg.AbsoluteSize
        local pct = math.clamp((input.Position.X - abs.X) / sz.X, 0, 1)
        sliderFill.Size = UDim2.new(pct, 0, 1, 0)
        sliderThumb.Position = UDim2.new(pct, -6, 0.5, -6)
        State.RenderDist = math.floor(pct * 2000)
        sliderVal.Text = to
