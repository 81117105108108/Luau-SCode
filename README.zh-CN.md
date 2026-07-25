# Luau 垃圾代码书写准则

[![State-of-the-art Shitcode](https://img.shields.io/static/v1?label=State-of-the-art&message=Shitcode&color=7B5804)](https://github.com/trekhleb/state-of-the-art-shitcode)

这是一个你的 Roblox/Luau 项目应该遵循的垃圾代码书写准则的列表，把被称为适当的垃圾代码。

_Read this in other languages:_
[_English_](README.md),
[_한국어_](README.ko-KR.md)

## 获取徽章

如果你的仓库遵循垃圾代码书写准则，你应该用下面的"state-of-the-art shitcode" 徽章：

[![State-of-the-art Shitcode](https://img.shields.io/static/v1?label=State-of-the-art&message=Shitcode&color=7B5804)](https://github.com/trekhleb/state-of-the-art-shitcode)

标记徽章的源代码:

```
[![State-of-the-art Shitcode](https://img.shields.io/static/v1?label=State-of-the-art&message=Shitcode&color=7B5804)](https://github.com/trekhleb/state-of-the-art-shitcode)
```

## 准则

### 💩 以一种代码已经被混淆的方式命名变量

如果我们键入的东西越少，那么就有越多的时间去思考代码逻辑等问题。

_Good 👍🏻_

```luau
local a = 42
```

_Bad 👎🏻_

```luau
local age = 42
```

### 💩 变量/函数混合命名风格

为不同庆祝一下。

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

### 💩 不要写注释

反正没人会读你的代码。

_Good 👍🏻_

```luau
local cdr = 700
```

_Bad 👎🏻_

更多时候，注释应该包含一些“为什么”，而不是一些“是什么”。如果“是什么”在代码中不清楚，那么代码可能太混乱了。

```luau
-- 700ms的数量是根据UX A/B测试结果进行经验计算的。
-- @查看: <详细解释700的一个链接>
local callbackDebounceRate = 700
```

### 💩 使用母语写注释

如果您违反了“无注释”原则，那么至少尝试用一种不同于您用来编写代码的语言来编写注释。如果你的母语是英语，你可能会违反这个原则。

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

### 💩 尽可能混合不同的格式

为不同庆祝一下。

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

### 💩 尽可能把代码写成一行

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

### 💩 不要处理错误

无论何时发现错误，都没有必要让任何人知道它。没有日志，没有错误弹框。

_Good 👍🏻_

```luau
pcall(function()
  -- 意料之外的情况。
end)
```

_Bad 👎🏻_

```luau
local success, err = pcall(function()
  -- 意料之外的情况。
end)
if not success then
  warn("Error occurred:", err)
end
```

### 💩 广泛使用全局变量

全球化的原则。

_Good 👍🏻_

```luau
_G.x = 5

function square()
  _G.x = _G.x ^ 2
end

square() -- 现在_G.x是25
```

_Bad 👎🏻_

```luau
local x = 5

local function square(num: number): number
  return num ^ 2
end

x = square(x) -- 现在x是25
```

### 💩 创建你不会使用的变量

以防万一。

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

### 💩 如果语言允许，不要指定类型和/或不执行类型检查。

_Good 👍🏻_

```luau
--!nocheck

local function sum(a, b)
  return a + b
end

-- 在这里享受没有类型的快乐
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

-- 这个应该在 Luau 类型检查期间失败。
local guessWhat = sum("hello", 123)
```

### 💩 你应该有不能到达的代码

这是你的 "Plan B".

_Good 👍🏻_

```luau
local function square(num)
  if num == nil then
    return nil
  else
    return num ^ 2
  end
  return false -- 这就是我的"Plan B".
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

### 💩 三角法则

就像鸟巢，鸟巢，鸟巢。

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

### 💩 混合缩进

避免缩进，因为它们会使复杂的代码在编辑器中占用更多的空间。如果你不喜欢回避他们，那就和他们捣乱。

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

### 💩 不要锁住你的依赖项

以非受控方式更新每个新安装的依赖项。为什么坚持使用过去的版本，让我们使用最先进的库版本。

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

### 💩 总是把布尔值命名为 `flag`

留出空间给你的同事思考布尔值的含义。

_Good 👍🏻_

```luau
local flag = true
```

_Bad 👎🏻_

```luau
local isDone = false
local isEmpty = false
```

### 💩 函数长的比短的好

不要把程序逻辑分成可读的部分。如果 IDE 的搜索停止，而您无法找到所需的文件或函数，该怎么办?

- 一个文件中 10000 行代码是 OK 的。
- 一个函数体有 1000 行代码是 OK 的。
- 在一个 `MainScript.server.lua` 中处理许多服务（DataStores、玩家消息、战斗系统、UI 逻辑、Leaderstats、手写的背包数据和 TweenService 动画）? 这是 OK 的。

### 💩 不要测试你的代码

这是重复且不需要的工作。（为什么要用 TestEZ 或 Jest-Lua 呢？）

### 💩 避免代码风格统一 / 避免代码检查工具

编写您想要的代码，特别是在一个团队中有多个开发人员的情况下。请避免使用 Selene、Luau-LSP 或 StyLua 等工具。这是“自由”原则。

### 💩 构建新项目不需要 README 文档

一开始我们就应该保持。

### 💩 保存不必要的代码

不要删除不用的代码，最多注释掉。
