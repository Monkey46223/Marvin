# ```lua
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local pipeTemplate = game.Workspace.PipeTemplate -- Replace with your own pipe template model
local spawnRate = 2 -- Adjust this value to control how frequently new pipes are spawned
local pipeSpeed = 5 -- Adjust this value to control how fast the pipes move towards the player
local jumpForce = 300 -- Adjust this value to control the jump height of the bird

local isJumping = false
local gameActive = true

local function spawnPipe()
    local clone = pipeTemplate:Clone()
    clone.Parent = game.Workspace
    
    local xPos = game.Workspace.CurrentCamera.Focus.Position.X + 100
    local yPos = math.random(-50, 50)
    clone:SetPrimaryPartCFrame(CFrame.new(xPos, yPos, 0))
    
    clone.BodyVelocity.Velocity = Vector3.new(-pipeSpeed, 0, 0)
    
    clone.AncestryChanged:Connect(function(_, parent)
        if parent == nil then
            clone:Destroy()
        end
    end)
end

local function onJump()
    if not isJumping and gameActive then
        isJumping = true
        humanoid:ApplyImpulse(Vector3.new(0, jumpForce, 0))
        
        wait(0.5) -- Adjust this value to control the cooldown between jumps
        
        isJumping = false
    end
end

humanoid.Died:Connect(function()
    gameActive = false
end)

game:GetService("UserInputService").JumpRequest:Connect(onJump)

while gameActive do
    spawnPipe()
    wait(spawnRate)
end
