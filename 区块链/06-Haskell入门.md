# Haskell

vscode安装3个插件：

![2025-04-06-01-05-50.png](./images/2025-04-06-01-05-50.png)

ghc编译haskell慢，可以安装下这个插件：

![2025-04-06-01-06-36.png](./images/2025-04-06-01-06-36.png)


## 入门
Haskell 是一种编译型语言，但具有某些类似解释型语言的特性。

编程环境使用GHC
- ghc 生成本地原生代码的优化编译器。（相当于C#的Roslyn）
- ghci 一个交互解析器和调试器。（相当于python.exe就是一个交互式窗口）
- runghc是一个以脚本形式(并不要首先编译)运行Haskell代码的程序

https://www.haskell.org/ghcup/
根据指示在Window下下载ghcup（推荐手动安装）

### ghci

导入模块：
![2025-04-08-22-28-09.png](./images/2025-04-08-22-28-09.png)

列表：
![2025-04-08-22-28-37.png](./images/2025-04-08-22-28-37.png)

字符串：
![2025-04-08-22-35-06.png](./images/2025-04-08-22-35-06.png)


- haskell没有定义0为False，非0为True

开启/关闭类型和求值结果的自动显示
```
 :set +t
 :unset +t
```

### 类型
- 所有类型名字都以大写字母开头
- 所有变量名字都以小写字母开头

特点：
- 强类型
- 静态
  - 编译器可以在编译期（而不是执行期）知道每个值和表达式的类型
- 可以通过自动推导得出


![2025-04-08-23-07-10.png](./images/2025-04-08-23-07-10.png)

- it 这是一个有特殊用途的变量， ghci将最近一次求值所得的结果保存在这个变量里（是ghci一个辅助功能，不是haskell有的）
- x :: y 表示表达式 x 的类型为 y

#### 基本类型：
- Char
- Bool
- Int
  - 带符号的定长整数
- Integer
  - 不限长度的带符号整数
- Double

![2025-04-09-21-34-36.png](./images/2025-04-09-21-34-36.png)


#### 复合数据类型：
- 列表
  - 列表可以任意长，且只能包含类型相同的值
- 元组
  - 元组的长度是固定的，但可以包含不同类型的值
  - 只要元素的结构和类型都一致，那么元组的类型就是相同的
  - 一般使用的场景
    - 一个函数需要返回多个值的时候
    - 需要定长容器，但又没有必要使用自定义类型的时候

![2025-04-09-22-07-19.png](./images/2025-04-09-22-07-19.png)


#### 自定义类型：
类比class

```hs
-- data 关键字
-- BookInfo 类型构造器，新类型的名字
-- Book 值构造器（数据构造器）， 类型的值就是由值构造器创建的，可以理解成是“构造函数” 返回BookInfo这个类型的“对象”
data BookInfo = Book Int String [String]
                deriving (Show)

-- 源码文件不能省略let， ghci可以
let myInfo = Book 1001 "tom" ["aa", "bb"]
```

![2025-04-10-00-02-51.png](./images/2025-04-10-00-02-51.png)

- 在 Haskell 里，类型的名字（类型构造器）和值构造器的名字是相互独立的。类型构造器只能出现在类型的定义，或者类型签名当中。而值构造器只能出现在实际的代码中。因为存在这种差别，给类型构造器和值构造器赋予一个相同的名字实际上并不会产生任何问题

类型别名：
```hs
type BookId = Int
type BookName = String
type Author = [String]

data BookInfo = Book BookId BookName Author


data BetterReview = BetterReview BookInfo CustomerID ReviewBody
```

#### 代数数据类型 (也是自定义类型)：
一个代数类型可以有多于一个值构造器，可以理解为C#里面的enum（感觉更像是switch，或者是多态）
```hs
data Bool = False | True

type CardNumber = String
type CardHolder = String
type CustomerId = Int

data BillingInfo = CreditCard CardNumber CardHolder
                    | CashOnDelivery
                    | Invoice CustomerId
                    deriving (Show)

let card = CreditCard "2901650221064486" "Thomas Gradgrind" ["Dickens", "England"]
```


#### 模式匹配
其实就是函数名一样，参数不一样而已（重载）

![2025-04-11-22-54-11.png](./images/2025-04-11-22-54-11.png)

（在源码才能有这个效果，ghci不行，会变成覆盖）

![2025-04-11-23-08-01.png](./images/2025-04-11-23-08-01.png)

组成和解构：
```hs
complicated (True, a, x:xs, 5) = (a, xs)
complicated (True, 1, [1, 2, 3], 5) -- (1,[2,3])
-- (x:xs) 或是 (d:ds) 这种类型的名字，是一个流行的命名规则
--  s 表示元素的复数，用 x 来表示列表的第一个元素，剩余的列表元素则用 xs 表示
```

```hs
bookID      (Book id title authors) = id
let book = (Book 3 "Probability Theory" ["E.T.H. Jaynes"])
bookID book -- 3
```

如果在匹配模式中我们不在乎某个值的类型，那么可以用下划线字符 “_” 作为符号来进行标识，它也叫做*通配符*
```hs
nicerID      (Book id _     _      ) = id
```

可以用通配符定义一个默认行为
```hs
sumList (x:xs) = x + sumList xs
sumList _  = 0
```

给每一个数据类型写访问器函数很枯燥，重复又是必须的，这个叫做样板代码
```hs
nicerID      (Book id _     _      ) = id
nicerTitle   (Book _  title _      ) = title
nicerAuthors (Book _  _     authors) = authors
```

#### 记录语法 *
haskell提供更加便捷的写法（记录语法）：
```hs
type BookId = Int

-- type BookName = String
-- type Author = [String]
-- data BookInfo = Book BookId BookName Author deriving (Show)

data Book = Book{
  bookId :: BookId,
  bookName :: String,
  author :: [String]
}deriving (Show)

-- 可以类比C#的{get;set;}了

-- myInfo = Book 9780135072455 "Algebra of Programming"["Richard Bird", "Oege de Moor"]

-- 可以这样赋值
myInfo = Book{
  bookName = "Algebra of Programming",
  bookId = 9780135072455,
  author = ["Richard Bird", "Oege de Moor"]
}
```

#### 参数化类型
![2025-04-12-02-16-48.png](./images/2025-04-12-02-16-48.png)

#### 递归类型
```hs
-- 定义类型
data List a = Cons a (List a)
            | Nil
              deriving (Show)

-- 定义方法
fromList (x:xs) = Cons x (fromList xs)
```

![2025-04-12-03-17-08.png](./images/2025-04-12-03-17-08.png)

### 变量
#### 局部变量
let...in...：
```hs
-- let 关键字标识一个变量声明区块的开始，用 in 关键字标识这个区块的结束
-- 在 let 区块内定义的变量，既可以在定义区内使用，也可以在紧跟着 in 关键字的表达式中使用

lend amount balance = let reserve = 100
                          newBalance = balance - amount
                      in if balance < reserve
                         then Nothing
                         else Just newBalance


{-lend amount balance = let reserve = 100 ;newBalance = balance - amount
                      in if balance < reserve
                         then Nothing
                         else Just newBalance -}
```

屏蔽：
```hs
-- 内部的 x 隐藏了，或称作屏蔽（shadowing）
foo = let x = 1
      in ((let x = "foo" in x), x)
```

where：
```hs
lend2 amount balance = if balance < reserve
                       then Nothing 
                       else Just newBalance
                  where reserve = 100
                        newBalance = balance - amount
```

![2025-04-12-05-20-56.png](./images/2025-04-12-05-20-56.png)

#### 全局变量
```hs
-- globalVar :: Int
globalVar = 42 
```

#### 闭包变量

#### 参数变量
```hs
foo x = x + 1  -- x是参数变量，作用域限于foo函数内部
```


### 函数

- 函数参数不需要括号包围，参数与参数之间不需要逗号
- 一些复杂的就需要括号让编译器知道这是一个参数
- 表达式用作另一个表达式的参数，需要用括号
- 纯函数
  - 副作用 函数的行为受系统的全局状态所影响
  - Haskell 的函数在默认情况下都是无副作用的
  - 将带副作用的函数称为“不纯（impure）函数”，而将不带副作用的函数称为“纯（pure）函数”
  - 不纯的本质  依赖外部状态
    - 相同输入可能返回不同结果
  - 不纯函数的类型签名都以 IO 开头
  - 一种常见的 Haskell 风格就是，将带有副作用的代码和不带副作用的代码分开处理。在这种情况下，不纯函数需要尽可能地简单，而复杂的任务则交给纯函数去做
- 多态
  - **参数多态**   像last这类函数，不在乎列表元素类型的，这种函数时多态的，这种多态称为参数多态 [Char]->Char, [int]->int ...
  - （强制多态   允许值在类型之间进行隐式的转换，  Haskell 坚决反对自动类型转换，所以haskell不支持这种多态）



![2025-04-09-21-37-27.png](./images/2025-04-09-21-37-27.png)

![2025-04-09-22-12-26.png](./images/2025-04-09-22-12-26.png)

![2025-04-09-22-14-40.png](./images/2025-04-09-22-14-40.png)

![2025-04-09-22-58-57.png](./images/2025-04-09-22-58-57.png)

haskell源码，函数定义：

![2025-04-09-23-03-20.png](./images/2025-04-09-23-03-20.png)

```hs
-- 变量
-- 一旦变量绑定了某个表达式，那么这个变量的值就不会改变
x = 10
y = 20
-- x = 50  错误写法，不能再赋值
```

```hs
-- 条件表达式
myDrop n xs = if n <= 0 || null xs
              then xs
              else myDrop (n-1) (tail xs)

-- 变量 xs 展示了一个命名列表的常见模式： s 可以视为后缀，而 xs 则表示“复数个 x ”
```

![2025-04-09-23-16-23.png](./images/2025-04-09-23-16-23.png)

## 函数式编程
### 循环
Haskell 既没有 for 循环，也没有 while 循环
#### 显示递归
```hs
-- 只载入 Data.Char 中的 digitToInt 函数
-- 用于将单个数字字符（0-9、A-F、a-f）转换成对应的整数值（0-15）
import Data.Char (digitToInt)

loop :: Int -> String -> Int -- 函数签名
-- 定义函数
loop acc [] = acc
loop acc (x:xs) = let acc' = acc * 10 + digitToInt x
                  in loop acc' xs

-- 定义函数
asInt xs = loop 0 xs

-- asInt "12" => loop 0 "12" => loop (0*10 + 1) "2" => loop 1 "2" => loop (1*10+2) [] => 12

```

#### 对列表元素进行转换
```hs
square :: [Double] -> [Double]

square (x:xs) = x * x : square xs
square [] = []

-- square [1,2,3,4]
-- [1.0,4.0,9.0,16.0]
```

#### 列表映射 map
```hs
square2 :: [Double] -> [Double]

square2 xs = map squareOne xs
             where squareOne x = x * x
```
![2025-04-12-06-58-22.png](./images/2025-04-12-06-58-22.png)

#### 筛选列表元素
```hs
-- 筛选出奇数 if-then-else版
oddList :: [Int] -> [Int]

oddList (x:xs) = 
  if odd x then x : oddList xs
  else oddList xs

oddList [] = []

-- 更简便写法  Guard版
oddList2 :: [Int] -> [Int]
oddList2 (x:xs) 
  | odd x = x : oddList2 xs
  | otherwise = oddList2 xs

oddList2 [] = []


-- oddList [1, 2, 3, 4,5]
-- [1,3,5]
```
