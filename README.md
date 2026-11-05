
--[[
    GOJO MM2 HUB V2.0
    Interface mobile
    Desenvolvido em Lua para Roblox Studio
]]

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")

local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

local Themes = {
    Preto = Color3.fromRGB(20,20,25),
    Branco = Color3.fromRGB(235,235,245),
    Azul = Color3.fromRGB(30,100,255),
    Vermelho = Color3.fromRGB(220,40,50),
    Rosa = Color3.fromRGB(255,100,180),
    Amarelo = Color3.fromRGB(255,200,40)
}

local Theme = Themes.Azul
local ESPEnabled = false
local BoostEnabled = false

local GUI = Instance.new("ScreenGui")
GUI.Name = "GojoMM2Hub"
GUI.ResetOnSpawn = false
GUI.Parent = PlayerGui

local Main = Instance.new("Frame")
Main.Size = UDim2.fromOffset(330,400)
Main.Position = UDim2.new(0.5,-165,0.5,-200)
Main.BackgroundColor3 = Color3.fromRGB(18,18,27)
Main.Visible = false
Main.Parent = GUI

Instance.new("UICorner",Main).CornerRadius =
    UDim.new(0,15)

local Border = Instance.new("UIStroke")
Border.Color = Theme
Border.Thickness = 2
Border.Parent = Main

local Toggle = Instance.new("TextButton")
Toggle.Size = UDim2.fromOffset(55,55)
Toggle.Position = UDim2.new(0,15,0.4,0)
Toggle.Text = "月"
Toggle.TextSize = 28
Toggle.Font = Enum.Font.GothamBold
Toggle.TextColor3 = Color3.new(1,1,1)
Toggle.BackgroundColor3 = Theme
Toggle.Parent = GUI

Instance.new("UICorner",Toggle).CornerRadius =
    UDim.new(1,0)

Toggle.Activated:Connect(function()
    Main.Visible = not Main.Visible
end)

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1,0,0,55)
Title.BackgroundTransparency = 1
Title.Text = "🌙 GOJO MM2 HUB"
Title.TextColor3 = Theme
Title.TextSize = 22
Title.Font = Enum.Font.GothamBold
Title.Parent = Main

local Tabs = Instance.new("Frame")
Tabs.Size = UDim2.new(1,-10,0,40)
Tabs.Position = UDim2.fromOffset(5,58)
Tabs.BackgroundTransparency = 1
Tabs.Parent = Main

local TabLayout = Instance.new("UIListLayout")
TabLayout.FillDirection = Enum.FillDirection.Horizontal
TabLayout.Padding = UDim.new(0,4)
TabLayout.Parent = Tabs

local Content = Instance.new("ScrollingFrame")
Content.Size = UDim2.new(1,-20,1,-110)
Content.Position = UDim2.fromOffset(10,105)
Content.BackgroundTransparency = 1
Content.BorderSizePixel = 0
Content.ScrollBarThickness = 3
Content.CanvasSize = UDim2.new(0,0,0,0)
Content.Parent = Main

local Layout = Instance.new("UIListLayout")
Layout.Padding = UDim.new(0,7)
Layout.Parent = Content

Layout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    Content.CanvasSize = UDim2.new(
        0,0,0,Layout.AbsoluteContentSize.Y + 10
    )
end)

local function Clear()
    for _,v in ipairs(Content:GetChildren()) do
        if v:IsA("TextButton") or v:IsA("TextLabel") then
            v:Destroy()
        end
    end
end

local function Button(text,callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1,-5,0,42)
    b.BackgroundColor3 = Color3.fromRGB(35,35,50)
    b.TextColor3 = Color3.new(1,1,1)
    b.Text = text
    b.Font = Enum.Font.GothamBold
    b.TextSize = 14
    b.Parent = Content

    Instance.new("UICorner",b).CornerRadius =
        UDim.new(0,8)

    b.Activated:Connect(callback)
    return b
end

local function Label(text)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1,-5,0,32)
    l.BackgroundTransparency = 1
    l.Text = text
    l.TextColor3 = Theme
    l.Font = Enum.Font.GothamBold
    l.TextSize = 15
    l.Parent = Content
end

-- VISUAL
local function Visual()
    Clear()
    Label("VISUAL")

    Button("ESP visual: OFF",function()
        ESPEnabled = not ESPEnabled
        print("ESP visual:",ESPEnabled)
    end)

    Button("Mostrar nomes dos jogadores",function()
        for _,target in ipairs(Players:GetPlayers()) do
            if target ~= Player then
                print(target.Name)
            end
        end
    end)
end

-- PERFORMANCE
local function Performance()
    Clear()
    Label("DESEMPENHO")

    Button("FPS BOOST: ON",function()
        BoostEnabled = not BoostEnabled
        Lighting.GlobalShadows = not BoostEnabled
        Lighting.FogEnd = 100000

        for _,obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") then
                obj.CastShadow = not BoostEnabled
            end
        end

        print("FPS Boost:",BoostEnabled)
    end)

    Button("Gráfico LEGO",function()
        for _,obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") then
                obj.Material = Enum.Material.SmoothPlastic
            end
        end
    end)
end

-- THEMES
local function ThemePage()
    Clear()
    Label("ESCOLHA SEU TEMA")

    for name,color in pairs(Themes) do
        Button(name,function()
            Theme = color
            Border.Color = Theme
            Toggle.BackgroundColor3 = Theme
            Title.TextColor3 = Theme
            print("Tema:",name)
        end)
    end
end

-- SETTINGS
local function Settings()
    Clear()
    Label("CONFIGURAÇÕES")

    Button("Fechar interface",function()
        Main.Visible = false
    end)

    Button("Restaurar iluminação",function()
        Lighting.GlobalShadows = true
        Lighting.FogEnd = 100000
        Lighting.Brightness = 2
    end)
end

local function CreateTab(name,callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.fromOffset(76,35)
    b.BackgroundColor3 = Theme
    b.TextColor3 = Color3.new(1,1,1)
    b.Text = name
    b.Font = Enum.Font.GothamBold
    b.TextSize = 11
    b.Parent = Tabs

    Instance.new("UICorner",b).CornerRadius =
        UDim.new(0,7)

    b.Activated:Connect(callback)
end

CreateTab("Visual",Visual)
CreateTab("FPS",Performance)
CreateTab("Temas",ThemePage)
CreateTab("Config",Settings)

print("GOJO MM2 HUB V2.0 carregado!")

