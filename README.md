-- Carioca Hub v2 - Blox Fruits Script
if game.PlaceId ~= 2753915549 then
    return warn("Este script é apenas para Blox Fruits!")
end

-- Carregar a UI
local Library = loadstring(game:HttpGet("https://pastebin.com/raw/aH9V9szV"))() -- Simple UI Lib
local Window = Library:CreateWindow("Carioca Hub v2 - Blox Fruits")

local MainTab = Window:CreateTab("Principal")
local FarmTab = Window:CreateTab("Auto-Farm")
local TeleportTab = Window:CreateTab("Teleportes")
local SettingsTab = Window:CreateTab("Configurações")
local ExtrasTab = Window:CreateTab("Extras")

-- Variáveis
local AutoFarm = false
local SafeFarm = true
local AutoFruitCollect = false
local AutoQuest = false

-- Funções
function FarmEnemies()
    while AutoFarm and task.wait() do
        pcall(function()
            local player = game.Players.LocalPlayer
            local character = player.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                for _, enemy in pairs(game.Workspace.Enemies:GetChildren()) do
                    if enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                        if SafeFarm then
                            character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame * CFrame.new(0, 20, 0)
                        else
                            character.HumanoidRootPart.CFrame = enemy.HumanoidRootPart.CFrame
                        end
                        repeat
                            task.wait()
                            enemy.Humanoid.Health = enemy.Humanoid.Health - 25
                        until enemy.Humanoid.Health <= 0 or not AutoFarm
                    end
                end
            end
        end)
    end
end

function CollectFruits()
    while AutoFruitCollect and task.wait(5) do
        pcall(function()
            for _, fruit in pairs(game.Workspace:GetChildren()) do
                if fruit:IsA("Tool") and fruit.Name:find("Fruit") then
                    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = fruit.Handle.CFrame
                    wait(1)
                    firetouchinterest(game.Players.LocalPlayer.Character.HumanoidRootPart, fruit.Handle, 0)
                    firetouchinterest(game.Players.LocalPlayer.Character.HumanoidRootPart, fruit.Handle, 1)
                end
            end
        end)
    end
end

function StartAutoQuest()
    while AutoQuest and task.wait(3) do
        pcall(function()
            local QuestService = require(game.ReplicatedStorage:WaitForChild("QuestService"))
            local player = game.Players.LocalPlayer

            if not player.PlayerGui.Main.Quest.Visible then
                -- Pegando missão
                for i,v in pairs(workspace:GetChildren()) do
                    if v:IsA("Model") and v.Name:find("Quest") then
                        player.Character.HumanoidRootPart.CFrame = v.Head.CFrame
                        wait(1)
                        QuestService:StartQuest(v.Name)
                        break
                    end
                end
            end
        end)
    end
end

-- GUI Elements
FarmTab:CreateToggle("Ativar Auto Farm NPCs", false, function(value)
    AutoFarm = value
    if value then
        FarmEnemies()
    end
end)

FarmTab:CreateToggle("Modo Seguro (Anti-Ban)", true, function(value)
    SafeFarm = value
end)

FarmTab:CreateToggle("Ativar Auto Quest", false, function(value)
    AutoQuest = value
    if value then
        StartAutoQuest()
    end
end)

TeleportTab:CreateButton("Cidadela Inicial", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(1123, 5, 1400)
end)

TeleportTab:CreateButton("Ilha dos Homens-Peixe", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(61163, 5, 1219)
end)

TeleportTab:CreateButton("Nova Ilha (Third Sea)", function()
    game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(-249, 7, 5342)
end)

SettingsTab:CreateButton("Ativar Anti-AFK", function()
    local VirtualUser = game:service('VirtualUser')
    game:service('Players').LocalPlayer.Idled:connect(function()
        VirtualUser:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
        task.wait(1)
        VirtualUser:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
    end)
end)

SettingsTab:CreateButton("Fechar Carioca Hub", function()
    Library:Destroy()
end)

ExtrasTab:CreateToggle("Auto Coletar Frutas no Mapa", false, function(value)
    AutoFruitCollect = value
    if value then
        CollectFruits()
    end
end)

ExtrasTab:CreateButton("Despertar Fruta (Iniciar Raid)", function()
    local args = {
        [1] = "AwakeningProgress",
        [2] = true
    }
    game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer(unpack(args))
end)

MainTab:CreateLabel("Bem-vindo ao Carioca Hub v2 - O Melhor Hub Brasileiro!")
