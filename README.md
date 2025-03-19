local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Players = game:GetService("Players")
local StarterGui = game:GetService("StarterGui")


local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")


local flying = false
local speed = 50
local bodyVelocity
local bodyGyro


-- Crear botón de vuelo en pantalla
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")


local flyButton = Instance.new("TextButton")
flyButton.Size = UDim2.new(0.2, 0, 0.1, 0) -- Tamaño del botón
flyButton.Position = UDim2.new(0.75, 0, 0.85, 0) -- Posición en la pantalla
flyButton.Text = "Volar"
flyButton.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
flyButton.TextScaled = true
flyButton.Parent = screenGui


-- Función para activar/desactivar vuelo
local function toggleFly()
    flying = not flying


    if flying then
        -- Crear BodyVelocity para mover al jugador
        bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        bodyVelocity.Parent = humanoidRootPart


        -- Crear BodyGyro para estabilizar al jugador
        bodyGyro = Instance.new("BodyGyro")
        bodyGyro.CFrame = humanoidRootPart.CFrame
        bodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        bodyGyro.Parent = humanoidRootPart


        flyButton.Text = "Detener"
        flyButton.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    else
        -- Eliminar BodyVelocity y BodyGyro para desactivar vuelo
        if bodyVelocity then bodyVelocity:Destroy() end
        if bodyGyro then bodyGyro:Destroy() end


        flyButton.Text = "Volar"
        flyButton.BackgroundColor3 = Color3.fromRGB(0, 200, 255)
    end
end


-- Detectar cuando se presiona el botón en pantalla
flyButton.MouseButton1Click:Connect(toggleFly)


-- Controlar el movimiento mientras se vuela
RunService.RenderStepped:Connect(function()
    if flying then
        local direction = Vector3.new(0, 0, 0)
        local moveVector = UserInputService:GetDeviceAcceleration()


        if moveVector.Magnitude > 0 then
            direction = moveVector * speed
        end


        -- Aplicar movimiento
        bodyVelocity.Velocity = Vector3.new(direction.X, direction.Y, -direction.Z)
    end
end)
