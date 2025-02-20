-- Пихать в StarterPlayerScripts
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

local defaultSpeed = humanoid.WalkSpeed -- Стандартна швидкість
local slowSpeed = defaultSpeed / 2 -- Швидкість у присяді
local crouchHeight = 1 -- Висота у присяді
local isCrouching = false

local function removeFootstepSounds()
	local rootPart = character:WaitForChild("HumanoidRootPart")
	for _, sound in ipairs(rootPart:GetChildren()) do
		if sound:IsA("Sound") then
			sound:Destroy()
		end
	end
	rootPart.ChildAdded:Connect(function(child)
		if child:IsA("Sound") then
			child:Destroy()
		end
	end)
end

removeFootstepSounds()

-- Функція для перемикання режиму присяду
local function toggleCrouch()
	if isCrouching then
		humanoid.HipHeight = 2 -- Повертаємо стандартну висоту
		humanoid.WalkSpeed = defaultSpeed -- Повертаємо стандартну швидкість
	else
		humanoid.HipHeight = crouchHeight -- Зменшуємо висоту
		humanoid.WalkSpeed = slowSpeed -- Уповільнюємо персонажа
	end
	isCrouching = not isCrouching
end

UserInputService.InputBegan:Connect(function(input, processed)
	if processed then return end

	if input.KeyCode == Enum.KeyCode.C then
		toggleCrouch()
	end

	if input.KeyCode == Enum.KeyCode.LeftShift and not isCrouching then
		humanoid.WalkSpeed = defaultSpeed * 1.5 -- Прискорення під час бігу (тільки якщо не в присяді)
	end
end)

UserInputService.InputEnded:Connect(function(input, processed)
	if processed then return end

	if input.KeyCode == Enum.KeyCode.LeftShift and not isCrouching then
		humanoid.WalkSpeed = defaultSpeed -- Повертаємо нормальну швидкість після бігу
	end
end)

player.CharacterAdded:Connect(function(newChar)
	character = newChar
	humanoid = character:WaitForChild("Humanoid")
	removeFootstepSounds()
end)
