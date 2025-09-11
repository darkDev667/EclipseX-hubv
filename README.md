-- Carregar a UI library
local redzlib = loadstring(game:HttpGet("https://raw.githubusercontent.com/tbao143/Library-ui/refs/heads/main/Redzhubui"))()
 
-- Criar a janela principal
local Window = redzlib:MakeWindow({
    Title = "EclipseX hub",
    SubTitle = "owner Xzbrian_darkzX",
    SaveFolder = "testando | redz lib v5.lua"
})
 
-- Ícone de minimizar
Window:AddMinimizeButton({
    Button = { Image = "rbxassetid://18751483361", BackgroundTransparency = 0 },
    Corner = { CornerRadius = UDim.new(0, 6) },
})
 
-- Aba Discord
local Tab1 = Window:MakeTab({
    Title = "Discord",
    Icon = "rbxassetid://84198990394879"
})
 
Tab1:AddDiscordInvite({
    Name = "snuffyhub",
    Description = "Discordlink",
    Logo = "",
    Invite = "https://discord.gg/Rjkdkpvd"
})
 
-- Aba de Funções (Cliente)
local Funcoes = Window:MakeTab({
    Title = "Cliente",
    Icon = "rbxassetid://18751483361"
})

-- Aba de Casas (House)
local House = Window:MakeTab({
    Title = "House",
    Icon = "rbxassetid://18751483361"
})

-- Auto copiar link do Discord ao executar
pcall(function()
    setclipboard("https://discord.gg/Rjkdkpvd")
    game.StarterGui:SetCore("SendNotification", {
        Title = "EclipseX Hub",
        Text = "Link do Discord copiado para área de transferência!",
        Duration = 5
    })
end)

-- Função de Velocidade
Funcoes:AddSlider({
    Name = "Velocidade",
    Min = 16,
    Max = 200,
    Default = 16,
    Callback = function(Value)
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.WalkSpeed = Value
        end
    end
})

-- Função de Pulo
Funcoes:AddSlider({
    Name = "JumpPower",
    Min = 50,
    Max = 500,
    Default = 50,
    Callback = function(Value)
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.JumpPower = Value
        end
    end
})

-- Função de Gravidade
Funcoes:AddSlider({
    Name = "Gravidade",
    Min = 0,
    Max = 500,
    Default = 196,
    Callback = function(Value)
        workspace.Gravity = Value
    end
})

-- Fly V3
Funcoes:AddButton({
    Name = "Fly V3",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fly-V3-48359"))()
    end
})

-- TP Tool
Funcoes:AddButton({
    Name = "TP Tool",
    Callback = function()
        local player = game.Players.LocalPlayer
        local mouse = player:GetMouse()
        local tool = Instance.new("Tool")
        tool.RequiresHandle = false
        tool.Name = "TP Tool"

        tool.Activated:Connect(function()
            if mouse.Hit then
                player.Character:MoveTo(mouse.Hit.p)
            end
        end)

        tool.Parent = player.Backpack
        game.StarterGui:SetCore("SendNotification", {
            Title = "EclipseX Hub",
            Text = "TP Tool adicionada ao inventário!",
            Duration = 4
        })
    end
})

-- ESP (toggle)
local espAtivado = false
local espHighlights = {}

Funcoes:AddToggle({
    Name = "ESP",
    Default = false,
    Callback = function(Value)
        espAtivado = Value
        if espAtivado then
            for _, v in pairs(game.Players:GetPlayers()) do
                if v ~= game.Players.LocalPlayer and v.Character then
                    for _, part in pairs(v.Character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            local highlight = Instance.new("BoxHandleAdornment")
                            highlight.Size = part.Size
                            highlight.Color3 = Color3.fromRGB(255, 0, 0)
                            highlight.Transparency = 0.5
                            highlight.AlwaysOnTop = true
                            highlight.ZIndex = 5
                            highlight.Adornee = part
                            highlight.Parent = part
                            table.insert(espHighlights, highlight)
                        end
                    end
                end
            end
            game.StarterGui:SetCore("SendNotification", {
                Title = "EclipseX Hub",
                Text = "ESP ativado! Jogadores destacados em vermelho.",
                Duration = 4
            })
        else
            for _, h in pairs(espHighlights) do
                if h and h.Parent then
                    h:Destroy()
                end
            end
            espHighlights = {}
            game.StarterGui:SetCore("SendNotification", {
                Title = "EclipseX Hub",
                Text = "ESP desativado!",
                Duration = 4
            })
        end
    end
})

-- Anti Lag (toggle)
local antiLagAtivo = false
local savedSettings = {
    shadows = game.Lighting.GlobalShadows,
    fog = game.Lighting.FogEnd,
    quality = settings().Rendering.QualityLevel
}

Funcoes:AddToggle({
    Name = "Anti Lag",
    Default = false,
    Callback = function(Value)
        antiLagAtivo = Value
        if antiLagAtivo then
            for _, v in pairs(workspace:GetDescendants()) do
                if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Explosion") then
                    v:Destroy()
                end
            end
            game.Lighting.GlobalShadows = false
            game.Lighting.FogEnd = 1e10
            settings().Rendering.QualityLevel = Enum.QualityLevel.Level01

            game.StarterGui:SetCore("SendNotification", {
                Title = "EclipseX Hub",
                Text = "Anti Lag ativado!",
                Duration = 4
            })
        else
            game.Lighting.GlobalShadows = savedSettings.shadows
            game.Lighting.FogEnd = savedSettings.fog
            settings().Rendering.QualityLevel = savedSettings.quality

            game.StarterGui:SetCore("SendNotification", {
                Title = "EclipseX Hub",
                Text = "Anti Lag desativado!",
                Duration = 4
            })
        end
    end
})

-- Unban (toggle noclip)
local noclipConexao
local noclipAtivo = false

House:AddToggle({
    Name = "Unban",
    Default = false,
    Callback = function(Value)
        noclipAtivo = Value
        local player = game.Players.LocalPlayer

        if noclipAtivo then
            noclipConexao = game:GetService("RunService").Stepped:Connect(function()
                if player.Character then
                    for _, part in pairs(player.Character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
                    end
                end
            end)
            game.StarterGui:SetCore("SendNotification", {
                Title = "EclipseX Hub",
                Text = "Unban (Noclip) ativado!",
                Duration = 4
            })
        else
            if noclipConexao then
                noclipConexao:Disconnect()
            end
            if player.Character then
                for _, part in pairs(player.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true
                    end
                end
            end
            game.StarterGui:SetCore("SendNotification", {
                Title = "EclipseX Hub",
                Text = "Unban (Noclip) desativado!",
                Duration = 4
            })
        end
    end
})# EclipseX-hubv
Script 
