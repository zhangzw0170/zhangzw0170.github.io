---
title: 2024 春夏季开源操作系统训练营 - 第一阶段总结
date: 2024-04-29 23:21:00 +0800
categories: [编程与算法, Rust]
tags: [Rust 语法, 内存安全]
---

> 备注：当时的 Github ID 为 **Zhangzw999**，原文发布在 [rCore-OS Blog](https://rcore-os.cn/blog/2024/04/29/Learning-OS-2024-Spring-Phase-1-Final-Report-Blog-From-Zhangzw999/)

## 前言

笔者初二开始接触编程，当时还只会写一点 C 和 Python。后来上了初三，遇见疫情，在家无聊，出于兴趣开始捣鼓一些复杂点的项目，但始终没有接触到本质的东西。这次的训练营是个好机会，不仅能让我深入接触到一门编程语言的工作方式，还能让我增进对操作系统原理的了解。

作为一名电子信息类专业的大一学生，我明白自己的时间和能力都极其有限，但我仍然愿意为了自己的兴趣，也为了将来工作多一份可能的机会，来参加开源操作系统训练营。我相信这次经历会成为我的一段难忘的回忆。

## 第一阶段：110 道 Rust 题目的摸爬滚打

### 一、变量 Variables

- 变量篇简简单单就过去了，区别就是 Rust 中的可变变量要**显式**声明。
- 常量必须**明确指定类型**：
  ```rust
  const a: i32 = 0;
  ```
- 可以用大括号限定变量的作用域，作用域之外的变量会被回收。
- 输出时可以直接在大括号内指定变量名，如：
  ```rust
  println!("{name}");
  ```
- 可以借助元组一次赋多个值：
  ```rust
  let (s1, s2) = ("Hello", "World");
  ```
- 定义有重复元素的数组：
  ```rust
  [3; 5] == [3, 3, 3, 3, 3]
  ```

### 二、函数 Functions

- 函数签名：
  ```rust
  [pub] fn function_name(para: type, ...) -> return_type
  [where T: ...] // 泛型实现的 Trait 要求
  { ... }
  ```
- `fn` 的返回值一般是在函数末尾不加分号的语句，以及 `return ...;` 语句。
- **注**：`fn` 不需要像 C 一样要先有独立的函数声明，直接定义即可。

### 三、条件控制流 Control Flow

#### 3.1 if 语句

基本语法：
```rust
if condition1 {
    // ...
} else if condition2 {
    // ...
} else {
    // ...
}
```
- 当 if **有返回值**时（各分支最后一条语句无分号），允许**将整个 if 语句作为右值**。
- 类似 C 的条件运算符 `A ? <condition> : <...>` 的增强版。

#### 3.2 loop 无条件循环

```rust
loop { ... } // 无限循环
```

#### 3.3 标签 Labels

- `'<name>:` 指定标签，用于跳转，可用于跳出循环，方法：
  ```rust
  break '<name>;
  continue '<>;
  ```
  **注意前面的单引号！**

#### 3.4 while 条件循环

```rust
while condition { ... }
```

#### 3.5 for 遍历循环

```rust
for n in (1..4).rev() { ... }
```
- `rev()`：反转数字序列
- 区间表达式：`(Start..=End)`

#### 3.6 match 语句

详见后文枚举类型 Enum。

#### 3.7 if let 简洁控制流

- 只匹配一个模式的值：
  ```rust
  if let Coin::Fen = coin { ... }
  // 可加入 else 处理其他情况
  ```

#### 3.8 while let

- 类似于 `if let`，只匹配一种情况，并以此作为循环变量进入循环。

### 四、内置类型 Primitive Types

#### 4.1 整型 Integer

- 前缀：`i` 有符号，`u` 无符号，8-128 以及 `isize` 和 `usize`（取决于架构）。
- **为方便阅读**，定义时可以在任意位置加入任意数量的 `_` 作为阅读分隔符：
  ```rust
  0b_1000_1000_0010_0101_u16
  ```

##### 整型溢出 Integer Overflow

- `--release` 模式下编译时，不会检查整数溢出。
- Rust 应对方法：
  1. `wrapping_*()`：回绕，溢出时返回正确结果对最大值取模
  2. `checked_*()`：检查，溢出时返回 `None`
  3. `overflowing_*()`：溢出，返回 `(结果, 是否溢出)`
  4. `saturating_*()`：饱和，返回最接近的范围内值

  - `*` 可用于：`add`, `sub`, `mul`, `div`, `rem`, `neg`, `abs`, `pow`, `shl`, `shr`

- 更多资料：[Rust 官方文档：运算符](https://doc.rust-lang.org/stable/book/appendix-02-operators.html)

#### 4.2 浮点型 Float

- `f32` 和 `f64`

#### 4.3 逻辑值 Bool

- `true` 和 `false`

#### 4.4 单字符 char

- Rust 字符类型为 **Unicode**，范围是 `U+0000 - U+D7FF, U+E000 - U+10FFFF`。
- **注意**：Rust 会将中文字符视作 “Alphabetic”（字母），处理时需特别留意。
- `b'A'`：这种情况下是 ASCII，仅 `u8`。

#### 4.5 数组 Array

```rust
let a = [1, 2, 4, 3, 5]; // 字面量
```

#### 4.6 数组切片 Slice

```rust
&array[1..=3]
// 获得数组中索引 1 <= index <= 3 的元素的切片
// 可以理解为获取子集
```
注意：尽量不要将切片用于 &str ，因为有的字符只占一个字节，但有的字符会占两个，如果两者在同一个&str中共存的话，就不知道该返回什么。

#### 4.7 元组 Tuple

```rust
let tuple1 = ("Foo", "Bar", "Baz");
let bar = tuple.1; // 元素访问
let (a, b, c) = tuple1; // 元组结构
// 此时 a,b,c 分别对应 "Foo", "Bar", "Baz"
// 此时所有权已经转移
```

#### 4.8 向量 Vectors

```rust
// let v: Vec<i32> = Vec::new();
let v = vec![1, 2, 4, 3, 5];
println!("向量 v: {:?} 的长度为 {}。", v, v.len()); // 输出数组长度 

println!("第一个元素：{}", v[1]); 
// 此处执行了隐式的类型转换：v -> &v
// 访问元素也可以用 v.get(1)

v.push(6) // 向 vec 中压入新元素，需要 &mut 
// 原因：push时需要分配新内存以及将原来的部分复制到新的地方，
// 在执行该操作时需要避免其他对该对象的引用
// （因为可能指向已经被移动的原来部分导致“空指针“的出现）
// v.pop() 同理

for i in &v { ... } // 隐式转换：&v -> iter
vec -> iter （迭代器）
v.iter()     // 不能改变 v 中的元素
或者

v.iter_mut() // 可以改变 v 中的元素
```
返回 Option<T>（即 Some(n) / None）

更多资料：
Rust 官方文档：向量/列表

### 五、映射 Map

```rust
let mut iter = A.iter().map( |<Variable>| <Expr>/{ ... } )
```
`| ... |`：闭包（closure）

映射只用于已对元素完成其他操作后的情形，根据上面的语句生成iter后，可用iter.next()访问下一个元素。

### 六、所有权 Ownership

这部分是 Rust 的精髓。在 Rust 中，在同一时段内，所有值均有且仅有一个所有者（Owner），且值的生命周期与所有者相同。

```rust
A.drop(): 变量退出作用域
```

**Rust 对不同类型的所有权处理**：

- 对于占用空间明确/可以压入栈的类型（比如内置的数字类型），在形如 `let n2 = n1;` 的语句中，会重新创建一个值相同的对象，和其他语言一样；
- 而对于其他类型，在上述语句中，会将原来值的所有权移动（move）给之后的值，原来的值将退出生命周期。
  注：占用空间明确/可以压入栈的类型：Integer bool float char tuple（元组，条件：只包含已经实现Copy的类型）

- 对于传入函数的其他类型，如果没有返回值且之后也没有调用的话，则在函数调用完之后，这些类型的变量会退出生命周期；
- 如果有返回值，则会将返回值的所有权交还给调用该函数的上一级。

- 要执行更高成本的深度复制 deep copy，可使用clone()方法创建副本：`let s2 = s1.clone();`

#### 做题经验

- 若想在传递值时不改变所有权，可使用共享不可变引用 &A ，或者独占可变引用 &mut A。要访问引用中的值，可使用 *B。两者的作用域不能重合！！！

- 若一个函数的参数不为引用（没有 & 和 &mut 修饰），则在执行函数时，该参数的所有权会移交给函数，提供的参数在进入函数时失效（无法再通过原来的变量名访问）。

- 通过引用传入函数的其他类型，由于函数并没有传入对象的所有权，因而这些变 量在离开函数之后也不会退出生命周期。

- Dangling Reference（虚调用？）：如果一个变量在域内定义，则不能将其引用返 回域外，但可以将变量本身返回域外

#### & 和 ref 的区别：

1. `&T`：类型：T的引用，可用于变量声明、函数参数和模式匹配中传递引用类型
2. `ref T`：主要用于模式匹配，将值绑定在该引用上
  （获取到的类型仍然是T）

- 函数参数可以使用 mut 修饰。

- Rust 中的方法会根据自身需要，要求不同类型的引用，此时除了相信编译器的力量，还要多多查阅官方文档，以避免不可控的事件。

### 七、结构体类型 Struct

以下展示了定义结构体的几种方法：

```rust
struct ColorClassicStruct {
    red: i32,
    green: i32,
    blue: i32,
}
// 允许提供默认值

struct ColorTupleStruct(i32, i32, i32);
// 该结构元素的访问和元组一样，定义的时候需要在前面加上结构名
// e.g.: let green = ColorTupleStruct(0, 255, 0);

struct UnitLikeStruct;
// 没有任何字段的类单元结构体
// 单元结构体： ()
```

- 如果需要某个类型来实现某种不需要数据的行为时就可以用类单元结构体，类似于其它语言中没有数据成员的类。

- 输出时可用 {:?} 获得结构体类型的名称。

- 可以用 `let A2 = A { a: false, ..A1}` 定义除了a之外，其他元素和A1一样的对象。

- 最好尽量保证结构体拥有其数据，也就是说尽量不要使用引用。
比如说尽量用 String 而不是 &str

- 定义针对结构变量的方法和关联函数：`impl B { fn mB1(&self) -> u32 { ... } }`，

- 方法的第一个参数必须是 `self: &[mut] self` （可以简写为&self）

- 关联函数不需要遵循上面的条件，用 `B::B2()` 调用，
例：`String::from()`，一般用于初始化对象（创建新的实例）

### 八、枚举类型 Enum

```rust
enum An_Enum
{ 
    Int_2(i32, i32), 
    Float(f64), 
    Text(String), 
    Other,  
}

let A = An_Enum::Int_2(0, 0);
```

- Rust 中枚举类型**只作为标识符**，不具有对应的“数字索引”。

- 枚举类型中的各个成员的类型可以不一样。

- 枚举 Option 在标准库中的定义：`enum Option<T> { None, Some(T), }`，可用于消除空值运算的风险

- match 语句：匹配枚举类型

```rust
enum Year { 2005, 2006, 2007, }
enum Coin { 
    Fen, 
    Jiao, 
    Yuan(Year), 
} 
// 可以用(A)或者{ a:i32, b:i32 }绑定其他类型，之后match可以用

fn value_in_coins(coin: Coin) -> u8 {
    match coin { // 此处的coin可以是任意类型，不一定是if那样的表达式
        Coin::Fen => 1,
        Coin::Jiao => 10,
        Coin::Yuan(year) => {
            println!(“这个一块发行于{:?}年”, year);
            100
        } // 可以是语句块

        // _ => (), 对默认情况 _（上面没有处理的情况）操作，
        // match 中必须处理所有可能出现的情况
    }
}
fn main() {
    value_in_coins( Coin::Yuan(2005) )
}
```

- 在匹配到的枚举元素后面，如果需要传递元组，需要拆成一个个元素才能传递
- 如果想忽略其中一些元素，可使用 `_` ，忽略几个就打几个
- 如果想忽略之后的所有元素，可使用 `..`

- match 可用于处理 Option<T>

- 匹配模式（=> 左边的值）：字面值、命名变量、多个值（用 | 分割）、区间表达式

### 九、字符串类型 &Str and String

笔者目前遇到的 Rust 字符串有 2 种：`&str` 和 `String`。字面量为 `&str` 。

```rust
// &str -> String
let A = String::from("foo");
```

访问 `String` 时需要用 `&` 。

以下提供一些字符串的常用方法（其他可查阅官方文档）：
- `A + " ... "`: 字符串拼接，注意后面的是 &str 。
- `A.replace(B, C)`: 将 A 中所有的子串 B 换成 C 。
- `"...".to.owned()`: 将 &str 转换为 String， 且获取其所有权。
- `A.to_uppercase() / A.to_lowercase() ` : 字面意思。
- `A.as_bytes()`: 可将字符串转换为为 vec。
- `A.as_chars()`: 可将字符串转换为迭代器。
- `A.clear()`: 清空字符串
- `A.trim()`: 删除字符串两端所有的空白字符
- `A.trim_matches(<pattern>)`: 匹配字符串中所有的 pat 并删除
  （ pat 不一定是字符串，比如 `char::is_numeric` 会删掉所有的数字）
- &str会自动转换为String
- 派生：str.trim_left/right_matches(pat)
- str.splitn(n, “<sign>”): 根据特定的sign将字符串分割为n份
  如果要从后面开始分割就用 str.rsplitn(...)
  如果不需要知道具体多少份，只需要全部分割，就用 split_terminator("<sign"?)

### 十、包 Crates

```rust
use std::time::{SystemTime, UNIX_EPOCH};
// 以上是一个应用标准库模块的示例

mod <mod_name> {
    [pub] use <some_explicit_module> as <alias>; 
    // 在 mod 内使用外部模块

    fn ... { ... }        // 私有成员
    pub fn ... { ... }    // 公有成员
}
```

#### 做题经验

cargo new得到的包中，./src/main.rs是与包同名的二进制crate的crate根，
而./src/lib.rs是与包同名的库crate的crate根，两者可共存
每个./src/bin下的文件都会被编译成一个独立的二进制crate

1. 对一个模块（mod），编译时会从crate根开始逐级往下编译，
在根文件中可通过mod <name>;声明模块。
寻找该模块：

- 内联：用语句块{ … }代替分号
- 在./src/.rs
- 在./src//mod.rs （老风格）
- 在上述ii, iii的文件中仍可以用mod <name>;声明子模块
模块一般需要定义在上一级的代码文件中，
如果想将当前目录下的主文件<name>.rs拆分成多个文件，也可以使用mod声明，
然后将声明的对象放在同级的文件夹中，
拆分后文件的子模块和上面操作一样，新建同名文件夹并将子模块放进去即可
访问：<crate>::<name1>::<name2>

2. 默认私有，公用：pub mod <pubname>;
访问模块的快捷方式：use <crate>::<name1>::<name2>; ，
之后就可以只用<name2>访问

3. 用途：对代码进行分组，使整体结构清晰

4. 根据模块树的路径访问模块：用 ::
绝对路径：从crate开始； 相对路径：从当前位置开始

5. 父模块的项不能使用子模块中的私有项，但反过来可以

6. 公有项里面的私有项仍然是私有项
由此可以管理模块中的公有和私有方法，以及结构的公有成员和私有成员
但公有枚举的所有成员均为公有

7. use super::<...>; 从父模块的路径开始

8. use不允许导入同名的快捷方式，但可以通过父模块区分，
或者用as（像Python一样）起别名

9. pub use：不仅将一个名称导入了当前作用域，还允许别人把它导入他们自己的作用域。
用途：导出公有的API

10. 使用外部包：在包中的Cargo.toml文件中加入<packagename> = "<version>"

11. use std::{cmp::Ordering, io}; ：可以用嵌套路径缩短use行

12. use std::collections::*; ：*称为glob(al?)运算符，引入该模块下的所有公有成员
务必小心，可能会有7中同名的情况
常用于测试模块test中，有时也用于prelude模式

13. build.rs：构建脚本
关于构建脚本的一些使用场景如下：

- 构建 C 依赖库
- 在操作系统中寻找指定的 C 依赖库
- 根据某个说明描述文件生成一个 Rust 模块
- 执行一些平台相关的配置
- 构建脚本的输入可以是环境变量和构建脚本所在的当前目录
- 构建脚本的输出不应修改该目录之外的任何文件
更多资料：
构建脚本 build.rs - Rust语言圣经(Rust Course)

14. 此脚本通过 stdout （标准输出）提供输出。
打印的所有行都写入到target/debug/build//output。
另外，以 cargo: 为前缀的行将由 cargo 直接解析，
因此可用于定义包编译的参数。

### 十一、哈希表 HashMap

使用前提：(因为比较少用，所以没有被 prelude 导入)

```rust
use std::collections::HashMap;

let hm1 = HashMap::<String, i32>::new();

hm1.insert( <key>, <value> );
// 会转移所有权，可以用 &

hm1.entry( <key> ).or_insert( <value> )
// 如果 hm1 中没有键 k ，就插入( <key>, <value> )

hm1.get( <key> )
// 读取元素
```

### 十二、异常处理 Panics
显式调用：panic!(“<err_message>”);

默认展开（回溯栈并清理所有数据），可在Cargo.toml文件中设置为终止，可以让最终二进制文件更小，用法：在该文件中加入:

```toml
[profile.release]
panic = ‘abort’
```

`enum Result<T, E> { Ok(T), Err(E), }` 用match处理

错误处理：依靠Result类型，
例如如果需要获得正确的结果(T)，就用match匹配即可（用闭包更短更快）

panic处理简写：

- `<ResultObject>.unwrap()` 返回Ok内的T，否则抛出panic
  （尽量别用，因为这对人工检查代码没有Err的要求比较高）
- `<ResultObject>.expect("<err_msg>")` 同上，抛出时附带信息（更常用）
- `<ResultObject>?` 同.unwrap()，能使代码更短（只能用在返回Result类型的函数中）
如果能准确预测错误的出现（可以恢复运行的错误），最好返回Result<T, E>而不是经常使用panic

输入字符串，处理为数字：`str.parse::<i32>()`

▲ 在没有返回值（比如main）或者返回值为()的函数中不能使用 ? 运算符，
如果想用可以给函数加上返回值类型（返回Result类型；main也可以）
可以用unwrap()代替？（不建议使用）
另：返回的Result可以是Result<(), ErrorType>，
也可以是 Result<(), Box<dyn ErrorType>>
（可以捕捉所有类型的错误，但不建议在库代码中使用）

### 十三、泛型 Generics

用在函数上：`fn largest<T>(list: &[T]) -> &T {}`
可用于函数、结构体、枚举、方法等
泛型定义中同一个字母只代表一种类型
Option 和 Result 就是利用泛型实现的
使用泛型的接口：`impl<T> Name<T> { }`
泛型代码编译时会先进行单态化
可以针对特定类型实现特殊方法，如`impl Point<f32> {}`

### 十四、接口 Traits

声明：`trait <trait_name> { }`
实现：`impl <trait_name> (for <datatype>) { }`

trait 可以作为参数，以 `item: &impl datatype` 的形式传入，以`item.method()`的形式调用方法（与 `<T: datatype>(item: &T) 等价）

可以用 + 连接需要同时实现的多个接口，
如`(item: &(impl Summary + Display)` 
如果太长，可以用where写成：

```rust
fn some_function<T, U>(t: &T, u: &U) -> i32
where

T: Display + Clone,
    U: Clone + Debug,
{ … }
```

`impl<T: trait_name> name<T>`：函数名处的<...>不需要再写实现的模块

返回值也可以用 impl trait 指定返回实现了trait的类型，但由于泛型会进行单态化，所以函数中所有的返回值类型必须一致

trait 中可以为方法提供默认行为，不是非要在 impl 中定义

### 十五、生命周期 Lifetimes

显式声明如：`&i32改为&'a i32`
函数名后加标签：`method<'a>`
不允许创建悬垂引用（空指针），通过改变生命周期的方法也不行
`'static`：变量生命周期贯穿整个程序
大部分情况中，推荐 `'static` 生命周期的错误信息都是尝试创建一个悬垂引用或者可用的生命周期不匹配的结果。
在这种情况下的解决方案是修复这些问题而非指定一个 'static 的生命周期。
可以用 static 代替 let 声明静态变量，此时需要显式指定变量类型
生命周期约束：
<’a, ‘b:’a, T>: 声明 ‘b 至少要和 ‘a 活得一样久
<’a, T>：表示 T 至少比 ‘a 活得久
引用的生命周期从借用处开始一直到最后一次使用的地方

### 十六、迭代器和闭包 Iterators and Closure

声明：如`A.iter()`

迭代器接口：

```rust
pub trait Iterator {
type Item;
fn next(&mut self) -> OptionSelf::Item;
// 此处省略了方法的默认实现
}
```

iter.next() 返回 Some(下一项)，结束时返回 None

迭代器声明后必须被使用

闭包：可以保存进变量或者作为参数传递给其他函数的匿名函数

闭包定义：`|x, y, z| { }` （只有一行时大括号可省略）
竖线内为参数
可以在一个地方创建闭包，之后在不同的上下文中进行闭包运算

```rust
let capitalize_words: Vec = words.iter().map(|x| capitalize_first(x)).collect();
// map方法接受一个闭包作为参数

let expensive_closure = |num| {

println!("calculating slowly...");
thread::sleep(Duration::from_secs(2));
num
};
```
后面的用法应该类似python的lambda函数

`<...>.collect()` 可以直接将前面的表达式收集为需要返回的目标类型，目标类型需要明确指定。

一些方法：

- `Iter.count()`：返回迭代器项数
- `Iter.sum()`：返回迭代器的和
- `Iter.product()`：返回迭代器的积
- `Iter.filter( |x| expr_x )`：根据表达式真假过滤元素
- `List_Iter_A.enumerate()`：可生成元组(Index, &Element)

更多资料：

【Rust 笔记】13-迭代器（上）_rust笔记 迭代器 13 中-CSDN博客
【Rust 笔记】13-迭代器（下）_rust product-CSDN博客
Hashmap转Vec：`Vec::from_iter( hashmap.iter() )`
返回`[(K1,V1), (K2,V2), …]`
可以换成hashmap.keys() / .values() 这两个方法直接返回迭代器

### 十七、智能指针 Smart Pointers

`Box<T>`：将数据存放在堆中，在栈中压入一个指向堆中数据的指针
用途：
1）编译时未知大小的类型，又想要在需要确切大小的上下文中使用这个类型值的时候
2）有大量数据并希望在确保数据不被复制的情况下转移所有权时

递归（Recrusive）：
定义：

```rust
enum List {

Cons(i32, List),
Nil, // Nil代表循环终止
}

使用：let list = Cons(1, Cons(2, Cons(3, Nil)))

在Rust中实现递归最大的问题是不知道运行的时候需要占用多少内存，
因此可以利用Box改成这样：

enum List {
Cons(i32, Box),
Nil, // Nil：代表循环终止
}

使用：let list = Box::new(Cons(1, Box::new(Cons(2, Box::new(Cons(3, Nil))))));
```

解引用（dereference）：`*`
实现 deref trait 即可，该特性允许智能指针采用与引用相同的处理方式进行处理
隐式Deref强制转换：有时可以省略&或*

Drop trait：(std::mem::drop)
需要实现方法drop，获取可变引用，最后释放资源
用法：A.drop()

引用计数（reference counting）：Rc<T>
用于需要用到多所有权的情况中
声明：`let Rc_A = Rc::new();`
获取多所有权：`let Rc_1 = Rc::clone( &Rc_A );`（可能需要显式指定类型）
释放多所有权：`drop(Rc_1);`
获取和释放的顺序可以不一样

Cow：Clone on write，use std::borrow::cow;
仅当需要可变的 B 类型对象或者获取所有权时，才会将该对象复制并传递
当然也可以将比如没有 & 的 slice 等类型按正常方式传递所有权
以下是标准库中的原型：

```rust
pub enum Cow<’a, B>
where

B: 'a + ToOwned + ?Sized,
{

Borrowed(&'a B),
Owned(<B as ToOwned>::Owned),
}
```

### 十八、线程 Threads / 并发 Concurrent Programming

并发（Concurrent Programming）：
程序的不同部分相互独立地执行
并行（Parallel Programming）：
程序的不同部分相互独立且同时执行
对操作系统来说，
进程是资源分配的基本单位
线程是任务执行的基本单位
目的：将一批任务分给多个线程同时处理以提高效率

创建新线程：

```rust
use std::thread;
<let handle => thread::spawn( || { 线程具体内容 })

两条竖线是没有参数的闭包
可选的move用于转移该线程中用到的变量所有权

handle.join() 等待所有线程结束，返回Result
thread::sleep(Duration::from_millis(1)): 休眠
（需要用到 std::time::Duration）
```

在线程之间传递数据：

```rust
let (Transmitter, Receiver) = std::sync::mpsc::channel();
// mpsc：Multiple producers and single conducter
Transmitter.send(val) 通过Transmitter将val发送给Receiver

Receiver.recv(): 通过Receiver接受Transmitter发送过来的数据
上述两个方法会转移所有权，且返回Result<T>
一个发送信道只能传递给一个线程，不过可以用clone
```

互斥器（Mutual Exclusion）：
`Mutex<T>`：任意时刻只允许一个线程访问某些数据
难点：

- 在使用数据之前使用`mutex_val.lock()`先将数据锁定在当前线程
- 在处理完数据后解锁让其他的线程获取lock
  e.g.:
  对于一个`A = Arc(Mutex(Struct1( …… )))`对象，
  调用Struct1内元素的方法是：`A.lock().unwrap().element1`
  对于有Mutex<T>的对象都需要先获取lock()

原子引用计数Arc<T>：
在std::sync::Arc中，用法和Rc<T>完全一样，但是能保证线程安全

## 十九、宏 Macros

优势：
可以接受不同数量的参数；
可以在编译前展开且为一个给定类型实现trait
缺点：
更难阅读、理解和维护
调用前必须定义
声明：`macro_rules! <macro_name> { }`
要使用在mod中的宏，可以在mod开头加入`#[macro_use]`
宏匹配的分支用;分隔即可，最后一个不需要

## 二十、类型转换 Conversions

- `as`：强制类型转换
  （Rust 不允许数值进行隐式的类型转换，也就是说1+1.0不合法）
- `from() / into()`：用于类型转换，实现了from就能自动实现into
- `try_from() / try_into()`：简单安全的类型转换，适用于比较容易错的类型转换
  库：`std::convert::{TryFrom, TryInto};`
上述方法可以实现泛型
- `as_ref() / as_mut() ：`
  对一个类型T，若实现了AsRef<U>，则可通过as_ref()将T转化为U的引用
  as_mut()为其可变版本

## 二十一、测试 Tests

1. #[test]：可以加在mod或者fn上面，则这个代码块只会在cargo test中运行，在所有测试标签中要放在最上面
2. 更常用：#[cfg(test)]：同上，但在cargo build中不编译
3. assert!()：用于断言某些条件为真，第二个参数是可选的错误信息
4. assert_eq/ne!(A, B)：字面意思
5. 一般测试模块都会加入use super::*;来避免写出繁复的块外调用
6. #[Should_panic]：
   当下面的代码块抛出panic时测试通过
   不能在返回Result类型的代码块中使用
7. 并行：cargo test -- --test-threads=<num>
8. 打印运行时输出的值：cargo test -- --show-output
9. cargo test <method_name>：指定运行哪些测试方法，
   此处的参数是指：包含参数这个字符串的所有方法（不仅仅是这一个）
10. #[ignore]：忽略某些测试
11. cargo test -- --ignored：只运行被忽略的测试
12. cargo test -- --include-ignored：运行所有测试
13. 集成测试：
    在包的根目录下（和src同级的地方）创建 tests 文件夹，
    新建 integration_test.rs 文件存放集成测试
14. 需要加入 use <包名>;
15. 如果需要在tests中创建不需要被执行的模块，须在模块目录下新建mod.rs，将模块内容放进去

## 二十二、非安全代码块 Unsafe Block

信息来自ChatGPT：
1. 在Rust中，\*mut是一个指针类型，表示可变的裸指针（raw pointer）。裸指针是一种直接存储内存地址而不提供安全性保证的指针类型。\*mut T中的T是指针指向的类型。\*mut指针可以用于访问和修改内存中的数据，但是使用它们需要特别小心，因为它们不受Rust的所有权和借用规则的保护，可能导致内存安全问题。
<br>
使用\*mut指针需要谨慎，因为它们可以绕过Rust的借用检查和内存安全性检查。在编写涉及裸指针的代码时，需要确保自己明确了解代码中的所有权和生命周期，并尽量减少对裸指针的使用，以减少潜在的错误和安全隐患。

2. 使用：`unsafe { ... }`
所有涉及到不安全行为的代码全部都要放在unsafe内
3. 裸指针（Naked pointer）：
？？？ 和C中的指针比较类似，可以通过unsafe中的裸指针修改值
4. `Box::into_raw(<data>)`：可以用于将Box<T>类型的data转换为 \*T,
`Box::from_raw(<\*mut_data>)`：可以将\*T转换为Box<T>

## 二十三、关于题99

[外部块 External Block](https://rustwiki.org/zh-CN/reference/items/external-blocks.html)

[Unsafe 的五种兵器](https://course.rs/advance/unsafe/superpowers.html)

[外部语言函数接口 Extern](https://rustwiki.org/zh-CN/rust-by-example/std_misc/ffi.html)

## 二十四、算法 Algorithms：第一阶段的拦路虎

### 栈 Stack

LIFO（最后入栈的元素最先出栈）
更快，因为数据在物理地址上更近，且组织形式简单

### 堆 Heap

分配内存并返回指向该内存的指针

调用函数时，函数参数（以及指向这些值的指针）会被压入栈以提高运行速度。

可以用栈实现，分为小顶堆和大顶堆

```rust
pub struct Heap
where

T: Default,
{

count: usize,
items: Vec<T>,
comparator: fn(&T, &T) -> bool,
}

impl Heap
where

T: Default,
{

pub fn new(comparator: fn(&T, &T) -> bool) -> Self {
    Self {
        count: 0,
        items: vec![T::default()],
        // 留下0号位，不使用
        comparator,
    }
}

pub fn len(&self) -> usize {
    self.count
}

pub fn is_empty(&self) -> bool {
    self.len() == 0
}

pub fn add(&mut self, value: T) {
    //TODO
    self.count += 1;
    self.items.push(value);
    let new_idx = self.count;
    self.heapify_from_bottom_to_top(new_idx);
}

fn heapify_from_bottom_to_top(&mut self, mut idx: usize) {
    // 目的：针对某一个元素自下而上堆化
    // 因为加入的新元素可能破坏堆结构
    // idx 为新元素的索引
    // 假设現在需要生成小顶堆，则比较器接口 comparator 应为 a < b -> true

    while idx > 1 {
        if (self.comparator)(&self.items[ idx ], &self.items[ idx/2 ]) {
            // 这里的 i > 1 是因为初始化堆时，已经预留了0号位不使用，所以此时堆中只有一个元素
            // 如果新元素比它现在的父节点大，就交换两者
            self.items.swap(idx, idx/2);
            idx /= 2;
            // 此时新元素的索引已经更新为 i/2 ，再次比较，直到上方元素小于或等于下方元素则终止
            // 注：堆中允许存在重复的元素
        } else {
            break;
            // !!! 这一条break非常重要！！！
            // 用于判定元素是否已经到达符合条件的位置
            // 如果没有break，会导致idx一直没有改变，从而陷入死循环
        }
    }
}

fn heapify_from_top_to_bottom(&mut self, mut idx: usize) {
    // 目的：针对某一个元素自上而下堆化
    // 其他同上
    while self.children_present(idx) {
        // 如果当前节点有子节点
        let schild = self.smallest_child_idx(idx);
        // schild 是当前节点下索引应当最小的那个元素的索引
        if (self.comparator)( &self.items[ schild ], &self.items[ idx ] ) {
            // 如果该子节点的值比新元素小，则需要将子节点的值上移
            self.items.swap( schild, idx );
            idx = schild;
            // 此时新元素的索引已经更新为schild，重复该操作直到该元素到达正确的位置，
            // 或者该元素已经没有子节点
        } else {
            break;
            // !!! 这一条break非常重要！！！
            // 用于判定元素是否已经到达符合条件的位置
            // 如果没有break，会导致idx一直没有改变，从而陷入死循环
        }
    }
}

fn parent_idx(&self, idx: usize) -> usize {
    idx / 2
}

fn children_present(&self, idx: usize) -> bool {
    // 如果当前节点有无子节点
    self.left_child_idx(idx) <= self.count
}

fn left_child_idx(&self, idx: usize) -> usize {
    idx * 2
}

fn right_child_idx(&self, idx: usize) -> usize {
    self.left_child_idx(idx) + 1
}

fn smallest_child_idx(&self, idx: usize) -> usize {
    //TODO
    // 目的：返回当前节点下索引应当最小的那个元素的索引（？？？）
    let lchild = self.left_child_idx(idx);
    let rchild = self.right_child_idx(idx);
    if rchild > self.count || (self.comparator)( &self.items[ lchild ], &self.items[ rchild ] ) {
        // 根据堆的结构：最底层的节点靠左填充，其他层节点全部被填满，
        // 如果右边没有元素（表现为用于储存数据的栈 items 长度不够），则直接返回左节点索引
        // 对小顶堆来说，每个节点左侧的节点值必定比右侧的大，此时返回左节点索引
        lchild
    } else {
        rchild
    }
}
}

impl Heap
where

T: Default + Ord,
{

/// Create a new MinHeap
pub fn new_min() -> Self {
    Self::new(|a, b| a < b)
}

/// Create a new MaxHeap
pub fn new_max() -> Self {
    Self::new(|a, b| a > b)
}
}

impl Iterator for Heap
where

T: Default,
{

type Item = T;

fn next(&mut self) -> Option<T> {
    //TODO
    if self.count == 0 {
        return None;
    }  
    // 如果堆中存在元素，就将其与堆顶元素交换后移出该元素
    let next_elem = self.items.swap_remove(1);
    self.count -= 1;
    self.heapify_from_top_to_bottom(1);
    Some(next_elem)
}
```

### 队列 Queue

```rust
use std::collections::VecDeque;
FIFO（最先入队的元素最先出队）

VecDeque 的 push_back, pop_front, pop_back（可在双端操作）

环形队列：在队列中访问索引时，对最大容量取模

栈和队列可以互相实现

pub struct myStack<T>
{
    //TODO
    q1:Queue<T>,
    q2:Queue<T>
}
impl<T> myStack<T> {
    pub fn new() -> Self {
        Self {
            //TODO
            q1:Queue::<T>::new(),
            q2:Queue::<T>::new()
            // q2的后端用于入栈
            // q1的前端用于出栈
            // 两者形成 U 形结构
        }
    }
    pub fn push(&mut self, elem: T) {
        self.q1.enqueue(elem);
        // 接下来需要将elem移动到栈顶
        while let Ok(val) = self.q2.dequeue() {
            self.q1.enqueue(val);
            // 经过该操作后，q2被清空。所有元素全部进入q1，elem在队头（q1）
        }
        // 交换以确保q1为空，为下一次入栈做准备
        std::mem::swap(&mut self.q1, &mut self.q2);
    }
    pub fn pop(&mut self) -> Result<T, &str> {
        //TODO
        match self.q2.dequeue() {
            Ok(val) => Ok(val),
            Err(_) => Err("Stack is empty"),
        }

    }
    pub fn is_empty(&self) -> bool {
        //TODO
        self.q1.elements.is_empty() && self.q2.elements.is_empty()
    }
}
```

### 排序

自定义排序：Ord Trait
归并排序
将2/多个有序子数组进行排序
先划分块（尽量等分），直到各个数组长度为1或2（长度尽量相等）

具体过程：
假设一开始是a0和b0比，比如说如果b0比较小，那么就把b0放进新数组，然后移动到b1（下标+=1），此时比较的是a0和b1，
以此类推，如果一个数组已经空了，就把另一个剩下的元素全部放进新数组，至此归并排序完成。

快速排序
选定基准数字，将小于其的放在左边，大于其的放在右边

Sort_unstable：value相同时不保证key原来的位置

目前最快的排序算法：Timsort（稳定），pdqsort（不稳定）
注：稳定是指是否必定保留排序前的相对顺序

## 内存布局

usize/isize：64为8 Bytes / 32为4 Bytes
Sized Trait：静态大小，可以在编译时确切知道数据有多大
链表：线性关系
节点（Node）：
包含值和指向下一个节点的引用，在内存中的储存是分散的
插入节点时必须先连接再断开，否则很可能造成内存泄漏

```rust
struct Node {
val: T,
next: Option<NonNull<Node>>,
}

解引用裸指针在Rust中视为unsafe

建议不要用Rust写链表，可以直接用标准库的std::collections::LinkedList（是双向链表）

二叉搜索树
```rust
impl<T> BinarySearchTree<T>
where
    T: Ord,
{

    fn new() -> Self {
        BinarySearchTree { root: None }
    }

    // Insert a value into the BST
    fn insert(&mut self, value: T) {
        match &mut self.root {
            None => {
                self.root = Some(Box::new(TreeNode::new(value)));
            },
            Some(root) => {
                // 如果root中已经有值，那就直接在root中insert，
                // 节点的insert已经实现了自动查找空的left和right
                root.insert(value);
            }
        }
    }

    // Search for a value in the BST
    fn search(&self, value: T) -> bool {
        //TODO
        match &self.root {
            None => {
                false
            },
            Some(root) => {
                //同上，节点处已经实现了search
                root.search(value)
            }
        }
    }
}

impl<T> TreeNode<T>
where
    T: Ord,
{
    // Insert a node into the tree
    fn insert(&mut self, value: T) {
        // 向二叉搜索树中插入元素
        match value.cmp(&self.value) {
            // 其实就是比较大小，只是Ordering可以让不需要操作的情况正常存在
            // 比根节点小的放在左侧，大的放右侧，接下去的每一层都这样实现
            Ordering::Less => {
                match &mut self.left {
                    // 左侧没有节点时创建储存value的新节点
                    // 否则在下一级递归调用insert，直到没有节点为止
                    Some(left) => left.insert(value),
                    None => {
                        self.left = Some(Box::new(TreeNode::new(value)));
                    },
                }
            },
            Ordering::Greater => {
                match &mut self.right {
                    Some(right) => right.insert(value),
                    None => {
                        self.right = Some(Box::new(TreeNode::new(value)));
                    },
                }
            },
            Ordering::Equal => {
                // 二叉树中不需要重复的元素
            }
        }
    }

    fn search(&self, value: T) -> bool {
        // 搜索树中的指定元素
        match value.cmp(&self.value) {
            Ordering::Less => match &self.left {
                // 如果要找的值比当前节点小，说明符合条件的值在子树的左侧
                Some(ref left) => left.search(value),
                None => false,
            }
            Ordering::Greater => match &self.right {
                Some(ref right) => right.search(value),
                None => false,
            }
            Ordering::Equal => true
        }
    }
}
```

## 二十五、图

图可分为有向图/无向图，连通图/非连通图（有孤立节点）
BFS与DFS中的self类型（Graph）采用如下定义：

```rust
struct Graph {
    adj: Vec<Vec<usize>>, 
}

impl Graph {
    fn new(n: usize) -> Self {
        Graph {
            adj: vec![vec![]; n],
        }
    }

    fn add_edge(&mut self, src: usize, dest: usize) {
        self.adj[src].push(dest);
        self.adj[dest].push(src); 
    }
}
```

### 25.1 BFS

```rust
fn bfs_with_return(&self, start: usize) -> Vec<usize> {

    // BFS 广度优先搜索
    // 要求：先访问初始节点所有没有访问过的邻节点，
    // 再按上述顺序依次访问下一级邻节点，
    // 直到所有节点被访问过

    // 初始化
    // visited 用于标记图中的元素是否已经被访问过
    // visit_order 相当于栈，按访问顺序（广度优先）返回元素
    // queue 为双端队列，是BFS实现的关键
    let mut visit_order = vec![];
    let mut visited = vec![false; self.adj.len()];
    let mut queue = VecDeque::new();

    // 初始化开始的节点 start，并作为访问的第一个元素，
    // 同时令其进入队列
    visited[start] = true;
    queue.push_back(start);
    visit_order.push(start);

    // 接下来在 queue 前端获取一个节点作为当前操作的节点
    while let Some(current_node) = queue.pop_front() {
        // 在本题的结构 Graph 中，
        // 节点通过边连接的其他节点以邻接数组方式存储，
        // （由于邻接矩阵唯一，因此广度优先遍历序列也唯一）
        for &neighbor_node in &self.adj[current_node] {
            // 然后遍历当前节点连接的所有节点
            // 看这些节点有没有被访问过
            if !visited[neighbor_node] {
                // 如果没有访问过，那就访问并修改访问标记
                visit_order.push(neighbor_node);
                queue.push_back(neighbor_node);
                visited[neighbor_node] = true;
            }
        }
        // 直到所有节点全部被访问，则pop_front将返回None，从而退出循环
    }

    visit_order
}
```

### 25.2 DFS

```rust
fn dfs_util(&self, v: usize, visited: &mut HashSet<usize>, visit_order: &mut Vec<usize>) {
    // DFS 深度优先算法
    // 要求：从初始节点开始向远处访问，直到没有后继节点，
    // 然后回溯到最近的且连接未访问节点的节点
    // 重复上述过程

    // 本题限制只能用HashSet，已经访问的元素可以放进HashSet中
    // 以下因为应用递归，所以每一次调用该方法都会使得输入的 v 
    // 通过开头的两条语句设置为已经被访问，因此只需要往下套即可
    visit_order.push(v);
    visited.insert(v);

    for &neighbor_node in &self.adj[v] {
        if !visited.contains(&neighbor_node) {
            self.dfs_util(neighbor_node, visited, visit_order)
        }
    }

    // 以下为 DFS 提供了一种非递归的实现
    #[cfg(feature = "non-recursive")]
    {
        let mut stack = vec![];
        // 此时v即为上题的start
        stack.push(v);
        visit_order.push(v);
        visited.insert(v);
        while let Some(current_node) = stack.pop() {
            for &neighbor_node in &self.adj[current_node] {
                if !visited.contains(&neighbor_node) {
                    stack.push(neighbor_node);
                    visit_order.push(neighbor_node);
                    visited.insert(neighbor_node);
                }
            }
        }
    }
}

// Perform a depth-first search on the graph, return the order of visited nodes
fn dfs(&self, start: usize) -> Vec<usize> {
    let mut visited = HashSet::new();
    let mut visit_order = Vec::new(); 
    self.dfs_util(start, &mut visited, &mut visit_order);
    visit_order
}
```

## 第一阶段总结

第一阶段虽然说迟了一两天完成，不过好在对 Rust 的理解更加深刻了。希望能顺利完成第二阶段。不过，算法那部分还是比较薄弱，在第二阶段应该会有更多的实际应用来巩固，期待第二阶段！

## 附录

项目链接：[开源操作系统社区](https://opencamp.cn/os2edu)
