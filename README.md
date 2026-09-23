if _G.BloodHoundsRunning then return end _G.BloodHoundsRunning = true local Players =
game:GetService("Players") local RunService = game:GetService("RunService") local UIS =
game:GetService("UserInputService") local TS = game:GetService("TweenService") local
Lighting = game:GetService("Lighting") local HS = game:GetService("HttpService") local
ReplicatedStorage = game:GetService("ReplicatedStorage") local LP = Players.LocalPlayer
local camera = workspace.CurrentCamera NS = 60 CS = 29 LAGGER_SPEED_1 = 20
LAGGER_SPEED_2 = 10 MEDUSA_COOLDOWN = 25 BAT_AIMBOT_SPEED = 58
BYPASS_AIMBOT_SPEED = 60 MOBILE_PANEL_WIDTH = 128 MOBILE_PANEL_HEIGHT =
294 CONFIG_FILE = "BloodHounds.json" BAT_V2_HIT_DIST = 4.5 _isDraggingButton = false
speedMode = false antiRagdollEnabled = false jumpEnabled = false laggerToggled = false
laggerLevel = 1 medusaCounterEnabled = false batCounterEnabled = false unwalkEnabled =
false autoLeftEnabled = false autoRightEnabled = false autoBatEnabled = false dropMode = 1
antiLagEnabled = false removeAccessoriesEnabled = false stretchEnabled = false stretchFOV =
120 medusaAutoResetEnabled = false uiLocked = true editModeEnabled = false uiScaleValue
= 100 espEnabled = false fovSelectorVisible = false bodyLockEnabled = false bodyLockRange
= 20 bodyLockRangeBox = nil _bodyLockConn = nil bodyLockSetVisual = nil
_blSuppressCount = 0 _blWasEnabled = false _blRestoreTimer = nil _blSmoothRestore = false
savedProgressBarPos = nil savedButtonPositions = {} savedMobilePanelPos = nil
instaResetFloatingPos = nil tpBatFloatingPos = nil batV2FloatingPos = nil neonWeatherEnabled
= false _originalLighting = nil setNeonWeatherVisual = nil currentAnimPack = "Off"
originalTryardAnims = nil tryardHeartbeatConn = nil animSelectorLabel = nil autoBatV2Enabled
= false autoBatV2SwingEnabled = true autoBatV2HitCooldown = false AUTO_BAT_V2_SPEED
= 60 AUTO_BAT_V2_DIST = 1.0 AUTO_BAT_V2_HEIGHT = 1.5 AUTO_BAT_V2_V_OFF = 0.0
AUTO_BAT_V2_HIT_DIST = 4.5 AUTO_BAT_V2_SWING_CD = 0.08 _batV2Conn = nil local
speedLinearVelocity = nil local speedAttachment = nil local speedConnection = nil local
currentSpeedValue = NS local speedEnabled = false local lastPosition = nil local lastTime = nil
local lagbackCooldown = 0 local ownershipTimer = 0 local CoreGui =
game:GetService("CoreGui") local InfJumpState = { enabled = true, mode = "hold", } local
_lastInfJump = 0 local _gamepadBtnHeld = false local _activeTouches = {} local
_lastTouchStart = nil local _holdTouch = nil local function applyImpulse(root, hum, yVel) local
attachment = root:FindFirstChild("InfJumpAttachment") or Instance.new("Attachment")
attachment.Name = "InfJumpAttachment" attachment.Parent = root local currentX =
root.AssemblyLinearVelocity.X local currentZ = root.AssemblyLinearVelocity.Z local targetY =
yVel or 50 local lv = Instance.new("LinearVelocity") lv.Name = "InfJumpVelocity" lv.MaxForce =
999999 lv.VectorVelocity = Vector3.new(currentX, targetY, currentZ) lv.RelativeTo =
Enum.ActuatorRelativeTo.World lv.Attachment0 = attachment lv.Parent = root task.delay(0.08,
function() if lv then lv:Destroy() end if attachment then attachment:Destroy() end end) end local
function _doInfJump() local now = os.clock() if now - _lastInfJump < 0.1 then return end
_lastInfJump = now local c = LP.Character; if not c then return end local hum =
c:FindFirstChildOfClass("Humanoid"); if not hum or hum.Health <= 0 then return end local root
= c:FindFirstChild("HumanoidRootPart"); if not root then return end hum.Jump = true
applyImpulse(root, hum) end UIS.JumpRequest:Connect(function() if not InfJumpState.enabled
then return end if InfJumpState.mode == "hold" then if _holdTouch == nil and _lastTouchStart
~= nil and _activeTouches[_lastTouchStart] then _holdTouch = _lastTouchStart end return end
_doInfJump() end) local function isGamepad(input) local uit = input.UserInputType return uit ==
Enum.UserInputType.Gamepad1 or uit == Enum.UserInputType.Gamepad2 or uit ==
Enum.UserInputType.Gamepad3 or uit == Enum.UserInputType.Gamepad4 or uit ==
Enum.UserInputType.Gamepad5 or uit == Enum.UserInputType.Gamepad6 or uit ==
Enum.UserInputType.Gamepad7 or uit == Enum.UserInputType.Gamepad8 end
UIS.InputBegan:Connect(function(inp) if inp.KeyCode ~= Enum.KeyCode.ButtonA or not
isGamepad(inp) then return end _gamepadBtnHeld = true if InfJumpState.enabled and
InfJumpState.mode == "manual" then _doInfJump() end end)
UIS.InputEnded:Connect(function(inp) if inp.KeyCode ~= Enum.KeyCode.ButtonA or not
isGamepad(inp) then return end _gamepadBtnHeld = false end)UIS.TouchStarted:Connect(function(touch) if not InfJumpState.enabled or InfJumpState.mode
~= "hold" then return end _activeTouches[touch] = true _lastTouchStart = touch end)
UIS.TouchEnded:Connect(function(touch) _activeTouches[touch] = nil if _lastTouchStart ==
touch then _lastTouchStart = nil end if _holdTouch == touch then _holdTouch = nil end end)
RunService.Heartbeat:Connect(function() if not InfJumpState.enabled or InfJumpState.mode
~= "hold" then return end local c = LP.Character; if not c then return end local root =
c:FindFirstChild("HumanoidRootPart"); if not root then return end local hum =
c:FindFirstChildOfClass("Humanoid"); if not hum or hum.Health <= 0 then return end local
jumpHeld = UIS:IsKeyDown(Enum.KeyCode.Space) or _gamepadBtnHeld or (_holdTouch ~=
nil) if jumpHeld and root.AssemblyLinearVelocity.Y < 30 then hum.Jump = true
applyImpulse(root, hum, 52) end if root.AssemblyLinearVelocity.Y < -120 then
applyImpulse(root, hum, -120) end end) local function createInfJumpPanel() return nil, nil end
local function getCharParts() local char = LP.Character if not char then return nil, nil end local
hum = char:FindFirstChildOfClass("Humanoid") local root =
char:FindFirstChild("HumanoidRootPart") if not hum or not root then return nil, nil end return
hum, root end local function claimOwnership(root) pcall(function() root:SetNetworkOwner(LP)
end) end local function cleanupSpeedPhysics() if speedLinearVelocity then
speedLinearVelocity:Destroy() speedLinearVelocity = nil end if speedAttachment then
speedAttachment:Destroy() speedAttachment = nil end if speedConnection then
speedConnection:Disconnect() speedConnection = nil end lastPosition = nil lastTime = nil
speedEnabled = false end local function applySpeedWithLinearVelocity(spd)
cleanupSpeedPhysics() if spd <= 0 then return end local hum, root = getCharParts() if not hum
or not root then return end claimOwnership(root) speedAttachment =
Instance.new("Attachment") speedAttachment.Name = "SpeedAttachment"
speedAttachment.Parent = root speedLinearVelocity = Instance.new("LinearVelocity")
speedLinearVelocity.Name = "SpeedLinearVelocity" speedLinearVelocity.Attachment0 =
speedAttachment speedLinearVelocity.RelativeTo = Enum.ActuatorRelativeTo.World
speedLinearVelocity.VelocityConstraintMode = Enum.VelocityConstraintMode.Plane
speedLinearVelocity.PrimaryTangentAxis = Vector3.new(1, 0, 0)
speedLinearVelocity.SecondaryTangentAxis = Vector3.new(0, 0, 1)
speedLinearVelocity.MaxForce = 100000 speedLinearVelocity.PlaneVelocity = Vector2.zero
speedLinearVelocity.Enabled = false speedLinearVelocity.Parent = root lastPosition =
root.Position lastTime = tick() speedEnabled = true currentSpeedValue = spd speedConnection
= RunService.Heartbeat:Connect(function(dt) if not speedEnabled then return end local hum2,
root2 = getCharParts() if not hum2 or not root2 or not speedLinearVelocity then
cleanupSpeedPhysics() return end ownershipTimer = ownershipTimer + dt if ownershipTimer

> = 1.5 then claimOwnership(root2) ownershipTimer = 0 end local dir = hum2.MoveDirection if
dir.Magnitude < 0.1 then speedLinearVelocity.Enabled = false lastPosition = root2.Position
lastTime = tick() return end speedLinearVelocity.Enabled = true
speedLinearVelocity.PlaneVelocity = Vector2.new(dir.X * spd, dir.Z * spd) lagbackCooldown =
lagbackCooldown - dt local now = tick() local elapsed = now - lastTime if elapsed > 0.1 and
lastPosition then local expectedDist = spd * elapsed local actualDist = (root2.Position -
lastPosition).Magnitude if actualDist < expectedDist * 0.3 and lagbackCooldown <= 0 then
speedLinearVelocity.PlaneVelocity = Vector2.new(dir.X * spd * 1.2, dir.Z * spd * 1.2)
lagbackCooldown = 0.3 end end lastPosition = root2.Position lastTime = now end) end local
ANIM_PACKS = { Tryhard = { idle1 = "rbxassetid://133806214992291", idle2 = "rbxassetid://
94970088341563", walk = "rbxassetid://707897309", run = "rbxassetid://707861613", jump =
"rbxassetid://116936326516985", fall = "rbxassetid://116936326516985", climb =
"rbxassetid://116936326516985", swim = "rbxassetid://116936326516985", swimidle =
"rbxassetid://116936326516985", }, Crazy = { idle1 = "rbxassetid://133806214992291", idle2 =
"rbxassetid://94970088341563", walk = "rbxassetid://134824450619865", run = "rbxassetid://
134824450619865", jump = "rbxassetid://121454505477205", fall = "rbxassetid://
94788218468396", climb = "rbxassetid://121454505477205", swim = "rbxassetid://
121454505477205", swimidle = "rbxassetid://121454505477205", } } localANIM_PACK_ORDER = {{"Off", "Off"}, {"Tryhard", "Tryhard"}, {"Crazy", "Crazy"}} local function
isPackAnim(id) for _, pack in pairs(ANIM_PACKS) do for _, v in pairs(pack) do if v == id then
return true end end end return false end local function saveOriginalAnims(char) local animate =
char:FindFirstChild("Animate") if not animate then return end local function g(obj) return obj and
obj.AnimationId or nil end local ids = { idle1 = g(animate.idle and animate.idle.Animation1),
idle2 = g(animate.idle and animate.idle.Animation2), walk = g(animate.walk and
animate.walk.WalkAnim), run = g(animate.run and animate.run.RunAnim), jump =
g(animate.jump and animate.jump.JumpAnim), fall = g(animate.fall and animate.fall.FallAnim),
climb = g(animate.climb and animate.climb.ClimbAnim), swim = g(animate.swim and
animate.swim.Swim), swimidle = g(animate.swimidle and animate.swimidle.SwimIdle), } if not
isPackAnim(ids.walk) then originalTryardAnims = ids end end local function
applyAnimPack(packName) currentAnimPack = packName if animSelectorLabel then
animSelectorLabel.Text = packName end if packName == "Off" then if originalTryardAnims and
LP.Character then local animate = LP.Character:FindFirstChild("Animate") if animate then local
function s(obj,id) if obj then obj.AnimationId = id end end s(animate.idle and
animate.idle.Animation1, originalTryardAnims.idle1) s(animate.idle and animate.idle.Animation2,
originalTryardAnims.idle2) s(animate.walk and animate.walk.WalkAnim,
originalTryardAnims.walk) s(animate.run and animate.run.RunAnim, originalTryardAnims.run)
s(animate.jump and animate.jump.JumpAnim, originalTryardAnims.jump) s(animate.fall and
animate.fall.FallAnim, originalTryardAnims.fall) s(animate.climb and animate.climb.ClimbAnim,
originalTryardAnims.climb) s(animate.swim and animate.swim.Swim, originalTryardAnims.swim)
s(animate.swimidle and animate.swimidle.SwimIdle, originalTryardAnims.swimidle) end end if
tryardHeartbeatConn then tryardHeartbeatConn:Disconnect(); tryardHeartbeatConn = nil end
return end local pack = ANIM_PACKS[packName] if not pack then return end if
tryardHeartbeatConn then tryardHeartbeatConn:Disconnect() end tryardHeartbeatConn =
RunService.Heartbeat:Connect(function() local c = LP.Character if not c then return end local
animate = c:FindFirstChild("Animate") if not animate then return end local function s(obj,id) if
obj then obj.AnimationId = id end end s(animate.idle and animate.idle.Animation1, pack.idle1)
s(animate.idle and animate.idle.Animation2, pack.idle2) s(animate.walk and
animate.walk.WalkAnim, pack.walk) s(animate.run and animate.run.RunAnim, pack.run)
s(animate.jump and animate.jump.JumpAnim, pack.jump) s(animate.fall and
animate.fall.FallAnim, pack.fall) s(animate.climb and animate.climb.ClimbAnim, pack.climb)
s(animate.swim and animate.swim.Swim, pack.swim) s(animate.swimidle and
animate.swimidle.SwimIdle, pack.swimidle) end) end local function startAnimPack(packName)
local char = LP.Character if char then saveOriginalAnims(char) applyAnimPack(packName) local
hum = char:FindFirstChildOfClass("Humanoid") if hum then for _, track in
ipairs(hum:GetPlayingAnimationTracks()) do track:Stop(0) end
hum:ChangeState(Enum.HumanoidStateType.Running) end else applyAnimPack(packName)
end currentAnimPack = packName end local function stopAnimPack() currentAnimPack = "Off"
if animSelectorLabel then animSelectorLabel.Text = "Off" end applyAnimPack("Off") end
DEFAULT_KB = { DropBrainrot = {kb = Enum.KeyCode.X, gp = nil}, AutoLeft = {kb =
Enum.KeyCode.Z, gp = nil}, AutoRight = {kb = Enum.KeyCode.C, gp = nil}, AutoBat = {kb =
Enum.KeyCode.E, gp = nil}, TPFloor = {kb = Enum.KeyCode.F, gp = nil}, GuiHide = {kb =
Enum.KeyCode.LeftControl, gp = nil}, CarryToggle = {kb = Enum.KeyCode.Q, gp = nil},
LaggerMode = {kb = Enum.KeyCode.R, gp = nil}, InstaReset = {kb = Enum.KeyCode.G, gp =
nil}, TPLock = {kb = Enum.KeyCode.B, gp = nil}, BatV2 = {kb = Enum.KeyCode.N, gp = nil}, }
KB = { DropBrainrot = {kb = DEFAULT_KB.DropBrainrot.kb, gp =
DEFAULT_KB.DropBrainrot.gp}, AutoLeft = {kb = DEFAULT_KB.AutoLeft.kb, gp =
DEFAULT_KB.AutoLeft.gp}, AutoRight = {kb = DEFAULT_KB.AutoRight.kb, gp =
DEFAULT_KB.AutoRight.gp}, AutoBat = {kb = DEFAULT_KB.AutoBat.kb, gp =
DEFAULT_KB.AutoBat.gp}, TPFloor = {kb = DEFAULT_KB.TPFloor.kb, gp =
DEFAULT_KB.TPFloor.gp}, GuiHide = {kb = DEFAULT_KB.GuiHide.kb, gp =
DEFAULT_KB.GuiHide.gp}, CarryToggle = {kb = DEFAULT_KB.CarryToggle.kb, gp =
DEFAULT_KB.CarryToggle.gp}, LaggerMode = {kb = DEFAULT_KB.LaggerMode.kb, gp =DEFAULT_KB.LaggerMode.gp}, InstaReset = {kb = DEFAULT_KB.InstaReset.kb, gp =
DEFAULT_KB.InstaReset.gp}, TPLock = {kb = DEFAULT_KB.TPLock.kb, gp =
DEFAULT_KB.TPLock.gp}, BatV2 = {kb = DEFAULT_KB.BatV2.kb, gp =
DEFAULT_KB.BatV2.gp}, } _isResetting = false _lastSavedJSON = nil _isLoading = false
CONFIG = { AUTO_STEAL_ENABLED = false, HOLD_MIN = 1.3, HOLD_MAX = 2.6,
ENTRY_DELAY = 0.3, COOLDOWN = 0.05, STEAL_RANGE = 9, PRIME_RANGE = 80, }
StealState = { active = false, startTime = 0, phase = "idle", label = "", lastResult = "",
lastResultTime = 0, totalSteals = 0, failedSteals = 0, } savedStealRadius =
CONFIG.STEAL_RANGE savedStealDuration = CONFIG.HOLD_MAX local plots =
workspace:WaitForChild("Plots") local AnimalsData = {} local syncRemotes = nil local
plotAnimalSync = { caches = {}, connections = {} } local allAnimalsCache = {} local
PromptMemoryCache = {} local InternalStealCache = {} local stealConnection = nil local
function splitSyncPath(path) if typeof(path) == "table" then return path end local out = {} for
part in string.gmatch(tostring(path), "[^%.]+") do table.insert(out, tonumber(part) or part) end
return out end local function resolveSyncPath(path, root) local current = root local parent = nil
local key = nil for _, part in ipairs(splitSyncPath(path)) do parent = current key = part current =
current and current[part] or nil end return current, parent, key end local function
applyPlotSyncDiff(channelName, packet) local cache = plotAnimalSync.caches[channelName]
if typeof(cache) ~= "table" then return end local path, action, a, b = packet[1], packet[2],
packet[3], packet[4] local current, parent, key = resolveSyncPath(path, cache) if action ==
"Changed" then if parent ~= nil then parent[key] = a end elseif action == "ArrayInsert" then if
current ~= nil then table.insert(current, b, a) end elseif action == "ArrayRemoved" then if
current ~= nil then table.remove(current, b) end elseif action == "DictionaryInsert" then if
current ~= nil then current[b] = a end elseif action == "DictionaryRemoved" then if current ~=
nil then current[b] = nil end end end local function attachPlotChannel(remote) if
plotAnimalSync.connections[remote] then return end local channelName =
tostring(remote.Name) if not plots:FindFirstChild(channelName) then return end if
syncRemotes.requestData and plotAnimalSync.caches[channelName] == nil then local ok, data
= pcall(function() return syncRemotes.requestData:InvokeServer(channelName) end) if ok and
typeof(data) == "table" then plotAnimalSync.caches[channelName] = data else
plotAnimalSync.caches[channelName] = {} end elseif plotAnimalSync.caches[channelName] ==
nil then plotAnimalSync.caches[channelName] = {} end plotAnimalSync.connections[remote] =
remote.OnClientEvent:Connect(function(queue) for _, packet in ipairs(queue) do
applyPlotSyncDiff(channelName, packet) end end) end local function
detachPlotChannel(channelName) for remote, conn in pairs(plotAnimalSync.connections) do if
tostring(remote.Name) == tostring(channelName) then conn:Disconnect()
plotAnimalSync.connections[remote] = nil plotAnimalSync.caches[tostring(channelName)] = nil
break end end end local function getPlotChannelData(plotName) return
plotAnimalSync.caches[plotName] end local function getPlotOwner(plot) local sign =
plot:FindFirstChild("PlotSign") local frame = sign and sign:FindFirstChild("SurfaceGui") and
sign.SurfaceGui:FindFirstChild("Frame") local label = frame and
frame:FindFirstChild("TextLabel") if not label or label.Text == "Empty Base" then return nil end
return label.Text:gsub("'s [Bb]ase$", ""):gsub("%s+$", "") end local function
isMyBaseAnimal(animalData) if not animalData or not animalData.plot then return false end
local plot = plots:FindFirstChild(animalData.plot) if not plot then return false end return
getPlotOwner(plot) == LP.DisplayName end local function
findProximityPromptForAnimal(animalData) if not animalData then return nil end local cached =
PromptMemoryCache[animalData.uid] if cached and cached.Parent then return cached end
local plot = plots:FindFirstChild(animalData.plot) if not plot then return nil end local podiums =
plot:FindFirstChild("AnimalPodiums") if not podiums then return nil end local podium =
podiums:FindFirstChild(animalData.slot) if not podium then return nil end local base =
podium:FindFirstChild("Base") if not base then return nil end local spawn =
base:FindFirstChild("Spawn") if not spawn then return nil end local attach =
spawn:FindFirstChild("PromptAttachment") if not attach then return nil end for _, p inipairs(attach:GetChildren()) do if p:IsA("ProximityPrompt") then
PromptMemoryCache[animalData.uid] = p return p end end return nil end local function
getAnimalPosition(animalData) local plot = plots:FindFirstChild(animalData.plot) if not plot then
return nil end local podiums = plot:FindFirstChild("AnimalPodiums") if not podiums then return
nil end local podium = podiums:FindFirstChild(animalData.slot) if not podium then return nil end
return podium:GetPivot().Position end local function distToAnimal(animalData) local character =
LP.Character if not character then return math.huge end local hrp =
character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("UpperTorso") if not
hrp then return math.huge end local pos = getAnimalPosition(animalData) if not pos then return
math.huge end return (hrp.Position - pos).Magnitude end local function pickClosest() local
character = LP.Character if not character then return nil end local hrp =
character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("UpperTorso") if not
hrp then return nil end local best, bestDist = nil, math.huge for _, animalData in
ipairs(allAnimalsCache) do if isMyBaseAnimal(animalData) then continue end local pos =
getAnimalPosition(animalData) if not pos then continue end local dist = (hrp.Position -
pos).Magnitude if dist > CONFIG.PRIME_RANGE then continue end if dist < bestDist then
bestDist = dist best = animalData end end return best end local function scanAllPlots() local
newCache = {} for _, plot in ipairs(plots:GetChildren()) do local cache =
getPlotChannelData(plot.Name) if not cache then continue end local animalList =
cache.AnimalList if typeof(animalList) ~= "table" then continue end for slot, animalData in
pairs(animalList) do if type(animalData) == "table" then local animalName = animalData.Index
local animalInfo = AnimalsData[animalName] if not animalInfo then co
