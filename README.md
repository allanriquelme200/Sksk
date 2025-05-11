-- Aimbot + AutoShoot com FOV para Delta Executor (Mobile)
-- Testado para jogos simples (FPS/Tiro) com armas de click

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")

local FOV_RADIUS = 100
local TARGET_PART = "Head"
local SHOOT_INTERVAL = 0.15
local lastShot = 0

-- FOV Circle
local fovCircle = Drawing.new("Circle")
fovCircle.Radius = FOV_RADIUS
fovCircle.Thickness = 2
fovCircle.Color = Color3.fromRGB(255, 0, 0)
fovCircle.Filled = false
fovCircle.Transparency = 1
fovCircle.Visible = true

-- Função para achar o inimigo mais perto da mira
local function getClosestTarget()
    local closest = nil
    local shortestDistance = FOV_RADIUS
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(TARGET_PART) then
            local part = player.Character:FindFirstChild(TARGET_PART)
            local pos, onScreen = Camera:WorldToViewportPoint(part.Position)
            if onScreen then
                local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)).Magnitude
                if dist < shortestDistance then
                    shortestDistance = dist
                    closest = part
                end
            end
        end
    end
    return closest
end

-- Loop
RunService.RenderStepped:Connect(function()
    -- Atualiza círculo
    fovCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

    -- Busca alvo
    local alvo = getClosestTarget()
    if alvo then
        -- Mira (Delta permite isso em jogos simples)
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, alvo.Position)

        -- AutoShoot
        if tick() - lastShot >= SHOOT_INTERVAL then
            mouse1click()
            lastShot = tick()
        end
    end
end)
