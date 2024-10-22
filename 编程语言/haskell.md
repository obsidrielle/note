# 1. 安装
```
yay -S ghc
yay -S ghcup-hs-bin
```

# 2. 基本语法

```haskell
1 + 2
True && False
3 == 5
```

函数调用

```haskell
succ 2 + max 5 3 + 1
```

函数定义
```haskell
power x = x * x
```

分支结构
```haskell
abs_ x = if x > 0 then x else -x
```

列表操作
```haskell
[1, 2, 3] ++ [4, 5, 6]  -- 拼接列表
"A" : "SMALL CAT" -- 前插入
[1, 2, 3] !! 1 -- 索引元素
[4, 5, 6] > [1, 2, 3] -- 按字典序比较元素
head [1, 2, 3] -- 返回第一个元素
tail [1, 2, 3] -- 返回最后一个元素
init [1, 2, 3] -- 返回除去最后一个元素的liebiao
length [1, 2, 3] -- 返回长度
null [1, 2, 3] -- 检查是否为空
reverse [1, 2, 3] -- 反转
take 3 [1, 2, 3] -- 取前几个元素
drop 1 [1, 2, 3] -- 删除前几个元素
minimum [1, 2, 3] -- 返回最小元素
maximum [1, 2, 3] -- 返回最大元素
sum [1, 2, 3] -- 返回和
product [1, 2, 3] -- 返回积

```

使用区间 ($\mathrm{Range}$)
```haskell
[1..20] -- 1, 2, ..., 20
['a'..'z'] -- a, b, ..., z
[2, 1..20] -- 1, 3, 5, ..., 20
[1..] -- 无限长度的 list
take 10 (cycle [1, 2, 3])
take 10 (repeat 5)
```

使用集合
```haskell
[x * 2 | x <- [1..10]]
[x * 2 | x <- [1..10], x * 2 >= 12]
[x | x <- [1..100], x `mod` 7 == 3]
[x * y | x <- [1..100], y <- [1..200], x * y >= 40, x > y]
```

使用元组
```haskell
fst (1, 2) -- 返回一个 pair 的首项
snd (1, 2) -- 返回一个 pair 的尾项
zip [1, 2, 3] [1, 2, 3] 
let triangle = [(a, b, c) | c <- [1..100], b <- [1..c], a <- [1..b], c^2 == a^2 + b^2]
```

# 3. 类型系统
使用 `:t` 返回一个表达式的类型

```haskell
:t 'a' -- 'a' :: Char
```

可以使用 `::` 给定义加上类型说明

```haskell
power x :: Int -> Int
power x = x * x
```

常见的类型
- `Int` 整形
- `Bool` 布尔
- `Integer` 无上限的整形
- `Float` 单精度
- `Double` 双精度
- `String` 字符串

## 3.1 型别 ($\mathrm{typeclass}$)
类似于 `java` 的接口。

```haskell
:t (==)
-- (==) :: (Eq a) => a -> a -> Bool
```

常用的型别
- `Eq a` 判断相等
- `Ord a` 比较
- `Show a` 转换为字符串
- `Read a` 从字符串转换为其他，如果后面有已知变量不需要显式指定，否则需要
```haskell
read "True" || False
read "5" :: Int
```
- `Enum a` 可枚举，如 `[1, 2, 3, ...]`
- `Bounded` 有上下限，如 `Int`
- `Num` 具有数字特征
- `Integral` 包含实数
- `Floating` 包含浮点数

`fromIntegral` 函数可以将一个整数转换为浮点数

# 4. 函数
模式匹配

```haskell
factorial :: (Integral a) => a -> a
factorial 0 = 1
factorial n = n * factorial (n - 1)
```

也可以对元组进行模式匹配

```haskell
addVector2 :: (Num a) => (a, a) -> (a, a) -> (a, a)
addVector2 (x1, x2) (y1, y2) = (x1 + y1, x2 + y2)
```

也可以对列表进行模式匹配

```haskell
head' :: [a] -> a
head' (x:xs) = x
```
将 `x` 绑定到头部，`xs` 绑定到其他元素

```haskell
tell :: (Show a) => [a] -> String  
tell [] = "The list is empty"  
tell (x:[]) = "The list has one element: " ++ show x  
tell (x:y:[]) = "The list has two elements: " ++ show x ++ " and " ++ show y  
tell (x:y:_) = "This list is long. The first two elements are: " ++ show x ++ " and " ++ show y
```

如果是 `x:y:_` 会匹配两元素和多元素的数组

对匹配出的模式进行绑定

```haskell
capital :: String -> String  
capital "" = "Empty string, whoops!"  
capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]
```

## 4.1 $\mathrm{Guard}$
$\mathrm{guard}$ 由跟在函数名及参数后面的竖线标志，如果为真，就使用其对应的函数体。如果为假，执行下一个。

```haskell
bmiTell :: (RealFloat a) => a -> a -> String  
bmiTell weight height  
    | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"  
    | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"  
    | otherwise                 = "You're a whale, congratulations!"
```

## 4.2 $\mathrm{where}$ 
使用 $\mathrm{where}$ 在模式匹配的最后定义绑定

```haskell
bmiTell :: (RealFloat a) => a -> a -> String  
bmiTell weight height  
    | bmi <= skinny = "You're underweight, you emo, you!"  
    | bmi <= normal = "You're supposedly normal. Pffft, I bet you're ugly!"  
    | bmi <= fat    = "You're fat! Lose some weight, fatty!"  
    | otherwise     = "You're a whale, congratulations!"  
    where bmi = weight / height ^ 2  
          skinny = 18.5  
          normal = 25.0  
          fat = 30.0
```

## 4.3 $\mathrm{let}$
使用 $\mathrm{let}$ 定义局部变量

```haskell
cylinder :: (RealFloat a) => a -> a -> a  
cylinder r h = 
    let sideArea = 2 * pi * r * h  
        topArea = pi * r ^2  
    in  sideArea + 2 * topArea
```
在 $\mathrm{let}$ 中定义的名字仅仅在 $\mathrm{in}$ 中可见
$\mathrm{let}$ 绑定是一个表达式
不同的 $\mathrm{let}$ 使用分号隔开

```haskell
ghci> (let a = 100; b = 200; c = 300 in a*b*c, let foo="Hey "; bar = "there!" in foo ++ bar)  
(6000000,"Hey there!")
```

## 4.4 $\mathrm{case}$
$\mathrm{case}$ 是模式匹配的语法糖，但是 $\mathrm{case}$ 作为一个表达式可以用在任何地方

```haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ case xs of [] -> "empty."  
                                               [x] -> "a singleton list."   
                                               xs -> "a longer list."
```

```haskell
describeList :: [a] -> String  
describeList xs = "The list is " ++ what xs  
    where what [] = "empty."  
          what [x] = "a singleton list."  
          what xs = "a longer list."
```

# 5. 高阶函数
$\mathrm{Haske ll}$ 的函数支持柯里化，对一个有多个参数的函数部分调用就可以得到一个部分调用的函数。一个返回函数的函数就是高阶函数。

```haskell
applyTwice :: (a -> a) -> a -> a  
applyTwice f x = f (f x)
```

## 5.1 $\mathrm{lambda}$
$\mathrm{lambda }$ 是匿名函数，使用 `\` 定义

```haskell
numLongChains :: Int  
numLongChains = length (filter (\xs -> length xs > 15) (map chain [1..100]))
```

```haskell
addThree :: (Num a) => a -> a -> a -> a  
addThree = \x -> \y -> \z -> x + y + z
```

## 5.2 $
$是右结合的，优先级最低, 相当于在右边写了一对括号

```haskell
f $ a $ b $ c -- f (a (b c))
```

### 5.3 函数组合
使用 `.` 来右结合的组合出一个新的函数

```haskell
ghci> map (negate . abs) [5,-3,-6,7,-3,2,-19,24]  
[-5,-3,-6,-7,-3,-2,-19,-24] -- 先 abs 再 negate
```

	