# rust-rustlings-2024-nusakom-blog
## 这个文档将记录我在参加2024年秋季NUSAKOM博客的Rustlings挑战中的学习过程。
操作系统训练营的学习内容：

第一阶段rust基础知识（9/30-10/18）

[rust-rustlings-2024-autumn-nusakom]https://github.com/LearningOS/rust-rustlings-2024-autumn-nusakom

此阶段完成110题rustlings（这一次从30题开始），blog以笔记和做题解析的形式记录。

第二阶段r core(10/21-11/8)；

第三阶段（12/2-12/22）；

（注：此文档以时间逆序的顺序记录，方便查阅）
### 2024/9/30
##### 结构体（Structs）
Rust 有三种结构类型：经典 C 结构、元组结构和单元结构。
1. **常规结构体（Regular Struct）**：

   - 由多个命名字段组成，每个字段可以有不同的数据类型。
   - 适用于需要清晰字段名称的场景。

   ```rust
   struct Person {
       name: String,
       age: u32,
   }
   ```
2. **元组结构体（Tuple Struct）**：

   - 由多个未命名字段组成，字段的顺序决定了它们的类型。
   - 适用于简洁地组合多个值，但不需要给每个字段命名的场景。

   ```rust
   struct Point(i32, i32);
   ```
3. **单元结构体（Unit-like Struct）**：

   - 没有字段，相当于一个零大小的类型。
   - 常用于标识某种状态或作为类型标记。

   ```rust
   struct Marker;
   ```
##### 小结
- **常规结构体**：适合包含多个命名字段。
- **元组结构体**：适合简洁表示多个值。
- **单元结构体**：用于状态标识，没有数据存储。
##### 这边有3道题
- **第30题**：考察常规结构体和元祖结构体，这里i32和u8其实都可以（区别在于132范围更大可以有负数，u8必须为正）。常规结构体都需要写字段名，然后元组结构体不需要。
- **第31题**：创建了一个新的 Order 实例 your_order，同时保留了 order_template 的其他字段的值。
- **第32题**：is_international 方法用于判断包裹是否为国际包裹，而 get_fees 方法用于计算运费。
#### Enums 枚举
在Rust中，枚举（enum）是一种用于定义一组相关值的类型。枚举允许你定义一种类型，它可以是不同的变体（variants），每个变体可以包含不同的数据。
- **第33题**： 
```rust
  enum Message {
    Quit,
    Echo(String),
    Move { x: i32, y: i32 },
    ChangeColor(i32, i32, i32),
  }
```
  这个枚举定义了四种变体：Quit、Echo、Move 和 ChangeColor。
Quit, // 不带数据的变体
Echo(String), // 带一个字符串数据的变体
Move { x: i32, y: i32 }, // 带有 x 和 y 的结构体变体
ChangeColor(i32, i32, i32), // 带有三个 i32 的变体（RGB颜色）
Echo 带有一个字符串，Move 带有 x 和 y 坐标，ChangeColor 带有 RGB 值。这样可以传递更丰富的信息。
- **第34题**：
```rust
enum Message {
    Move { x: i32, y: i32 }, // 带有 x 和 y 的结构体变体
    Echo(String),           // 带有一个字符串数据的变体
    ChangeColor(i32, i32, i32), // 带有三个 i32 的变体（RGB颜色）
    Quit,                   // 不带数据的变体
}
```
定义 Message 枚举的不同变体，以匹配 main 函数中使用的变体。丰富的枚举类型支持，可以支持结构体，元祖等混合使用。
- **第35题**：
```rust
enum Message {
    ChangeColor(u8, u8, u8), // 变体：变体用于改变颜色，携带 RGB 值
    Echo(String),            // 变体：变体用于回显消息，携带一个字符串
    Move(Point),             // 变体：变体用于移动，携带一个 Point 结构体
    Quit,                    // 变体：变体用于退出，不需要任何数据
}
```
主要就是枚举的match操作，类似于kotlin的when，不需要使用break。
#### Strings 字符串
Rust 有两种字符串类型，一个字符串切片 （&str） 和一个拥有的字符串 （String）。我们不会规定何时应该使用哪一个，但我们将向您展示如何识别和创建它们，以及使用它们。
- **第36题**：
1、`&str` 是不可变的切片引用，而 `String` 是可变的堆分配字符串。
2、当需要动态修改字符串或在运行时生成字符串时，使用 `String`。当仅需读取静态字符串时，使用 `&str`。
- **第37题**：
- 可以通过调用 .as_str() 方法来完成
- `word.as_str()` 将 `String` 类型的 `word` 转换为 `&str`，满足 `is_a_color_word` 函数的参数类型要求。
- 这样，代码可以成功编译并运行，判断 `word` 是否为已知颜色词。
- **第38题**：
- 需要实现 trim_me、compose_me 和 replace_me 三个函数
1、trim_me:
使用 trim() 方法去除字符串两端的空白，返回一个 &str，然后调用 to_string() 将其转换为 String。
2、compose_me:
使用 format! 宏来创建一个新字符串，将 " world!" 添加到输入字符串后面。
3、replace_me:
使用 replace 方法，将字符串中的 "cars" 替换为 "balloons"，并返回新的 String。
- **第39题**：
```rust
fn main() {
    string_slice("blue"); // &str
    string("red".to_string()); // String
    string(String::from("hi")); // String
    string("rust is fun!".to_owned()); // String
    string("nice weather".into()); // String
    string(format!("Interpolation {}", "Station")); // String
    string_slice(&String::from("abc")[0..1]); // &str
    string_slice("  hello there ".trim()); // &str
    string("Happy Monday!".to_string().replace("Mon", "Tues")); // String
    string("mY sHiFt KeY iS sTiCkY".to_lowercase()); // String
}
```
1、 string_slice 函数适用于 &str 类型的参数，因此调用时需要传入字符串字面量或引用。
2、string 函数适用于 String 类型的参数，通常用于堆分配的字符串或经过转换的结果。

- "blue": 字符串字面量，类型为 &str。
- "red".to_string(): 转换为 String。
- String::from("hi"): 直接创建 String。
- "rust is fun!".to_owned(): 返回 String。
- "nice weather".into(): 自动转换为 String。
- format!("Interpolation {}", "Station"): 返回 String。
- &String::from("abc")[0..1]: 返回 &str。
- " hello there ".trim(): 返回 &str。
- "Happy Monday!".to_string().replace("Mon", "Tues"): replace 返回 String。
- "mY sHiFt KeY iS sTiCkY".to_lowercase(): 返回 String。
#### Modules 模块
In this section we'll give you an introduction to Rust's module system.
在本节中，我们将向你介绍 Rust 的模块系统。
- **第40题**：
1、pub 关键字: 使 make_sausage 函数在模块外部可见。没有 pub 的函数只能在定义它们的模块内调用。
2、get_secret_recipe: 仍然保持为私有函数，这样可以保护其实现细节，不允许外部访问。
- **第41题**：
1、pub use: 使用 pub 关键字，使得 fruit 和 veggie 在模块外部可见。
2、别名: 在 use 语句中将 PEAR 和 CUCUMBER 分别重命名为 fruit 和 veggie，这样在 main 函数中可以直接使用
- **第42题**：
use std::time::{SystemTime, UNIX_EPOCH};: 这行代码同时引入了 SystemTime 和 UNIX_EPOCH，满足了题目的要求。

### Hashmaps 哈希图
- **第43题**：

- **第44题**：

- **第45题**：

- **测试2**：