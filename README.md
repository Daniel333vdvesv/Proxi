--[[
    LoospMod Panel v11 — Full PvP + Armas + Anti-Detecção + 👻 GGL
    ABAS: VISUAL | MIRA | PLAYER | ARMA | UTIL | ☢️ | 👻
    v10 completo + aba 👻 nova
--]]

-- ==================== CLEANUP GLOBAL ====================
if _G.LoospMod_Cleanup then pcall(_G.LoospMod_Cleanup) end

_G.LoospMod_Conexoes = {}
_G.LoospMod_Drawings = {}
_G.LoospMod_Instancias = {}

local function registrarConexao(conn) table.insert(_G.LoospMod_Conexoes, conn) return conn end
local function registrarDrawing(d) table.insert(_G.LoospMod_Drawings, d) return d end
local function registrarInstancia(i) table.insert(_G.LoospMod_Instancias, i) return i end

_G.LoospMod_Cleanup = function()
    for _, c in ipairs(_G.LoospMod_Conexoes or {}) do pcall(function() c:Disconnect() end) end
    _G.LoospMod_Conexoes = {}
    for _, d in ipairs(_G.LoospMod_Drawings or {}) do pcall(function() d:Remove() end) end
    _G.LoospMod_Drawings = {}
    for _, i in ipairs(_G.LoospMod_Instancias or {}) do pcall(function() i:Destroy() end) end
    _G.LoospMod_Instancias = {}
    for _, g in ipairs(CoreGui:GetChildren()) do
        if g.Name == "LoospModPanel" or g.Name == "LoospModVisual" then
            pcall(function() g:Destroy() end)
        end
    end
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local CoreGui = game:GetService("CoreGui")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualUser = game:GetService("VirtualUser")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

-- ==================== CONFIG ====================
local Config = {
    -- VISUAL
    ESP_Ativar = false, ESP_Box = true, ESP_Nome = true, ESP_Distancia_Show = true,
    ESP_Linha = false, ESP_HP = true, ESP_HeadDot = false, ESP_TimeCheck = true,
    ESP_MaxDist = 1500,
    Holograma_Ativar = false, Holograma_Cor = Color3.fromRGB(0, 150, 255), Chams_Ativar = false,
    FullBright = false, NoFog = false, FOV_Circle = false, FOV_Radius = 120,
    -- MIRA
    Aimbot_Ativar = false, Aimbot_Cabeca = true, Aimbot_Smooth = 0.35,
    Aimbot_FOV = 150, Aimbot_Visivel = true, Aimbot_Team = true,
    SilentAim = false, TriggerBot = false, NoRecoil = false, NoSpread = false, InstantHit = false,
    -- PLAYER
    Velocidade = 16, Pulo = 50, Invisivel = false, AntiRagdoll = false,
    AntiKick = true, AntiFling = false, InfJump = false, NoFall = false,
    Fly = false, FlySpeed = 50, Noclip = false,
    -- ARMA
    FastFire = false, InfiniteAmmo = false, FastReload = false,
    AutoFire = false, Wallbang = false, FakeLag = false,
    -- UTIL
    AutoRedeem = false, AntiAFK = false, PlayerList = false, NotifyHit = false,
    -- ☢️
    PanicKey_Ativar = true, DetectorAlert = false, AutoHide = false, AntiSpectate = false,
    StreamerMode = false, StealthMode = false, ClearDrawings = false,
    AutoDisconnect = false, HideOnAdmin = false, GhostMode = false,
    -- 👻 GGL
    GGL_Ativar = false, GGL_Duracao = 3, GGL_Alcance = 30, GGL_Metodo = 4,
    GGL_EfeitoVisual = true, GGL_Som = false, GGL_Highlight = true,
}

-- ==================== GUI ====================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "LoospModPanel"
ScreenGui.ResetOnSpawn = false
ScreenGui.IgnoreGuiInset = true
ScreenGui.Parent = CoreGui
registrarInstancia(ScreenGui)

local VisualGui = Instance.new("ScreenGui")
VisualGui.Name = "LoospModVisual"
VisualGui.ResetOnSpawn = false
VisualGui.IgnoreGuiInset = true
VisualGui.Parent = CoreGui
registrarInstancia(VisualGui)

-- ==================== PAINEL ====================
local Main = Instance.new("Frame")
Main.Size = UDim2.new(0, 560, 0, 340)
Main.Position = UDim2.new(0.5, -280, 0.5, -170)
Main.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Main.ClipsDescendants = true
Main.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 8)
MainCorner.Parent = Main

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(45, 45, 50)
MainStroke.Thickness = 1
MainStroke.Parent = Main

local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 32)
TopBar.BackgroundColor3 = Color3.fromRGB(12, 12, 14)
TopBar.BorderSizePixel = 0
TopBar.Parent = Main

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(0, 200, 1, 0)
Title.Position = UDim2.new(0, 12, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "LoospMod v11"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 14
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TopBar

local StatusDot = Instance.new("Frame")
StatusDot.Size = UDim2.new(0, 6, 0, 6)
StatusDot.Position = UDim2.new(0, 110, 0.5, -3)
StatusDot.BackgroundColor3 = Color3.fromRGB(0, 220, 100)
StatusDot.BorderSizePixel = 0
StatusDot.Parent = TopBar
local DotCorner = Instance.new("UICorner")
DotCorner.CornerRadius = UDim.new(1, 0)
DotCorner.Parent = StatusDot

local StatusText = Instance.new("TextLabel")
StatusText.Size = UDim2.new(0, 60, 1, 0)
StatusText.Position = UDim2.new(0, 120, 0, 0)
StatusText.BackgroundTransparency = 1
StatusText.Text = "ONLINE"
StatusText.TextColor3 = Color3.fromRGB(0, 220, 100)
StatusText.Font = Enum.Font.GothamBold
StatusText.TextSize = 10
StatusText.TextXAlignment = Enum.TextXAlignment.Left
StatusText.Parent = TopBar

local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 24, 0, 24)
MinBtn.Position = UDim2.new(1, -60, 0, 4)
MinBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
MinBtn.Text = "−"
MinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 14
MinBtn.Parent = TopBar
local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 4)
MinCorner.Parent = MinBtn

local CloseBtn = Instance.new("TextButton")
CloseBtn.Size = UDim2.new(0, 24, 0, 24)
CloseBtn.Position = UDim2.new(1, -32, 0, 4)
CloseBtn.BackgroundColor3 = Color3.fromRGB(220, 60, 60)
CloseBtn.Text = "×"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 14
CloseBtn.Parent = TopBar
local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 4)
CloseCorner.Parent = CloseBtn

local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 70, 1, -32)
Sidebar.Position = UDim2.new(0, 0, 0, 32)
Sidebar.BackgroundColor3 = Color3.fromRGB(12, 12, 14)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = Main

local SideList = Instance.new("UIListLayout")
SideList.Padding = UDim.new(0, 4)
SideList.HorizontalAlignment = Enum.HorizontalAlignment.Center
SideList.SortOrder = Enum.SortOrder.LayoutOrder
SideList.Parent = Sidebar

local SidePad = Instance.new("UIPadding")
SidePad.PaddingTop = UDim.new(0, 8)
SidePad.Parent = Sidebar

local function criarAba(nome, icone, ordem)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 60, 0, 44)
    btn.BackgroundColor3 = Color3.fromRGB(25, 25, 28)
    btn.Text = ""
    btn.LayoutOrder = ordem
    btn.Parent = Sidebar
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 6)
    c.Parent = btn

    local iconeLbl = Instance.new("TextLabel")
    iconeLbl.Size = UDim2.new(1, 0, 0, 20)
    iconeLbl.Position = UDim2.new(0, 0, 0, 4)
    iconeLbl.BackgroundTransparency = 1
    iconeLbl.Text = icone
    iconeLbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    iconeLbl.Font = Enum.Font.GothamBold
    iconeLbl.TextSize = 14
    iconeLbl.Parent = btn

    local nomeLbl = Instance.new("TextLabel")
    nomeLbl.Size = UDim2.new(1, 0, 0, 14)
    nomeLbl.Position = UDim2.new(0, 0, 0, 24)
    nomeLbl.BackgroundTransparency = 1
    nomeLbl.Text = nome
    nomeLbl.TextColor3 = Color3.fromRGB(200, 200, 200)
    nomeLbl.Font = Enum.Font.GothamBold
    nomeLbl.TextSize = 9
    nomeLbl.Parent = btn

    return btn, iconeLbl, nomeLbl
end

local tabVisual, icoVisual, nomVisual = criarAba("VISUAL", "◉", 1)
local tabMira, icoMira, nomMira = criarAba("MIRA", "⊕", 2)
local tabPlayer, icoPlayer, nomPlayer = criarAba("PLAYER", "☻", 3)
local tabArma, icoArma, nomArma = criarAba("ARMA", "⚔", 4)
local tabUtil, icoUtil, nomUtil = criarAba("UTIL", "⚙", 5)
local tabNuke, icoNuke, nomNuke = criarAba("☢️", "☢", 6)
local tabGGL, icoGGL, nomGGL = criarAba("👻", "👻", 7)

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -70, 1, -32)
Content.Position = UDim2.new(0, 70, 0, 32)
Content.BackgroundColor3 = Color3.fromRGB(22, 22, 25)
Content.BorderSizePixel = 0
Content.Parent = Main

local ContentPad = Instance.new("UIPadding")
ContentPad.PaddingTop = UDim.new(0, 10)
ContentPad.PaddingLeft = UDim.new(0, 12)
ContentPad.PaddingRight = UDim.new(0, 12)
ContentPad.Parent = Content

local ContentScroll = Instance.new("ScrollingFrame")
ContentScroll.Size = UDim2.new(1, 0, 1, 0)
ContentScroll.BackgroundTransparency = 1
ContentScroll.BorderSizePixel = 0
ContentScroll.ScrollBarThickness = 3
ContentScroll.ScrollBarImageColor3 = Color3.fromRGB(80, 80, 85)
ContentScroll.CanvasSize = UDim2.new(0, 0, 0, 0)
ContentScroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
ContentScroll.Parent = Content

local ContentList = Instance.new("UIListLayout")
ContentList.Padding = UDim.new(0, 6)
ContentList.SortOrder = Enum.SortOrder.LayoutOrder
ContentList.Parent = ContentScroll

-- ==================== COMPONENTES ====================
local function criarSecao(parent, texto)
    local s = Instance.new("TextLabel")
    s.Size = UDim2.new(1, 0, 0, 22)
    s.BackgroundTransparency = 1
    s.Text = texto
    s.TextColor3 = Color3.fromRGB(220, 50, 50)
    s.Font = Enum.Font.GothamBold
    s.TextSize = 11
    s.TextXAlignment = Enum.TextXAlignment.Left
    s.Parent = parent
end

local function criarToggle(parent, nome, configKey)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1, 0, 0, 28)
    row.BackgroundTransparency = 1
    row.Parent = parent

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -50, 1, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = nome
    lbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = row

    local swBg = Instance.new("Frame")
    swBg.Size = UDim2.new(0, 38, 0, 20)
    swBg.Position = UDim2.new(1, -42, 0.5, -10)
    swBg.BackgroundColor3 = Config[configKey] and Color3.fromRGB(220, 50, 50) or Color3.fromRGB(50, 50, 55)
    swBg.BorderSizePixel = 0
    swBg.Parent = row
    local swCorner = Instance.new("UICorner")
    swCorner.CornerRadius = UDim.new(1, 0)
    swCorner.Parent = swBg

    local swKnob = Instance.new("Frame")
    swKnob.Size = UDim2.new(0, 16, 0, 16)
    swKnob.Position = Config[configKey] and UDim2.new(1, -18, 0.5, -8) or UDim2.new(0, 2, 0.5, -8)
    swKnob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    swKnob.BorderSizePixel = 0
    swKnob.Parent = swBg
    local knCorner = Instance.new("UICorner")
    knCorner.CornerRadius = UDim.new(1, 0)
    knCorner.Parent = swKnob

    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 1, 0)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.Parent = row

    btn.MouseButton1Click:Connect(function()
        Config[configKey] = not Config[configKey]
        if Config[configKey] then
            swBg.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
            swKnob.Position = UDim2.new(1, -18, 0.5, -8)
        else
            swBg.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
            swKnob.Position = UDim2.new(0, 2, 0.5, -8)
        end
    end)
end

local function criarSlider(parent, nome, min, max, default, sufixo, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, 0, 0, 38)
    frame.BackgroundTransparency = 1
    frame.Parent = parent

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -50, 0, 16)
    lbl.BackgroundTransparency = 1
    lbl.Text = nome
    lbl.TextColor3 = Color3.fromRGB(220, 220, 220)
    lbl.Font = Enum.Font.Gotham
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = frame

    local valLbl = Instance.new("TextLabel")
    valLbl.Size = UDim2.new(0, 50, 0, 16)
    valLbl.Position = UDim2.new(1, -50, 0, 0)
    valLbl.BackgroundTransparency = 1
    valLbl.Text = tostring(default) .. (sufixo or "")
    valLbl.TextColor3 = Color3.fromRGB(220, 50, 50)
    valLbl.Font = Enum.Font.GothamBold
    valLbl.TextSize = 11
    valLbl.TextXAlignment = Enum.TextXAlignment.Right
    valLbl.Parent = frame

    local bar = Instance.new("Frame")
    bar.Size = UDim2.new(1, 0, 0, 4)
    bar.Position = UDim2.new(0, 0, 0, 26)
    bar.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
    bar.BorderSizePixel = 0
    bar.Parent = frame
    local barCorner = Instance.new("UICorner")
    barCorner.CornerRadius = UDim.new(1, 0)
    barCorner.Parent = bar

    local fill = Instance.new("Frame")
    fill.Size = UDim2.new((default - min) / (max - min), 0, 1, 0)
    fill.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
    fill.BorderSizePixel = 0
    fill.Parent = bar
    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(1, 0)
    fillCorner.Parent = fill

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 12, 0, 12)
    knob.Position = UDim2.new(fill.Size.X.Scale, -6, 0.5, -6)
    knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    knob.BorderSizePixel = 0
    knob.Parent = bar
    local knCorner = Instance.new("UICorner")
    knCorner.CornerRadius = UDim.new(1, 0)
    knCorner.Parent = knob

    local dragging = false
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 20)
    btn.Position = UDim2.new(0, 0, 0, 18)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.Parent = frame

    local function update(input)
        local pos = math.clamp((input.Position.X - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
        local valor = math.floor(min + (max - min) * pos)
        fill.Size = UDim2.new(pos, 0, 1, 0)
        knob.Position = UDim2.new(pos, -6, 0.5, -6)
        valLbl.Text = tostring(valor) .. (sufixo or "")
        if callback then pcall(callback, valor) end
    end

    registrarConexao(btn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            update(input)
        end
    end))
    registrarConexao(UserInputService.InputChanged:Connect(function(input)
        if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            update(input)
        end
    end))
    registrarConexao(UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = false
        end
    end))
end

local function criarBotao(parent, nome, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 30)
    btn.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
    btn.Text = nome
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 12
    btn.Parent = parent
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 6)
    c.Parent = btn
    btn.MouseButton1Click:Connect(function() pcall(callback) end)
end

local function criarConteudoAba()
    local f = Instance.new("Frame")
    f.Size = UDim2.new(1, 0, 0, 0)
    f.BackgroundTransparency = 1
    f.AutomaticSize = Enum.AutomaticSize.Y
    f.Visible = false
    f.Parent = ContentScroll
    local list = Instance.new("UIListLayout")
    list.Padding = UDim.new(0, 6)
    list.SortOrder = Enum.SortOrder.LayoutOrder
    list.Parent = f
    return f
end

local frameVisual = criarConteudoAba()
local frameMira = criarConteudoAba()
local framePlayer = criarConteudoAba()
local frameArma = criarConteudoAba()
local frameUtil = criarConteudoAba()
local frameNuke = criarConteudoAba()
local frameGGL = criarConteudoAba()

-- VISUAL
criarSecao(frameVisual, "ESP")
criarToggle(frameVisual, "Ativar ESP", "ESP_Ativar")
criarToggle(frameVisual, "Box", "ESP_Box")
criarToggle(frameVisual, "Nome", "ESP_Nome")
criarToggle(frameVisual, "Distância", "ESP_Distancia_Show")
criarToggle(frameVisual, "Linha (Snapline)", "ESP_Linha")
criarToggle(frameVisual, "HP Bar", "ESP_HP")
criarToggle(frameVisual, "Ponto na Cabeça", "ESP_HeadDot")
criarToggle(frameVisual, "Time Check (só inimigos)", "ESP_TimeCheck")
criarSlider(frameVisual, "Distância Máx", 100, 3000, 1500, "m", function(v) Config.ESP_MaxDist = v end)
criarSecao(frameVisual, "HOLOGRAMA / CHAMS")
criarToggle(frameVisual, "Holograma Azul", "Holograma_Ativar")
criarToggle(frameVisual, "Chams (cor sólida)", "Chams_Ativar")
criarSecao(frameVisual, "AMBIENTE")
criarToggle(frameVisual, "Full Bright", "FullBright")
criarToggle(frameVisual, "No Fog", "NoFog")
criarToggle(frameVisual, "Círculo FOV", "FOV_Circle")
criarSlider(frameVisual, "Raio FOV", 30, 400, 120, "px", function(v) Config.FOV_Radius = v end)

-- MIRA
criarSecao(frameMira, "AIMBOT")
criarToggle(frameMira, "Ativar Aimbot", "Aimbot_Ativar")
criarToggle(frameMira, "Só Cabeça", "Aimbot_Cabeca")
criarToggle(frameMira, "Visível (linha de visão)", "Aimbot_Visivel")
criarToggle(frameMira, "Time Check", "Aimbot_Team")
criarSlider(frameMira, "Smooth", 1, 100, 35, "%", function(v) Config.Aimbot_Smooth = v / 100 end)
criarSlider(frameMira, "FOV", 30, 500, 150, "px", function(v) Config.Aimbot_FOV = v end)
criarSecao(frameMira, "EXTRAS")
criarToggle(frameMira, "Silent Aim", "SilentAim")
criarToggle(frameMira, "Trigger Bot", "TriggerBot")
criarToggle(frameMira, "No Recoil", "NoRecoil")
criarToggle(frameMira, "No Spread", "NoSpread")
criarToggle(frameMira, "Instant Hit", "InstantHit")

-- PLAYER
criarSecao(framePlayer, "MOVIMENTO")
criarSlider(framePlayer, "Velocidade", 16, 300, 16, "", function(v) Config.Velocidade = v end)
criarSlider(framePlayer, "Pulo", 50, 300, 50, "", function(v) Config.Pulo = v end)
criarToggle(framePlayer, "Pulo Infinito", "InfJump")
criarToggle(framePlayer, "Fly", "Fly")
criarSlider(framePlayer, "Fly Speed", 10, 300, 50, "", function(v) Config.FlySpeed = v end)
criarToggle(framePlayer, "Noclip", "Noclip")
criarSecao(framePlayer, "PROTEÇÃO")
criarToggle(framePlayer, "Invisível", "Invisivel")
criarToggle(framePlayer, "Anti-Ragdoll", "AntiRagdoll")
criarToggle(framePlayer, "Anti-Kick", "AntiKick")
criarToggle(framePlayer, "Anti-Fling", "AntiFling")
criarToggle(framePlayer, "No Fall Damage", "NoFall")

-- ARMA
criarSecao(frameArma, "COMBATE")
criarToggle(frameArma, "Fast Fire", "FastFire")
criarToggle(frameArma, "Munição Infinita", "InfiniteAmmo")
criarToggle(frameArma, "Reload Rápido", "FastReload")
criarToggle(frameArma, "Auto Fire", "AutoFire")
criarToggle(frameArma, "Wallbang", "Wallbang")
criarToggle(frameArma, "Fake Lag", "FakeLag")
criarBotao(frameArma, "Aplicar Config Arma", function()
    print("[Arma] Config aplicada — valores modificados em ReplicatedStorage.Weapons")
end)

-- UTIL
criarSecao(frameUtil, "UTILIDADE")
criarToggle(frameUtil, "Auto Redeem", "AutoRedeem")
criarToggle(frameUtil, "Anti-AFK", "AntiAFK")
criarToggle(frameUtil, "Player List", "PlayerList")
criarToggle(frameUtil, "Notify Hit", "NotifyHit")
criarBotao(frameUtil, "Resgatar Códigos (1x)", function()
    local remote = ReplicatedStorage:FindFirstChild("RedeemCode", true)
    if remote and remote:IsA("RemoteEvent") then
        local codigos = {"SKIBIDI_CASH_2026", "OHIO_DEFENSE_WALL", "FANUM_TAX_RETURN", "SIGMA_GRINDSET", "RIZZLER_SPEED", "TACOBOOST"}
        for _, c in ipairs(codigos) do remote:FireServer(c) task.wait(0.2) end
        print("[Redeem] Enviados")
    else
        print("[Redeem] Remote não encontrado")
    end
end)

-- ☢️ NUKE / ANTI-DETECÇÃO
criarSecao(frameNuke, "☢ ANTI-DETECÇÃO")
criarToggle(frameNuke, "Panic Key (RightControl)", "PanicKey_Ativar")
criarToggle(frameNuke, "Detector Alert", "DetectorAlert")
criarToggle(frameNuke, "Auto Hide (esconde ao suspeitar)", "AutoHide")
criarToggle(frameNuke, "Anti-Spectate", "AntiSpectate")
criarToggle(frameNuke, "Streamer Mode (borra nomes)", "StreamerMode")
criarToggle(frameNuke, "Stealth Mode (ESP lento)", "StealthMode")
criarToggle(frameNuke, "Clear Drawings (auto)", "ClearDrawings")
criarToggle(frameNuke, "Auto Disconnect (ao detectar)", "AutoDisconnect")
criarToggle(frameNuke, "Hide on Admin Join", "HideOnAdmin")
criarToggle(frameNuke, "Ghost Mode (sem rastro)", "GhostMode")

criarSecao(frameNuke, "AÇÕES RÁPIDAS")
criarBotao(frameNuke, "🛑 PANIC (desliga tudo agora)", function()
    Config.ESP_Ativar = false
    Config.Aimbot_Ativar = false
    Config.Holograma_Ativar = false
    Config.Chams_Ativar = false
    Config.Fly = false
    Config.Noclip = false
    Config.AutoFire = false
    Config.InfiniteAmmo = false
    Config.FastFire = false
    Main.Visible = false
    FloatBtn.Visible = false
    print("[PANIC] Todos os mods desligados + painel escondido")
end)
criarBotao(frameNuke, "🧹 Limpar Drawings (ESP)", function()
    for _, d in ipairs(_G.LoospMod_Drawings) do pcall(function() d.Visible = false end) end
    print("[Clean] Drawings escondidos")
end)
criarBotao(frameNuke, "🔍 Scan Admins no Server", function()
    local encontrados = 0
    for _, p in ipairs(Players:GetPlayers()) do
        local ok, rank = pcall(function() return p:GetRankInGroup(game.CreatorId) end)
        if ok and rank and rank >= 100 then
            encontrados = encontrados + 1
            print("[ADMIN] " .. p.Name .. " (rank " .. rank .. ")")
        end
        local ok2, badges = pcall(function() return p:GetBadges() end)
        if ok2 and badges then
            for _, badge in ipairs(badges) do
                if badge.Name:lower():find("admin") or badge.Name:lower():find("mod") then
                    encontrados = encontrados + 1
                    print("[ADMIN] " .. p.Name .. " (badge: " .. badge.Name .. ")")
                end
            end
        end
    end
    print("[Scan] " .. encontrados .. " admins encontrados")
end)
criarBotao(frameNuke, "💀 Auto Disconnect", function()
    print("[DC] Desconectando...")
    pcall(function() game:Shutdown() end)
end)

-- 👻 GGL FREEZE
criarSecao(frameGGL, "👻 GGL FREEZE")
criarToggle(frameGGL, "Ativar GGL Freeze", "GGL_Ativar")
criarSlider(frameGGL, "Duração (segundos)", 1, 10, 3, "s", function(v) Config.GGL_Duracao = v end)
criarSlider(frameGGL, "Alcance (metros)", 5, 100, 30, "m", function(v) Config.GGL_Alcance = v end)

criarSecao(frameGGL, "🎯 MÉTODO DE CONGELAMENTO")
criarBotao(frameGGL, "Método 1: Anchor HRP", function()
    Config.GGL_Metodo = 1
    print("[GGL] Método: Anchor HRP")
end)
criarBotao(frameGGL, "Método 2: Loop de Posição", function()
    Config.GGL_Metodo = 2
    print("[GGL] Método: Loop de Posição")
end)
criarBotao(frameGGL, "Método 3: WalkSpeed Zero", function()
    Config.GGL_Metodo = 3
    print("[GGL] Método: WalkSpeed Zero")
end)
criarBotao(frameGGL, "Método 4: TODOS (recomendado)", function()
    Config.GGL_Metodo = 4
    print("[GGL] Método: TODOS os 3 juntos")
end)

criarSecao(frameGGL, "❄️ EXTRAS")
criarToggle(frameGGL, "Efeito Visual (gelo)", "GGL_EfeitoVisual")
criarToggle(frameGGL, "Som de Congelamento", "GGL_Som")
criarToggle(frameGGL, "Highlight Azul no Alvo", "GGL_Highlight")

criarSecao(frameGGL, "ℹ️ COMO USAR")
local infoGGL = Instance.new("TextLabel")
infoGGL.Size = UDim2.new(1, 0, 0, 70)
infoGGL.BackgroundTransparency = 1
infoGGL.Text = "1. Ativa o GGL Freeze\n2. Aparece o botão 🧊 flutuante\n3. Chega perto do inimigo\n4. Aperta 🧊 → congela o mais próximo\n5. Duração configurada acima"
infoGGL.TextColor3 = Color3.fromRGB(180, 180, 180)
infoGGL.Font = Enum.Font.Gotham
infoGGL.TextSize = 11
infoGGL.TextXAlignment = Enum.TextXAlignment.Left
infoGGL.TextYAlignment = Enum.TextYAlignment.Top
infoGGL.TextWrapped = true
infoGGL.Parent = frameGGL

-- ==================== TROCA DE ABA ====================
local abasInfo = {
    {nome = "VISUAL", frame = frameVisual, btn = tabVisual, ico = icoVisual, nom = nomVisual},
    {nome = "MIRA", frame = frameMira, btn = tabMira, ico = icoMira, nom = nomMira},
    {nome = "PLAYER", frame = framePlayer, btn = tabPlayer, ico = icoPlayer, nom = nomPlayer},
    {nome = "ARMA", frame = frameArma, btn = tabArma, ico = icoArma, nom = nomArma},
    {nome = "UTIL", frame = frameUtil, btn = tabUtil, ico = icoUtil, nom = nomUtil},
    {nome = "☢️", frame = frameNuke, btn = tabNuke, ico = icoNuke, nom = nomNuke},
    {nome = "👻", frame = frameGGL, btn = tabGGL, ico = icoGGL, nom = nomGGL},
}

local function selecionarAba(nomeSel)
    for _, info in ipairs(abasInfo) do
        if info.nome == nomeSel then
            info.btn.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
            info.ico.TextColor3 = Color3.fromRGB(255, 255, 255)
            info.nom.TextColor3 = Color3.fromRGB(255, 255, 255)
            info.frame.Visible = true
        else
            info.btn.BackgroundColor3 = Color3.fromRGB(25, 25, 28)
            info.ico.TextColor3 = Color3.fromRGB(200, 200, 200)
            info.nom.TextColor3 = Color3.fromRGB(200, 200, 200)
            info.frame.Visible = false
        end
    end
end

for _, info in ipairs(abasInfo) do
    info.btn.MouseButton1Click:Connect(function() selecionarAba(info.nome) end)
end
selecionarAba("VISUAL")

-- ==================== BOTÕES FLUTUANTES ====================
local FloatBtn = Instance.new("TextButton")
FloatBtn.Size = UDim2.new(0, 50, 0, 50)
FloatBtn.Position = UDim2.new(0, 30, 0, 120)
FloatBtn.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
FloatBtn.Text = "L"
FloatBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
FloatBtn.Font = Enum.Font.GothamBold
FloatBtn.TextSize = 22
FloatBtn.Active = true
FloatBtn.Draggable = true
FloatBtn.Visible = false
FloatBtn.Parent = ScreenGui
local FloatCorner = Instance.new("UICorner")
FloatCorner.CornerRadius = UDim.new(1, 0)
FloatCorner.Parent = FloatBtn

local FreezeBtn = Instance.new("TextButton")
FreezeBtn.Size = UDim2.new(0, 60, 0, 60)
FreezeBtn.Position = UDim2.new(0, 30, 0, 200)
FreezeBtn.BackgroundColor3 = Color3.fromRGB(100, 200, 255)
FreezeBtn.Text = "🧊"
FreezeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
FreezeBtn.Font = Enum.Font.GothamBlack
FreezeBtn.TextSize = 30
FreezeBtn.Active = true
FreezeBtn.Draggable = true
FreezeBtn.Visible = false
FreezeBtn.BorderSizePixel = 0
FreezeBtn.Parent = ScreenGui
registrarInstancia(FreezeBtn)

local FreezeCorner = Instance.new("UICorner")
FreezeCorner.CornerRadius = UDim.new(1, 0)
FreezeCorner.Parent = FreezeBtn

local FreezeGlow = Instance.new("UIStroke")
FreezeGlow.Color = Color3.fromRGB(150, 220, 255)
FreezeGlow.Thickness = 3
FreezeGlow.Transparency = 0.3
FreezeGlow.Parent = FreezeBtn

CloseBtn.MouseButton1Click:Connect(function() Main.Visible = false FloatBtn.Visible = true end)
MinBtn.MouseButton1Click:Connect(function() Main.Visible = false FloatBtn.Visible = true end)
FloatBtn.MouseButton1Click:Connect(function() Main.Visible = true FloatBtn.Visible = false end)

registrarConexao(UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.RightShift then
        Main.Visible = not Main.Visible
        FloatBtn.Visible = not Main.Visible
    end
    if input.KeyCode == Enum.KeyCode.RightControl and Config.PanicKey_Ativar then
        Config.ESP_Ativar = false
        Config.Aimbot_Ativar = false
        Config.Holograma_Ativar = false
        Config.Chams_Ativar = false
        Config.Fly = false
        Config.Noclip = false
        Config.AutoFire = false
        Config.InfiniteAmmo = false
        Config.FastFire = false
        Main.Visible = false
        FloatBtn.Visible = false
        print("[PANIC] Mods desligados via tecla")
    end
end))

-- ==================== FOV CIRCLE ====================
local fovCircle
task.spawn(function()
    pcall(function()
        fovCircle = Drawing.new("Circle")
        registrarDrawing(fovCircle)
        fovCircle.Thickness = 1
        fovCircle.Color = Color3.fromRGB(220, 50, 50)
        fovCircle.Transparency = 0.7
        fovCircle.Filled = false
        fovCircle.Visible = false
    end)
    registrarConexao(RunService.RenderStepped:Connect(function()
        if fovCircle then
            fovCircle.Visible = Config.FOV_Circle
            if Config.FOV_Circle then
                local vp = Camera.ViewportSize
                fovCircle.Position = Vector2.new(vp.X / 2, vp.Y / 2)
                fovCircle.Radius = Config.FOV_Radius
            end
        end
    end))
end)

-- ==================== ESP ====================
local espCache = {}

local function criarESP(p)
    if p == LocalPlayer or espCache[p] then return end
    local box = Drawing.new("Square")
    registrarDrawing(box)
    box.Thickness = 1; box.Filled = false
    box.Color = Color3.fromRGB(0, 200, 255); box.Transparency = 1; box.Visible = false
    local name = Drawing.new("Text")
    registrarDrawing(name)
    name.Size = 14; name.Center = true; name.Outline = true
    name.Color = Color3.fromRGB(255, 255, 255); name.Transparency = 1; name.Visible = false
    local dist = Drawing.new("Text")
    registrarDrawing(dist)
    dist.Size = 12; dist.Center = true; dist.Outline = true
    dist.Color = Color3.fromRGB(255, 220, 100); dist.Transparency = 1; dist.Visible = false
    local line = Drawing.new("Line")
    registrarDrawing(line)
    line.Thickness = 1.5; line.Color = Color3.fromRGB(0, 200, 255)
    line.Transparency = 1; line.Visible = false
    local headDot = Drawing.new("Circle")
    registrarDrawing(headDot)
    headDot.Thickness = 1; headDot.Filled = true; headDot.Radius = 4
    headDot.Color = Color3.fromRGB(255, 50, 50); headDot.Transparency = 1; headDot.Visible = false
    local hpBar = Drawing.new("Square")
    registrarDrawing(hpBar)
    hpBar.Thickness = 1; hpBar.Filled = true
    hpBar.Color = Color3.fromRGB(0, 220, 100); hpBar.Transparency = 1; hpBar.Visible = false
    espCache[p] = { Box = box, Name = name, Dist = dist, Line = line, Head = headDot, HP = hpBar }
end

local function removerESP(p)
    if espCache[p] then
        for _, v in pairs(espCache[p]) do pcall(function() v:Remove() end) end
        espCache[p] = nil
    end
end

registrarConexao(Players.PlayerAdded:Connect(criarESP))
registrarConexao(Players.PlayerRemoving:Connect(removerESP))
for _, p in ipairs(Players:GetPlayers()) do criarESP(p) end

local ultimoUpdate = 0
registrarConexao(RunService.RenderStepped:Connect(function()
    local agora = tick()
    local intervalo = Config.StealthMode and 0.066 or 0
    if agora - ultimoUpdate < intervalo then return end
    ultimoUpdate = agora

    local vp = Camera.ViewportSize
    for player, esp in pairs(espCache) do
        local char = player.Character
        local vis = false
        if Config.ESP_Ativar and char and not Config.AutoHide then
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hrp and hum and hum.Health > 0 then
                local mesmoTime = Config.ESP_TimeCheck 
                    and player.Team ~= nil 
                    and LocalPlayer.Team ~= nil 
                    and player.Team == LocalPlayer.Team
                if not mesmoTime then
                    local pos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
                    local dv = (Camera.CFrame.Position - hrp.Position).Magnitude
                    if onScreen and pos.Z > 0 and dv <= Config.ESP_MaxDist then
                        vis = true
                        local s = 1 / pos.Z * 100
                        local w, h = 50 * s, 80 * s

                        esp.Box.Size = Vector2.new(w, h)
                        esp.Box.Position = Vector2.new(pos.X - w/2, pos.Y - h/2)
                        esp.Box.Visible = Config.ESP_Box

                        esp.Name.Position = Vector2.new(pos.X, pos.Y - h/2 - 20)
                        if Config.StreamerMode then
                            esp.Name.Text = string.rep("*", #player.Name)
                        else
                            esp.Name.Text = player.Name
                        end
                        esp.Name.Visible = Config.ESP_Nome

                        esp.Dist.Position = Vector2.new(pos.X, pos.Y + h/2 + 4)
                        esp.Dist.Text = string.format("%d m", math.floor(dv))
                        esp.Dist.Visible = Config.ESP_Distancia_Show

                        esp.Line.From = Vector2.new(vp.X / 2, vp.Y)
                        esp.Line.To = Vector2.new(pos.X, pos.Y + h/2)
                        esp.Line.Visible = Config.ESP_Linha

                        local head = char:FindFirstChild("Head")
                        if head then
                            local hp, hon = Camera:WorldToViewportPoint(head.Position)
                            if hon and hp.Z > 0 then
                                esp.Head.Position = Vector2.new(hp.X, hp.Y)
                                esp.Head.Visible = Config.ESP_HeadDot
                            else
                                esp.Head.Visible = false
                            end
                        else
                            esp.Head.Visible = false
                        end

                        if Config.ESP_HP then
                            local hpp = hum.Health / hum.MaxHealth
                            esp.HP.Size = Vector2.new(3, h * hpp)
                            esp.HP.Position = Vector2.new(pos.X - w/2 - 7, pos.Y - h/2 + h * (1 - hpp))
                            esp.HP.Color = Color3.fromRGB(
                                math.floor(255 * (1 - hpp)),
                                math.floor(255 * hpp), 0)
                            esp.HP.Visible = true
                        else
                            esp.HP.Visible = false
                        end
                    end
                end
            end
        end
        if not vis then
            esp.Box.Visible = false
            esp.Name.Visible = false
            esp.Dist.Visible = false
            esp.Line.Visible = false
            esp.Head.Visible = false
            esp.HP.Visible = false
        end
    end
end))

-- ==================== HOLOGRAMA / CHAMS ====================
local hologramaCache = {}

task.spawn(function()
    pcall(function()
        local function aplicar(p)
            if p == LocalPlayer then return end
            if hologramaCache[p] then
                pcall(function() hologramaCache[p]:Destroy() end)
                hologramaCache[p] = nil
            end
            if not (Config.Holograma_Ativar or Config.Chams_Ativar) then return end
            local char = p.Character
            if not char then return end
            local hl = Instance.new("Highlight")
            hl.Name = "LoospModHL"
            if Config.Chams_Ativar then
                hl.FillColor = Color3.fromRGB(0, 150, 255)
                hl.FillTransparency = 0
                hl.OutlineTransparency = 1
            else
                hl.FillColor = Config.Holograma_Cor
                hl.FillTransparency = 0.7
                hl.OutlineColor = Color3.fromRGB(0, 220, 255)
                hl.OutlineTransparency = 0
            end
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.Parent = char
            hologramaCache[p] = hl
        end
        registrarConexao(Players.PlayerAdded:Connect(function(p)
            p.CharacterAdded:Connect(function() task.wait(0.5) aplicar(p) end)
        end))
        registrarConexao(Players.PlayerRemoving:Connect(function(p)
            if hologramaCache[p] then
                pcall(function() hologramaCache[p]:Destroy() end)
                hologramaCache[p] = nil
            end
        end))
        registrarConexao(RunService.Heartbeat:Connect(function()
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LocalPlayer then
                    if Config.Holograma_Ativar or Config.Chams_Ativar then
                        if not hologramaCache[p] or not hologramaCache[p].Parent then
                            aplicar(p)
                        end
                    else
                        if hologramaCache[p] then
                            pcall(function() hologramaCache[p]:Destroy() end)
                            hologramaCache[p] = nil
                        end
                    end
                end
            end
        end))
    end)
end)

-- ==================== FULL BRIGHT / NO FOG ====================
task.spawn(function()
    pcall(function()
        local origAmbient = Lighting.Ambient
        local origOutdoor = Lighting.OutdoorAmbient
        local origBrightness = Lighting.Brightness
        local origFogEnd = Lighting.FogEnd
        registrarConexao(RunService.Heartbeat:Connect(function()
            if Config.FullBright then
                Lighting.Ambient = Color3.fromRGB(200, 200, 200)
                Lighting.OutdoorAmbient = Color3.fromRGB(200, 200, 200)
                Lighting.Brightness = 3
            else
                Lighting.Ambient = origAmbient
                Lighting.OutdoorAmbient = origOutdoor
                Lighting.Brightness = origBrightness
            end
            if Config.NoFog then
                Lighting.FogEnd = 100000
            else
                Lighting.FogEnd = origFogEnd
            end
        end))
    end)
end)

-- ==================== AIMBOT ====================
task.spawn(function()
    pcall(function()
        local function temVisao(part)
            local params = RaycastParams.new()
            params.FilterType = Enum.RaycastFilterType.Exclude
            params.FilterDescendantsInstances = {LocalPlayer.Character}
            local origem = Camera.CFrame.Position
            local dir = (part.Position - origem)
            local ray = Workspace:Raycast(origem, dir, params)
            if not ray then return true end
            return ray.Instance:IsDescendantOf(part.Parent)
        end

        local function getClosest()
            local closest, dist = nil, math.huge
            local vp = Camera.ViewportSize
            local centro = Vector2.new(vp.X / 2, vp.Y / 2)
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LocalPlayer then
                    local mesmoTime = Config.Aimbot_Team 
                        and p.Team ~= nil 
                        and LocalPlayer.Team ~= nil 
                        and p.Team == LocalPlayer.Team
                    if not mesmoTime then
                        local char = p.Character
                        if char then
                            local part = Config.Aimbot_Cabeca and char:FindFirstChild("Head") or char:FindFirstChild("HumanoidRootPart")
                            local hum = char:FindFirstChildOfClass("Humanoid")
                            if part and hum and hum.Health > 0 then
                                local pos, onScreen = Camera:WorldToViewportPoint(part.Position)
                                if onScreen and pos.Z > 0 then
                                    local d = (Vector2.new(pos.X, pos.Y) - centro).Magnitude
                                    if d < Config.Aimbot_FOV and d < dist then
                                        if not Config.Aimbot_Visivel or temVisao(part) then
                                            closest = part
                                            dist = d
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
            return closest
        end

        registrarConexao(RunService.RenderStepped:Connect(function()
            if not Config.Aimbot_Ativar then return end
            local target = getClosest()
            if target then
                local currentCF = Camera.CFrame
                local wantedCF = CFrame.new(currentCF.Position, target.Position)
                Camera.CFrame = currentCF:Lerp(wantedCF, Config.Aimbot_Smooth)
            end
        end))
    end)
end)

-- ==================== ANTI-KICK ====================
task.spawn(function()
    pcall(function()
        local mt = getrawmetatable(game)
        local old = mt.__namecall
        setreadonly(mt, false)
        mt.__namecall = newcclosure(function(self, ...)
            local m = getnamecallmethod()
            if Config.AntiKick and m == "Kick" and self == LocalPlayer then return end
            return old(self, ...)
        end)
        setreadonly(mt, true)
    end)
end)

-- ==================== ANTI-RAGDOLL ====================
task.spawn(function()
    pcall(function()
        local function aplicar(char)
            local hum = char:WaitForChild("Humanoid", 5)
            if not hum then return end
            pcall(function()
                hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
                hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
                hum:SetStateEnabled(Enum.HumanoidStateType.Physics, false)
            end)
        end
        LocalPlayer.CharacterAdded:Connect(aplicar)
        if LocalPlayer.Character then aplicar(LocalPlayer.Character) end
        registrarConexao(RunService.Heartbeat:Connect(function()
            if not Config.AntiRagdoll then return end
            local char = LocalPlayer.Character
            if char then
                local hum = char:FindFirstChildOfClass("Humanoid")
                if hum and hum.PlatformStand then
                    pcall(function() hum.PlatformStand = false end)
                end
            end
        end))
    end)
end)

-- ==================== VELOCIDADE / PULO ====================
registrarConexao(RunService.Heartbeat:Connect(function()
    local char = LocalPlayer.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            if hum.WalkSpeed ~= Config.Velocidade then
                pcall(function() hum.WalkSpeed = Config.Velocidade end)
            end
            if hum.UseJumpPower and hum.JumpPower ~= Config.Pulo then
                pcall(function() hum.JumpPower = Config.Pulo end)
            end
        end
    end
end))

-- ==================== INF JUMP ====================
registrarConexao(UserInputService.JumpRequest:Connect(function()
    if Config.InfJump then
        local char = LocalPlayer.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then pcall(function() hum:ChangeState(Enum.HumanoidStateType.Jumping) end) end
        end
    end
end))

-- ==================== FLY ====================
task.spawn(function()
    pcall(function()
        local flyAtivo = false
        local function ativarFly()
            local char = LocalPlayer.Character
            if not char then return end
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not hrp or not hum then return end
            hum.PlatformStand = true
            hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            flyAtivo = true
        end
        local function desativarFly()
            flyAtivo = false
            local char = LocalPlayer.Character
            if not char then return end
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then hum.PlatformStand = false end
        end
        registrarConexao(RunService.Heartbeat:Connect(function(dt)
            if not Config.Fly then
                if flyAtivo then desativarFly() end
                return
            end
            local char = LocalPlayer.Character
            if not char then return end
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not hrp or not hum then return end
            if not flyAtivo then ativarFly() end
            local dir = Vector3.new(0, 0, 0)
            local moveDir = hum.MoveDirection
            if moveDir.Magnitude > 0 then dir = dir + moveDir.Unit end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then dir = dir + Vector3.new(0, 1, 0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then dir = dir - Vector3.new(0, 1, 0) end
            if dir.Magnitude > 0 then
                hrp.AssemblyLinearVelocity = dir.Unit * Config.FlySpeed
            else
                hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            end
        end))
        LocalPlayer.CharacterAdded:Connect(function() flyAtivo = false end)
    end)
end)

-- ==================== NOCLIP ====================
registrarConexao(RunService.Stepped:Connect(function()
    if not Config.Noclip then return end
    local char = LocalPlayer.Character
    if char then
        for _, p in ipairs(char:GetDescendants()) do
            if p:IsA("BasePart") and p.CanCollide then
                p.CanCollide = false
            end
        end
    end
end))

-- ==================== INVISÍVEL ====================
task.spawn(function()
    pcall(function()
        local function aplicar(char)
            if not char then return end
            for _, p in ipairs(char:GetDescendants()) do
                if p:IsA("BasePart") then
                    p.LocalTransparencyModifier = Config.Invisivel and 1 or 0
                elseif p:IsA("Decal") or p:IsA("Texture") then
                    p.Transparency = Config.Invisivel and 1 or 0
                end
            end
        end
        LocalPlayer.CharacterAdded:Connect(function(c) task.wait(0.5) aplicar(c) end)
        if LocalPlayer.Character then task.wait(0.5) aplicar(LocalPlayer.Character) end
        registrarConexao(RunService.Heartbeat:Connect(function()
            if LocalPlayer.Character then aplicar(LocalPlayer.Character) end
        end))
    end)
end)

-- ==================== ARMA ====================
local WeaponsFolder = ReplicatedStorage:FindFirstChild("Weapons")
local ArmaOriginais = {}

local function guardarOriginais()
    if not WeaponsFolder then return end
    for _, arma in ipairs(WeaponsFolder:GetChildren()) do
        if not ArmaOriginais[arma] then
            ArmaOriginais[arma] = {}
            for _, v in ipairs(arma:GetDescendants()) do
                if v:IsA("IntValue") or v:IsA("NumberValue") or v:IsA("BoolValue") then
                    ArmaOriginais[arma][v] = v.Value
                end
            end
        end
    end
end

local function restaurarArma(arma)
    if not ArmaOriginais[arma] then return end
    for valor, original in pairs(ArmaOriginais[arma]) do
        pcall(function() valor.Value = original end)
    end
end

local function aplicarModsArma()
    if not WeaponsFolder then return end
    for _, arma in ipairs(WeaponsFolder:GetChildren()) do
        for _, v in ipairs(arma:GetDescendants()) do
            if Config.InfiniteAmmo then
                if v:IsA("IntValue") and v.Name == "StoredAmmo" then
                    pcall(function() v.Value = 9999 end)
                end
                if v:IsA("IntValue") and v.Name == "ClipSize" then
                    pcall(function() v.Value = 9999 end)
                end
            end
            if Config.FastFire then
                if v:IsA("IntValue") and v.Name == "FireRate" then
                    pcall(function() v.Value = 500 end)
                end
            end
            if Config.FastReload then
                if v:IsA("NumberValue") and v.Name == "ReloadTime" then
                    pcall(function() v.Value = 0.1 end)
                end
            end
            if Config.NoRecoil then
                if v:IsA("NumberValue") and v.Name == "CameraRecoil" then
                    pcall(function() v.Value = 0 end)
                end
            end
            if Config.NoSpread then
                if v:IsA("NumberValue") and v.Name == "Accuracy" then
                    pcall(function() v.Value = 0 end)
                end
            end
            if Config.AutoFire then
                if v:IsA("BoolValue") and v.Name == "Automatic" then
                    pcall(function() v.Value = true end)
                end
            end
            if Config.InstantHit then
                if v:IsA("IntValue") and v.Name == "Damage" then
                    pcall(function() v.Value = 999 end)
                end
            end
        end
    end
end

task.spawn(function()
    guardarOriginais()
    while task.wait(0.5) do
        local algumLigado = Config.InfiniteAmmo or Config.FastFire or Config.FastReload
            or Config.NoRecoil or Config.NoSpread or Config.AutoFire or Config.InstantHit
        if algumLigado then
            pcall(aplicarModsArma)
        else
            if WeaponsFolder then
                for arma, _ in pairs(ArmaOriginais) do
                    pcall(restaurarArma, arma)
                end
            end
        end
    end
end)

task.spawn(function()
    pcall(function()
        registrarConexao(RunService.RenderStepped:Connect(function()
            if not Config.AutoFire then return end
            local vp = Camera.ViewportSize
            local ray = Camera:ViewportPointToRay(vp.X / 2, vp.Y / 2)
            local params = RaycastParams.new()
            params.FilterType = Enum.RaycastFilterType.Exclude
            params.FilterDescendantsInstances = {LocalPlayer.Character}
            local hit = Workspace:Raycast(ray.Origin, ray.Direction * 500, params)
            if hit and hit.Instance then
                local model = hit.Instance:FindFirstAncestorOfClass("Model")
                if model then
                    local plr = Players:GetPlayerFromCharacter(model)
                    if plr and plr ~= LocalPlayer then
                        if not (Config.Aimbot_Team and plr.Team == LocalPlayer.Team) then
                            pcall(function()
                                VirtualUser:ClickButton1(Vector2.new(vp.X / 2, vp.Y / 2))
                            end)
                        end
                    end
                end
            end
        end))
    end)
end)

-- ==================== AUTO REDEEM ====================
task.spawn(function()
    pcall(function()
        local tentado = false
        while task.wait(2) do
            if not Config.AutoRedeem or tentado then continue end
            tentado = true
            local remote = ReplicatedStorage:FindFirstChild("RedeemCode", true)
            if remote and remote:IsA("RemoteEvent") then
                for _, c in ipairs({"SKIBIDI_CASH_2026", "OHIO_DEFENSE_WALL", "FANUM_TAX_RETURN"}) do
                    remote:FireServer(c)
                    task.wait(0.3)
                end
            end
        end
    end)
end)

-- ==================== ANTI-AFK ====================
registrarConexao(LocalPlayer.Idled:Connect(function()
    if Config.AntiAFK then
        VirtualUser:CaptureController()
        VirtualUser:ClickButton2(Vector2.new())
    end
end))

-- ==================== PLAYER LIST ====================
task.spawn(function()
    pcall(function()
        local lastCount = 0
        while task.wait(1) do
            if Config.PlayerList then
                local count = #Players:GetPlayers()
                if count ~= lastCount then
                    lastCount = count
                    print("[PlayerList] " .. count .. " jogadores no servidor")
                end
            end
        end
    end)
end)

-- ==================== ☢ ANTI-DETECÇÃO ====================
task.spawn(function()
    pcall(function()
        local function checarSuspeito()
            for _, p in ipairs(Players:GetPlayers()) do
                if p ~= LocalPlayer then
                    local ok, rank = pcall(function() return p:GetRankInGroup(game.CreatorId) end)
                    if ok and rank and rank >= 100 then return p, "Rank " .. rank end
                    local ok2, badges = pcall(function() return p:GetBadges() end)
                    if ok2 and badges then
                        for _, b in ipairs(badges) do
                            local n = b.Name:lower()
                            if n:find("admin") or n:find("mod") or n:find("owner") then
                                return p, "Badge: " .. b.Name
                            end
                        end
                    end
                    local n = (p.Name or ""):lower()
                    if n:find("admin") or n:find("owner") then
                        return p, "Nome suspeito"
                    end
                end
            end
            return nil
        end

        local jaAvisou = {}
        while task.wait(2) do
            if Config.DetectorAlert or Config.AutoHide or Config.HideOnAdmin or Config.AutoDisconnect then
                local suspeito, motivo = checarSuspeito()
                if suspeito and not jaAvisou[suspeito] then
                    jaAvisou[suspeito] = true
                    print("[☢ ALERT] Suspeito: " .. suspeito.Name .. " (" .. motivo .. ")")
                    
                    if Config.DetectorAlert then
                        local sg = Instance.new("ScreenGui")
                        sg.Name = "LoospModAlert"
                        sg.Parent = CoreGui
                        local frame = Instance.new("Frame")
                        frame.Size = UDim2.new(0, 300, 0, 50)
                        frame.Position = UDim2.new(0.5, -150, 0, 100)
                        frame.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
                        frame.BorderSizePixel = 0
                        frame.Parent = sg
                        local corner = Instance.new("UICorner")
                        corner.CornerRadius = UDim.new(0, 8)
                        corner.Parent = frame
                        local lbl = Instance.new("TextLabel")
                        lbl.Size = UDim2.new(1, -10, 1, -10)
                        lbl.Position = UDim2.new(0, 5, 0, 5)
                        lbl.BackgroundTransparency = 1
                        lbl.Text = "☢ SUSPEITO: " .. suspeito.Name
                        lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
                        lbl.Font = Enum.Font.GothamBold
                        lbl.TextSize = 14
                        lbl.Parent = frame
                        task.delay(5, function() pcall(function() sg:Destroy() end) end)
                    end

                    if Config.AutoHide or Config.HideOnAdmin then
                        Config.ESP_Ativar = false
                        Config.Aimbot_Ativar = false
                        Config.Holograma_Ativar = false
                        Config.Chams_Ativar = false
                        Main.Visible = false
                        FloatBtn.Visible = false
                    end

                    if Config.AutoDisconnect then
                        task.wait(0.5)
                        pcall(function() game:Shutdown() end)
                        pcall(function() LocalPlayer:Kick("Auto DC") end)
                    end
                end
            end
        end
    end)
end)

-- Anti-Spectate
task.spawn(function()
    pcall(function()
        while task.wait(1) do
            if Config.AntiSpectate then
                for _, p in ipairs(Players:GetPlayers()) do
                    if p ~= LocalPlayer and p.Character then
                        local cam = Workspace.CurrentCamera
                        if cam and cam.CameraSubject == LocalPlayer.Character then
                            local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
                            if hum and cam.CameraSubject ~= hum then
                                print("[☢ ANTI-SPECTATE] Possível spectate de: " .. p.Name)
                            end
                        end
                    end
                end
            end
        end
    end)
end)

-- Clear Drawings
task.spawn(function()
    pcall(function()
        while task.wait(0.5) do
            if Config.ClearDrawings then
                if not Config.ESP_Ativar then
                    for _, esp in pairs(espCache) do
                        for _, v in pairs(esp) do
                            pcall(function() v.Visible = false end)
                        end
                    end
                end
            end
        end
    end)
end)

-- ==================== 👻 GGL FREEZE ====================
local congelados = {}

local function acharInimigoMaisProximo()
    local char = LocalPlayer.Character
    if not char then return nil end
    local meuHRP = char:FindFirstChild("HumanoidRootPart")
    if not meuHRP then return nil end
    
    local maisProximo, menorDist = nil, math.huge
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local c = p.Character
            if c then
                local hum = c:FindFirstChildOfClass("Humanoid")
                local hrp = c:FindFirstChild("HumanoidRootPart")
                if hum and hrp and hum.Health > 0 then
                    local d = (hrp.Position - meuHRP.Position).Magnitude
                    if d <= Config.GGL_Alcance and d < menorDist then
                        maisProximo = p
                        menorDist = d
                    end
                end
            end
        end
    end
    return maisProximo, menorDist
end

local function congelar(player)
    if not player then return end
    local char = player.Character
    if not char then return end
    
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hrp or not hum then return end
    
    local estado = {
        hrp = hrp, hum = hum,
        CFrameOriginal = hrp.CFrame,
        WalkSpeedOriginal = hum.WalkSpeed,
        JumpPowerOriginal = hum.JumpPower,
        AnchoredOriginal = hrp.Anchored,
        CanCollideOriginal = hrp.CanCollide,
    }
    
    if Config.GGL_Metodo == 1 or Config.GGL_Metodo == 4 then
        pcall(function() hrp.Anchored = true end)
    end
    
    if Config.GGL_Metodo == 2 or Config.GGL_Metodo == 4 then
        task.spawn(function()
            local posOriginal = hrp.CFrame
            local fim = tick() + Config.GGL_Duracao
            while tick() < fim do
                pcall(function()
                    if hrp and hrp.Parent then hrp.CFrame = posOriginal end
                end)
                task.wait(0.03)
            end
        end)
    end
    
    if Config.GGL_Metodo == 3 or Config.GGL_Metodo == 4 then
        pcall(function()
            hum.WalkSpeed = 0
            hum.JumpPower = 0
        end)
    end
    
    if Config.GGL_EfeitoVisual then
        pcall(function()
            local effect = Instance.new("Part")
            effect.Size = Vector3.new(4, 6, 4)
            effect.CFrame = hrp.CFrame
            effect.Anchored = true
            effect.CanCollide = false
            effect.Transparency = 0.5
            effect.Color = Color3.fromRGB(150, 220, 255)
            effect.Material = Enum.Material.Ice
            effect.Parent = Workspace
            effect.Name = "GGL_FreezeEffect"
            task.delay(Config.GGL_Duracao, function() pcall(function() effect:Destroy() end) end)
        end)
    end
    
    if Config.GGL_Highlight then
        pcall(function()
            local hl = Instance.new("Highlight")
            hl.FillColor = Color3.fromRGB(100, 200, 255)
            hl.OutlineColor = Color3.fromRGB(200, 240, 255)
            hl.FillTransparency = 0.5
            hl.Parent = char
            hl.Name = "GGL_FreezeHL"
            task.delay(Config.GGL_Duracao, function() pcall(function() hl:Destroy() end) end)
        end)
    end
    
    if Config.GGL_Som then
        pcall(function()
            local s = Instance.new("Sound")
            s.SoundId = "rbxassetid://9125402323"
            s.Volume = 0.5
            s.Parent = hrp
            s:Play()
            task.delay(Config.GGL_Duracao, function() pcall(function() s:Destroy() end) end)
        end)
    end
    
    congelados[player] = estado
    
    task.delay(Config.GGL_Duracao, function()
        if congelados[player] then
            local est = congelados[player]
            pcall(function()
                if est.hrp and est.hrp.Parent then
                    est.hrp.Anchored = est.AnchoredOriginal
                    est.hrp.CanCollide = est.CanCollideOriginal
                end
                if est.hum and est.hum.Parent then
                    est.hum.WalkSpeed = est.WalkSpeedOriginal
                    est.hum.JumpPower = est.JumpPowerOriginal
                end
            end)
            congelados[player] = nil
            print("[GGL] Descongelou: " .. player.Name)
        end
    end)
    
    print("[GGL] ❄️ Congelado: " .. player.Name .. " por " .. Config.GGL_Duracao .. "s")
end

FreezeBtn.MouseButton1Click:Connect(function()
    if not Config.GGL_Ativar then return end
    
    local alvo, dist = acharInimigoMaisProximo()
    if alvo then
        congelar(alvo)
        FreezeBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
        task.delay(0.3, function() FreezeBtn.BackgroundColor3 = Color3.fromRGB(100, 200, 255) end)
    else
        print("[GGL] Nenhum inimigo em " .. Config.GGL_Alcance .. "m")
        FreezeBtn.BackgroundColor3 = Color3.fromRGB(200, 100, 100)
        task.delay(0.3, function() FreezeBtn.BackgroundColor3 = Color3.fromRGB(100, 200, 255) end)
    end
end)

task.spawn(function()
    while task.wait(0.5) do
        FreezeBtn.Visible = Config.GGL_Ativar
    end
end)

print("[LoospMod Panel v11] Carregado — aba 👻 GGL ativa")
print("   RightShift = Abrir/Fechar | RightControl = PANIC")
print("   GGL: ativa na aba 👻 e aperta o botão 🧊")
