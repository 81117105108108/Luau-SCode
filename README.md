# Luau State-of-the-Art Shitcode Principles

## The Principles

### 💩 Name variables in a way as if your code was already obfuscated

Fewer keystrokes, more time for you.

_Good 👍🏻_

```luau
local a = 42
```

_Bad 👎🏻_

```luau
local age = 42
```

### 💩 Mix variable/functions naming style

Celebrate the difference.

_Good 👍🏻_

```luau
local wWidth = 640
local w_height = 480
```

_Bad 👎🏻_

```luau
local windowWidth = 640
local windowHeight = 480
```

### 💩 Never write comments

No one is going to read your code anyway.

_Good 👍🏻_

```luau
local cdr = 700
```

_Bad 👎🏻_

More often comments should contain some 'why' and not some 'what'. If the 'what' is not clear in the code, the code is probably too messy.

```luau
-- The number of 700ms has been calculated empirically based on UX A/B test results.
-- @see: <link to experiment or to related Jira task or to something that explains number 700 in details>
local callbackDebounceRate = 700
```

### 💩 Always write comments in your native language

If you violated the "No comments" principle then at least try to write comments in a language that is different from the language you use to write the code. If your native language is English you may violate this principle.

_Good 👍🏻_

```luau
-- Закриваємо модальне віконечко при виникненні помилки.
toggleModal(false)
```

_Bad 👎🏻_

```luau
-- Hide modal window on error.
toggleModal(false)
```

### 💩 Try to mix formatting style as much as possible

Celebrate the difference.

_Good 👍🏻_

```luau
local i = {'tomato', 'onion', 'mushrooms'}
local d = { "ketchup", "mayonnaise" }
```

_Bad 👎🏻_

```luau
local ingredients = {"tomato", "onion", "mushrooms"}
local dressings = {"ketchup", "mayonnaise"}
```

### 💩 Put as much code as possible into one line

_Good 👍🏻_

```luau
game:GetService("Players").LocalPlayer.PlayerGui:FindFirstChild("ScreenGui"):FindFirstChild("Frame"):FindFirstChild("TextLabel").Text = "Hello"
```

_Bad 👎🏻_

```luau
local Players = game:GetService("Players")
local localPlayer = Players.LocalPlayer
local playerGui = localPlayer:WaitForChild("PlayerGui")
local screenGui = playerGui:WaitForChild("ScreenGui")
local frame = screenGui:WaitForChild("Frame")
local textLabel = frame:WaitForChild("TextLabel")

textLabel.Text = "Hello"
```

### 💩 Fail silently

Whenever you catch an error it is not necessary for anyone to know about it. No logs, no error modals, chill.

_Good 👍🏻_

```luau
pcall(function()
  -- Something unpredictable.
end)
```

_Bad 👎🏻_

```luau
local success, err = pcall(function()
  -- Something unpredictable.
end)
if not success then
  warn("Error occurred:", err)
end
```

### 💩 Use global variables extensively

Globalization principle.

_Good 👍🏻_

```luau
_G.x = 5

function square()
  _G.x = _G.x ^ 2
end

square() -- Now _G.x is 25.
```

_Bad 👎🏻_

```luau
local x = 5

local function square(num: number): number
  return num ^ 2
end

x = square(x) -- Now x is 25.
```

### 💩 Create variables that you're not going to use.

Just in case.

_Good 👍🏻_

```luau
local function sum(a, b, c)
  local timeout = 1300
  local result = a + b
  return a + b
end
```

_Bad 👎🏻_

```luau
local function sum(a, b)
  return a + b
end
```

### 💩 Don't specify types and/or don't do type checks if language allows you to do so.

_Good 👍🏻_

```luau
--!nocheck

local function sum(a, b)
  return a + b
end

-- Having untyped fun here.
local guessWhat = sum("hello", 123)
```

_Bad 👎🏻_

```luau
--!strict

local function sum(a: number, b: number): number?
  if type(a) ~= "number" or type(b) ~= "number" then
    return nil
  end
  return a + b
end

-- This one should fail during Luau type checking.
local guessWhat = sum("hello", 123)
```

### 💩 You need to have an unreachable piece of code

This is your "Plan B".

_Good 👍🏻_

```luau
local function square(num)
  if num == nil then
    return nil
  else
    return num ^ 2
  end
  return false -- This is my "Plan B".
end
```

_Bad 👎🏻_

```luau
local function square(num)
  if num == nil then
    return nil
  end
  return num ^ 2
end
```

### 💩 Triangle principle

Be like a bird - nest, nest, nest.

_Good 👍🏻_

```luau
local function onDamage(player, amount)
  if player then
    if player.Character then
      if player.Character:FindFirstChild("Humanoid") then
        task.spawn(function()
          if amount > 0 then
            for i = 1, 10 do
              if player.Character.Humanoid.Health > 0 then
                player.Character.Humanoid:TakeDamage(amount)
              end
            end
          end
        end)
      end
    end
  end
end
```

_Bad 👎🏻_

```luau
local function onDamage(player: Player, amount: number)
  if not player or not player.Character then return end
  local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
  if not humanoid or amount <= 0 then return end

  for i = 1, 10 do
    if humanoid.Health <= 0 then break end
    humanoid:TakeDamage(amount)
  end
end
```

### 💩 Mess with indentations

Avoid indentations since they make complex code take up more space in the editor. If you're not feeling like avoiding them then just mess with them.

_Good 👍🏻_

```luau
local fruits = {'apple',
  'orange', 'grape', 'pineapple'}
  local toppings = {'syrup', 'cream', 
                    'jam', 
                    'chocolate'}
local desserts = {}
for _, fruit in fruits do
for _, topping in toppings do
    table.insert(desserts, {
fruit,topping})
    end end
```

_Bad 👎🏻_

```luau
local fruits = {"apple", "orange", "grape", "pineapple"}
local toppings = {"syrup", "cream", "jam", "chocolate"}
local desserts = {}

for _, fruit in fruits do
  for _, topping in toppings do
    table.insert(desserts, {fruit, topping})
  end
end
```

### 💩 Do not lock your dependencies

Update your dependencies on each new installation in uncontrolled way. Why stick to the past, let's use the cutting edge libraries versions.

_Good 👍🏻_

```
$ ls -la

wally.toml
```

_Bad 👎🏻_

```
$ ls -la

wally.toml
wally.lock
```

### 💩 Always name your boolean value a `flag`

Leave the space for your colleagues to think what the boolean value means.

_Good 👍🏻_

```luau
local flag = true
```

_Bad 👎🏻_

```luau
local isDone = false
local isEmpty = false
```

### 💩 Long-read functions are better than short ones.

Don't divide a program logic into readable pieces. What if your IDE's search breaks and you will not be able to find the necessary file or function?

- 10000 lines of code in one file is OK.
- 1000 lines of a function body is OK.
- Dealing with many services (DataStores, Player Messaging, Combat, UI logic, Leaderstats, hand-written Inventory ORM and TweenService animations) in one `MainScript.server.lua`? It's OK.

### 💩 Avoid covering your code with tests

This is a duplicate and unnecessary amount of work. (Why use TestEZ or Jest-Lua?)

### 💩 As hard as you can try to avoid code linters

Write code as you want, especially if there is more than one developer in a team. Avoid tools like Selene, Luau-LSP, or StyLua. This is a "freedom" principle.

### 💩 Start your project without a README file.

And keep it that way for the time being.

### 💩 You need to have unnecessary code

Don't delete the code your app doesn't use. At most, comment it.

---

### 💩 Use the `wait()` global forever

The `task` library is for cowards. `wait()`, `delay()`, and `spawn()` are deprecated? Nonsense, they build character and add that sweet, unpredictable throttle to your game loop.

_Good 👍🏻_

```luau
while true do
  wait(0.1)
  if math.random() > 0.5 then
    spawn(function()
      print("Maybe this runs tomorrow")
    end)
  end
end
```

_Bad 👎🏻_

```luau
local RunService = game:GetService("RunService")

RunService.Heartbeat:Connect(function(dt)
  if math.random() > 0.5 then
    task.spawn(function()
      print("Ran immediately")
    end)
  end
end)
```

### 💩 Use `game.Workspace` instead of `workspace`

Why use the globally available, optimized `workspace` keyword when you can chain property lookups from the root `game` object every single time?

_Good 👍🏻_

```luau
game.Workspace.Map.Castle.Tower.Brick.Color = Color3.new(1, 0, 0)
```

_Bad 👎🏻_

```luau
workspace.Map.Castle.Tower.Brick.Color = Color3.new(1, 0, 0)
```

### 💩 The God RemoteEvent

Why create multiple RemoteEvents for different actions when you can send a massive string and a dictionary of untyped garbage through a single `RemoteEvent`? 

_Good 👍🏻_

```luau
-- Client
local event = game.ReplicatedStorage:WaitForChild("Event")
event:FireServer("Attack", {target = "Dragon", damage = 999, isCrit = true, vector = Vector3.new(1,2,3), randomString = "hello"})

-- Server
event.OnServerEvent:Connect(function(player, action, data)
  if action == "Attack" then
    -- Do something with data.target, data.damage, etc.
    -- I hope the client sent the right types!
  elseif action == "Move" then
    -- ...
  elseif action == "Respawn" then
    -- ...
  end
end)
```

_Bad 👎🏻_

```luau
-- Client
local attackEvent = game.ReplicatedStorage:WaitForChild("AttackEvent")
attackEvent:FireServer(target, damage, isCrit)

-- Server
attackEvent.OnServerEvent:Connect(function(player: Player, target: Instance, damage: number, isCrit: boolean)
  -- ...
end)
```

### 💩 Parent instances before setting their properties

Why set properties in memory before they exist in the DataModel? Let the engine repaint and recalculate physics for the instance on every single property change. It creates jobs for the CPU.

_Good 👍🏻_

```luau
local part = Instance.new("Part")
part.Parent = workspace
part.Size = Vector3.new(10, 10, 10) -- Engine recalculates
part.Position = Vector3.new(0, 50, 0) -- Engine recalculates again
part.Anchored = true -- Engine recalculates again
part.BrickColor = BrickColor.new("Really red") -- Engine recalculates again
```

_Bad 👎🏻_

```luau
local part = Instance.new("Part")
part.Size = Vector3.new(10, 10, 10)
part.Position = Vector3.new(0, 50, 0)
part.Anchored = true
part.BrickColor = BrickColor.new("Really red")
part.Parent = workspace -- Engine calculates once
```

### 💩 Never use `GetService`

Direct indexing on the `game` object is the hallmark of a true veteran. Who needs future-proofing or thread-safety?

_Good 👍🏻_

```luau
local players = game.Players
local repStorage = game.ReplicatedStorage
local runService = game.RunService
```

_Bad 👎🏻_

```luau
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
```

### 💩 String concatenation inside loops

The garbage collector has been slacking off lately. Give it some work by creating thousands of intermediate string objects.

_Good 👍🏻_

```luau
local msg = ""
for i = 1, 1000 do
  msg = msg .. "Number: " .. tostring(i) .. ", "
end
print(msg)
```

_Bad 👎🏻_

```luau
local msgParts = {}
for i = 1, 1000 do
  table.insert(msgParts, "Number: " .. tostring(i) .. ", ")
end
local msg = table.concat(msgParts)
print(msg)
```

### 💩 Connect events in loops without ever disconnecting them

Memory leaks are just features that keep the game dynamic. If the player clicks the button 100 times, the function *should* fire 100 times simultaneously.

_Good 👍🏻_

```luau
local button = script.Parent

button.MouseButton1Click:Connect(function()
  print("Clicked once!")
end)

-- Wait, what if they click it again?
button.MouseButton1Click:Connect(function()
  print("Clicked twice!")
end)

-- Just to be safe, let's connect it in a loop
for i = 1, 10 do
  button.MouseButton1Click:Connect(function()
    print("Clicked " .. tostring(i) .. " times!")
  end)
end
```

_Bad 👍🏻_

```luau
local button = script.Parent
local connection

connection = button.MouseButton1Click:Connect(function()
  print("Clicked!")
  connection:Disconnect()
end)
```

### 💩 Reinvent the standard library

Don't trust Luau's built-in `math.clamp`, `math.round`, or `table.find`. Write your own bloated, inefficient versions. It shows how smart you are.

_Good 👍🏻_

```luau
local function customClamp(val, min, max)
  if val < min then
    return min
  elseif val > max then
    return max
  else
    return val
  end
end

local function customRound(num)
  local floor = math.floor(num)
  if num - floor >= 0.5 then
    return floor + 1
  else
    return floor
  end
end
```

_Bad 👎🏻_

```luau
local clampedVal = math.clamp(val, min, max)
local roundedVal = math.round(num)
```

### 💩 Abuse the Fake Ternary Operator

Luau doesn't have a real ternary operator, so chain `and`/`or` as much as possible to create unreadable, bug-prone logic. Don't worry about `nil` or `false` falsy values breaking it!

_Good 👍🏻_

```luau
local state = isMoving and isRunning and "Run" or isMoving and "Walk" or "Idle"
local color = (health == 0 and "Dead") or (health < 10 and "Critical") or "Alive"
local target = enemy and (enemy:IsA("Humanoid") and enemy or enemy:FindFirstChild("Humanoid")) or nil
```

_Bad 👎🏻_

```luau
local state = "Idle"
if isMoving then
  state = isRunning and "Run" or "Walk"
end
```

### 💩 Use `FindFirstChild` instead of the `.` operator

You never know when an instance might spontaneously combust, so use `FindFirstChild` and `WaitForChild` for *everything*, even your own variables and immediate children.

_Good 👍🏻_

```luau
local player = game:GetService("Players"):FindFirstChild("Player1")
if player and player:FindFirstChild("Character") and player.Character:FindFirstChild("Humanoid") then
  local hrp = player.Character:FindFirstChild("HumanoidRootPart")
  hrp:FindFirstChild("CFrame")
end
```

_Bad 👎🏻_

```luau
local player = game.Players.Player1
if player and player.Character and player.Character:FindFirstChild("Humanoid") then
  local hrp = player.Character.HumanoidRootPart
  hrp.CFrame = CFrame.new(0, 10, 0)
end
```

### 💩 Mix Array and Dictionary keys in the same table

Why separate your arrays and maps? Mix integers and strings in the same table. It forces the next developer to use `pairs()` and manually check the types of keys.

_Good 👍🏻_

```luau
local myData = {
  "Player1",
  "Player2",
  admin = true,
  500,
  health = 100,
  [{}] = "What am I doing?"
}
```

_Bad 👎🏻_

```luau
local playersArray = {"Player1", "Player2", 500}
local playerInfo = {
  admin = true,
  health = 100
}
```

### 💩 Trust the Client entirely

ServerScriptService is just a suggestion. Put all your RemoteEvent logic, data saving, and anti-cheat in `StarterPlayerScripts`. The client is your friend, they would never exploit your game!

_Good 👍🏻_

```luau
-- StarterPlayerScripts.ClientAntiCheat
local player = game.Players.LocalPlayer
local humanoid = player.Character:WaitForChild("Humanoid")

humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
  if humanoid.WalkSpeed > 16 then
    player:Kick("Speed hacking!")
  end
end)
```

_Bad 👎🏻_

```luau
-- ServerScriptService.AntiCheat
local function monitorPlayer(player: Player)
  local character = player.Character
  if not character then return end
  local humanoid = character:WaitForChild("Humanoid")
  
  humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
    if humanoid.WalkSpeed > 16 then
      player:Kick("Speed hacking!")
    end
  end)
end
```
