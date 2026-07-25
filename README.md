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
