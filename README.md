spawn(
    function()
        game:GetService("RunService").RenderStepped:Connect(
            function()
                if getgenv().LevelFarm == true then
                    game.Players.LocalPlayer.Character.Stun.Value = 0
                    game.Players.LocalPlayer.Character.Humanoid.Sit = false
                    game.Players.LocalPlayer.Character.Busy.Value = false
                end
            end
        )
    end
)
task.spawn(function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Stun") then
		game.Players.LocalPlayer.Character.Stun.Changed:connect(function()
			pcall(function()
				if game.Players.LocalPlayer.Character:FindFirstChild("Stun") then
					game.Players.LocalPlayer.Character.Stun.Value = 0
				end
			end)
		end)
	end
end)

local CombatFramework = require(game:GetService("Players").LocalPlayer.PlayerScripts:WaitForChild("CombatFramework"))
local CombatFrameworkR = getupvalues(CombatFramework)[2]
local RigController = require(game:GetService("Players")["LocalPlayer"].PlayerScripts.CombatFramework.RigController)
local RigControllerR = getupvalues(RigController)[2]
local realbhit = require(game.ReplicatedStorage.CombatFramework.RigLib)
local cooldownfastattack = tick()

function CurrentWeapon()
	local ac = CombatFrameworkR.activeController
	local ret = ac.blades[1]
	if not ret then return game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool").Name end
	pcall(function()
		while ret.Parent~=game.Players.LocalPlayer.Character do ret=ret.Parent end
	end)
	if not ret then return game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool").Name end
	return ret
end

function getAllBladeHitsPlayers(Sizes)
	local Hits = {}
	local Client = game.Players.LocalPlayer
	local Characters = game:GetService("Workspace").Characters:GetChildren()
	for i=1,#Characters do local v = Characters[i]
		local Human = v:FindFirstChildOfClass("Humanoid")
		if v.Name ~= game.Players.LocalPlayer.Name and Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
			table.insert(Hits,Human.RootPart)
		end
	end
	return Hits
end

function getAllBladeHits(Sizes)
	local Hits = {}
	local Client = game.Players.LocalPlayer
	local Enemies = game:GetService("Workspace").Enemies:GetChildren()
	for i=1,#Enemies do local v = Enemies[i]
		local Human = v:FindFirstChildOfClass("Humanoid")
		if Human and Human.RootPart and Human.Health > 0 and Client:DistanceFromCharacter(Human.RootPart.Position) < Sizes+5 then
			table.insert(Hits,Human.RootPart)
		end
	end
	return Hits
end

function AttackFunction()
	local ac = CombatFrameworkR.activeController
	if ac and ac.equipped then
		for indexincrement = 1, 1 do
			local bladehit = getAllBladeHits(0)
			if #bladehit > 0 then
				local AcAttack8 = debug.getupvalue(ac.attack, 5)
				local AcAttack9 = debug.getupvalue(ac.attack, 6)
				local AcAttack7 = debug.getupvalue(ac.attack, 4)
				local AcAttack10 = debug.getupvalue(ac.attack, 7)
				local NumberAc12 = (AcAttack8 * 798405 + AcAttack7 * 727595) % AcAttack9
				local NumberAc13 = AcAttack7 * 798405
				(function()
					NumberAc12 = (NumberAc12 * AcAttack9 + NumberAc13) % 1099511627776
					AcAttack8 = math.floor(NumberAc12 / AcAttack9)
					AcAttack7 = NumberAc12 - AcAttack8 * AcAttack9
				end)()
				AcAttack10 = AcAttack10 + 1
				debug.setupvalue(ac.attack, 5, AcAttack8)
				debug.setupvalue(ac.attack, 6, AcAttack9)
				debug.setupvalue(ac.attack, 4, AcAttack7)
				debug.setupvalue(ac.attack, 7, AcAttack10)
				for k, v in pairs(ac.animator.anims.basic) do
					v:Play(0.01,0.01,0.01)
				end                 
				if game.Players.LocalPlayer.Character:FindFirstChildOfClass("Tool") and ac.blades and ac.blades[1] then 
					game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange",tostring(CurrentWeapon()))
					game.ReplicatedStorage.Remotes.Validator:FireServer(math.floor(NumberAc12 / 1099511627776 * 16777215), AcAttack10)
					game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("hit", bladehit, 2, "") 
				end
			end
		end
	end
end
task.spawn(function()
	while wait() do
		for i,v in pairs(game:GetService("Workspace")["_WorldOrigin"]:GetChildren()) do
			pcall(function()
				if v.Name == ("CurvedRing") or v.Name == ("SlashHit") or v.Name == ("SwordSlash") or v.Name == ("SlashTail") or v.Name == ("Sounds") then
					v:Destroy()
				end
			end)
		end
	end
end)

		coroutine.wrap(function()
			while task.wait(.1) do
				local ac = CombatFrameworkR.activeController
				if ac and ac.equipped then
					if getgenv().LevelFarm then
						AttackFunction()
						if getgenv().LevelFarm then
							if tick() - cooldownfastattack > 0.5 then wait(.2) cooldownfastattack = tick() end
						end
					elseif getgenv().LevelFarm == true then
						if ac.hitboxMagnitude ~= 55 then
							ac.hitboxMagnitude = 55
						end
					if getgenv().LevelFarm == false then
						require(game:GetService("Players").LocalPlayer.PlayerScripts.CombatFramework).activeController.hitboxMagnitude = 50
						end
						ac:attack()
					end
				end
			end
		end)()

task.spawn(function()
	if game.Players.LocalPlayer.Character:FindFirstChild("Stun") then
		game.Players.LocalPlayer.Character.Stun.Changed:connect(function()
			pcall(function()
				if game.Players.LocalPlayer.Character:FindFirstChild("Stun") then
					game.Players.LocalPlayer.Character.Stun.Value = 0
				end
			end)
		end)
	end
end)
		local Client = game.Players.LocalPlayer
		local STOP = require(Client.PlayerScripts.CombatFramework.Particle)
		local STOPRL = require(game:GetService("ReplicatedStorage").CombatFramework.RigLib)
		spawn(function()
			while task.wait() do
				pcall(function()
					if not shared.orl then shared.orl = STOPRL.wrapAttackAnimationAsync end
					if not shared.cpc then shared.cpc = STOP.play end
						STOPRL.wrapAttackAnimationAsync = function(a,b,c,d,func)
						local Hits = STOPRL.getBladeHits(b,c,d)
						if Hits then
            								if getgenv().LevelFarmn then
								STOP.play = function() end
								a:Play(0.01,0.01,0.01)
								func(Hits)
								STOP.play = shared.cpc
								wait(a.length * 0.5)
								a:Stop()
							else
								a:Play()
							end
						end
					end
				end)
			end
		end)
local plr = game.Players.LocalPlayer local CbFw = debug.getupvalues(require(plr.PlayerScripts.CombatFramework))local CbFw2 = CbFw[2]function GetCurrentBlade()local p13 = CbFw2.activeController local ret = p13.blades[1] if not ret then return end while ret.Parent~=game.Players.LocalPlayer.Character do ret=ret.Parent end return ret end function AttackNoCD() local AC = CbFw2.activeController for i = 1, 1 do  local bladehit = require(game.ReplicatedStorage.CombatFramework.RigLib).getBladeHits( plr.Character, {plr.Character.HumanoidRootPart}, 60 )local cac = {}local hash = {}for k, v in pairs(bladehit) do if v.Parent:FindFirstChild("HumanoidRootPart") and not hash[v.Parent] then table.insert(cac, v.Parent.HumanoidRootPart)hash[v.Parent] = true end end bladehit = cac if #bladehit > 0 then local u8 = debug.getupvalue(AC.attack, 5)local u9 = debug.getupvalue(AC.attack, 6)local u7 = debug.getupvalue(AC.attack, 4)local u10 = debug.getupvalue(AC.attack, 7)local u12 = (u8 * 798405 + u7 * 727595) % u9 local u13 = u7 * 798405 (function()u12 = (u12 * u9 + u13) % 1099511627776 u8 = math.floor(u12 / u9)u7 = u12 - u8 * u9 end)()u10 = u10 + 1 debug.setupvalue(AC.attack, 5, u8)debug.setupvalue(AC.attack, 6, u9)debug.setupvalue(AC.attack, 4, u7)debug.setupvalue(AC.attack, 7, u10)pcall(function()for k, v in pairs(AC.animator.anims.basic) do v:Play()end end)if plr.Character:FindFirstChildOfClass("Tool") and AC.blades and AC.blades[1] then game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("weaponChange",tostring(GetCurrentBlade()))game.ReplicatedStorage.Remotes.Validator:FireServer(math.floor(u12 / 1099511627776 * 16777215), u10)game:GetService("ReplicatedStorage").RigControllerEvent:FireServer("hit", bladehit, i, "")end end end end require(game.ReplicatedStorage.Util.CameraShaker):Stop()task.spawn(function()while task.wait() do pcall(function()if getgenv().LevelFarm then if getgenv().LevelFarm then AttackNoCD()end end end)end end) local CameraShaker = require(game.ReplicatedStorage.Util.CameraShaker)CombatFrameworkR = require(game:GetService("Players").LocalPlayer.PlayerScripts.CombatFramework)y = debug.getupvalues(CombatFrameworkR)[2]spawn(function()game:GetService("RunService").RenderStepped:Connect(function()if getgenv().LevelFarm then if typeof(y) == "table" then pcall(function()CameraShaker:Stop()y.activeController.timeToNextAttack = -10 y.activeController.timeToNextAttack = 0 y.activeController.hitboxMagnitude = 100 y.activeController.active = false y.activeController.timeToNextBlock = 0 y.activeController.focusStart = 0 y.activeController.increment = 0 y.activeController.blocking = false y.activeController.attacking = false y.activeController.humanoid.AutoRotate = true end)end end end)end)spawn(function()game:GetService("RunService").RenderStepped:Connect(function()if getgenv().LevelFarm == true then game.Players.LocalPlayer.Character.Stun.Value = 0 game.Players.LocalPlayer.Character.Humanoid.Sit = false game.Players.LocalPlayer.Character.Busy.Value = false end end)end)
