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
```RUST
fn fruit_basket() -> HashMap<String, u32> {
    let mut basket = HashMap::new(); // 初始化一个 HashMap

    // 两根香蕉已经放在你的篮子里了 :)
    basket.insert(String::from("banana"), 2);

    // 把更多的水果放进你的篮子
    basket.insert(String::from("apple"), 3); // 例如，放入3个苹果
    basket.insert(String::from("orange"), 1); // 放入1个橙子

    basket
}
```
fruit_basket 函数创建并返回一个包含三种水果及其数量的哈希映射：其中有 2 个香蕉、3 个苹果和 1 个橙子。它通过插入键值对的方式将这些水果及其数量添加到一个可变的 HashMap 中，并在最后返回这个篮子。
- **第44题**：
```RUST
fn fruit_basket(basket: &mut HashMap<Fruit, u32>) {
    let fruit_kinds = vec![
        Fruit::Apple,
        Fruit::Banana,
        Fruit::Mango,
        Fruit::Lychee,
        Fruit::Pineapple,
    ];

    for fruit in fruit_kinds {
        // 插入新的水果，如果它们尚未在篮子中存在
        if !basket.contains_key(&fruit) {
            basket.insert(fruit, 1); // 每种新水果默认插入1个
        }
    }
}
```
`fruit_basket` 函数接受一个可变的哈希映射 `basket`，该映射的键为 `Fruit` 类型，值为 `u32`，用于记录水果的数量。函数定义了一个包含多种水果的向量 `fruit_kinds`，包括苹果、香蕉、芒果、荔枝和菠萝。接着，它遍历这个向量，对每种水果检查是否已经存在于 `basket` 中，如果不存在，则将该水果插入到篮子中，默认数量为 1。
- **第45题**：
```rust
fn build_scores_table(results: String) -> HashMap<String, Team> {
    // The name of the team is the key and its associated struct is the value.
    let mut scores: HashMap<String, Team> = HashMap::new();

    for r in results.lines() {
        let v: Vec<&str> = r.split(',').collect();
        let team_1_name = v[0].to_string();
        let team_1_score: u8 = v[2].parse().unwrap();
        let team_2_name = v[1].to_string();
        let team_2_score: u8 = v[3].parse().unwrap();

        // 更新团队1的得分
        scores.entry(team_1_name.clone()).or_insert(Team { goals_scored: 0, goals_conceded: 0 });
        let team_1 = scores.get_mut(&team_1_name).unwrap();
        team_1.goals_scored += team_1_score; // 增加进球数
        team_1.goals_conceded += team_2_score; // 增加失球数

        // 更新团队2的得分
        scores.entry(team_2_name.clone()).or_insert(Team { goals_scored: 0, goals_conceded: 0 });
        let team_2 = scores.get_mut(&team_2_name).unwrap();
        team_2.goals_scored += team_2_score; // 增加进球数
        team_2.goals_conceded += team_1_score; // 增加失球数
    }
    
    scores
}
```
这个程序定义了一个 `build_scores_table` 函数，它解析比赛结果并构建一个包含各球队得分和失分的哈希表。对于每场比赛，函数提取出两个球队的名称及其得分，并更新对应球队的 `goals_scored` 和 `goals_conceded` 数据。如果球队不在哈希表中，它会被初始化。最终，函数返回这个哈希表，其中每个球队都记录了他们的进球和失球情况。
- **46测试2**：
```RUST
pub enum Command {
    Uppercase,
    Trim,
    Append(usize),
}

mod my_module {
    use super::Command;

    pub fn transformer(input: Vec<(String, Command)>) -> Vec<String> {
        let mut output: Vec<String> = Vec::new();
        for (string, command) in input.iter() {
            let transformed = match command {
                Command::Uppercase => string.to_uppercase(),
                Command::Trim => string.trim().to_string(),
                Command::Append(n) => {
                    let append_str = "bar".repeat(*n);
                    string.clone() + &append_str
                }
            };
            output.push(transformed);
        }
        output
    }
}

#[cfg(test)]
mod tests {
    use crate::my_module::transformer;
    use super::Command; 

    #[test]
    fn it_works() {
        let output = transformer(vec![
            ("hello".into(), Command::Uppercase),
            (" all roads lead to rome! ".into(), Command::Trim),
            ("foo".into(), Command::Append(1)),
            ("bar".into(), Command::Append(5)),
        ]);
        assert_eq!(output[0], "HELLO");
        assert_eq!(output[1], "all roads lead to rome!");
        assert_eq!(output[2], "foobar");
        assert_eq!(output[3], "barbarbarbarbarbar");
    }
}
```
这个程序通过 `transformer` 函数接收一组包含字符串和操作命令的元组，对每个字符串执行不同的操作。命令可以将字符串转换为大写 (`Uppercase`)、去除空白 (`Trim`)，或在字符串末尾添加指定数量的 "bar" (`Append(usize)`)。处理后的字符串被收集到一个向量并返回。测试用例验证了不同命令的正确执行，确保函数的输出符合预期。。

相关资料：
[Storing Keys with Associated Values in Hash Maps](https://doc.rust-lang.org/book/ch08-03-hash-maps.html)

### Options 选项
Type Option represents an optional value: every Option is either Some and contains a value, or None, and does not. Option types are very common in Rust code, as they have a number of uses:

Type Option 表示一个可选值：每个 Option 要么是 Some 并包含一个值，要么是 None，但不包含。选项类型在 Rust 代码中非常常见，因为它们有很多用途：

- Initial values 初始值
- Return values for functions that are not defined over their entire input range (partial functions)
- 未在其整个输入范围内定义的函数（部分函数）的返回值
- Return value for otherwise reporting simple errors, where None is returned on error
- 否则报告简单错误的返回值，其中 None 在出错时返回 None
- Optional struct fields 可选 struct 字段
- Struct fields that can be loaned or “taken”
- 可以借用或“获取”的结构体字段
- Optional function arguments
- 可选函数参数
- Nullable pointers 可为 null 的指针
- Swapping things out of difficult situations
- 从困难的境地中换出

- **47Options1**:
`Option` 类型是 Rust 中用于处理可能为空或无效值的一种枚举类型。它有两个变体：

1. `Some(T)`：表示有一个有效的值 `T`。
2. `None`：表示没有值或无效。

在 `maybe_icecream` 函数中，`Option` 的使用很典型：

- 当时间在 10AM 到 10PM 之间时，返回 `Some(5)`，表示有 5 个冰淇淋可用。
- 当时间在 0AM 到 11PM 但不在 10AM 到 10PM 范围内时，返回 `Some(0)`，表示没有冰淇淋。
- 如果输入的时间无效（不在 0 到 23 的范围内），返回 `None`，表示这个时间无效，没有冰淇淋。

这展示了 `Option` 的基本使用方式，可以通过 `Some` 包含值，也可以通过 `None` 表示缺失或无效的情况。具体来说：

```rust
fn maybe_icecream(time_of_day: u16) -> Option<u16> {
    if time_of_day >= 10 && time_of_day <= 22 {
        Some(5) // 有效时间段，返回5份冰淇淋
    } else if time_of_day >= 0 && time_of_day <= 23 {
        Some(0) // 其他有效时间段，返回0
    } else {
        None // 无效时间，返回None
    }
}
```
通过 `Option`，Rust 避免了像空指针（null pointer）那样的错误处理机制，使得函数能够显式地处理缺失值情况。
- **48Options2**:
```RUST
#[cfg(test)]
mod tests {
    #[test]
    fn simple_option() {
        let target = "rustlings";
        let optional_target = Some(target);

        // 使用 if let 来匹配 Some(value) 模式
        if let Some(word) = optional_target {
            assert_eq!(word, target);
        }
    }

    #[test]
    fn layered_option() {
        let range = 10;
        let mut optional_integers: Vec<Option<i8>> = vec![None];

        for i in 1..=range {
            optional_integers.push(Some(i));
        }

        let mut cursor = range;

        // 使用 while let 来处理 Vec 中的 Option<i8>
        while let Some(Some(integer)) = optional_integers.pop() {
            assert_eq!(integer, cursor);
            cursor -= 1;
        }

        assert_eq!(cursor, 0);
    }
}
```
这个代码演示了如何在 Rust 中使用 `Option` 类型以及如何通过 `if let` 和 `while let` 来处理 `Option` 的值。

1. **`simple_option` 测试函数**:
   - 这里使用 `if let` 语法来解构 `Option` 类型中的 `Some` 值。它检查 `optional_target` 是否是 `Some(target)`，如果是，则解构出 `word` 并将其与 `target` 进行比较。`if let Some(word) = optional_target` 是一个简洁的方式来处理 `Option` 类型并获得其内部的值。

2. **`layered_option` 测试函数**:
   - 该函数创建一个包含 `Option<i8>` 类型的 `Vec`，初始值是 `None`，然后从 1 到 `range` 的值被包装在 `Some` 中添加到向量中。
   - 使用 `while let` 来不断从向量中弹出元素，并同时处理内部嵌套的 `Option`（即 `Some(Some(integer))`）。它在循环中解构 `Option` 的值并与递减的 `cursor` 进行比较，直到向量为空。
   - `while let` 提供了一种迭代处理 `Option` 值的方式，它可以解构嵌套的 `Option`，使得代码更清晰简洁。

#### 总结
这段代码展示了 `Option` 类型的基本使用。`if let` 和 `while let` 是用于处理 `Option` 内部值的简便方法，通过模式匹配的方式解构 `Some`，使得对 `Option` 的处理更加直观。
- **49Options3**:
在这段代码中，使用了 `match` 来处理 `Option<Point>`，其中 `Some(p)` 匹配到了值并将 `Point` 解构出来。然而，`match` 会“夺走”匹配到的值的所有权，导致后续再使用该值时出现错误。
### 问题分析：
当使用 `match` 将 `Some(p)` 解构为 `p` 时，`p` 的所有权转移到了 `println!` 语句中。因此，后续的变量 `y` 不再拥有该值的所有权，导致它无法再次被使用。
```rust
fn main() {
    let y: Option<Point> = Some(Point { x: 100, y: 200 });

    match y {
        Some(p) => println!("Co-ordinates are {},{} ", p.x, p.y), // 此处的 p 获取了所有权
        _ => panic!("no match!"),
    }
    let _ = y; // 因为 y 的所有权在上面的 match 中丢失，无法再继续使用 y
}
```
#### 解决方法：
可以通过引用来避免所有权的丢失，即使用 `ref` 或者直接使用引用 `&` 来借用值而不是取得所有权。
#### 解决方案 1：使用 `ref`
```rust
fn main() {
    let y: Option<Point> = Some(Point { x: 100, y: 200 });

    match &y {
        Some(p) => println!("Co-ordinates are {},{} ", p.x, p.y), // 通过引用借用
        _ => panic!("no match!"),
    }

    let _ = y; // y 可以继续被使用
}
```
#### 解决方案 2：使用 `as_ref()` 方法
`as_ref()` 可以将 `Option<T>` 转换为 `Option<&T>`，避免所有权转移。

```rust
fn main() {
    let y: Option<Point> = Some(Point { x: 100, y: 200 });

    match y.as_ref() {
        Some(p) => println!("Co-ordinates are {},{} ", p.x, p.y), // 通过引用借用
        _ => panic!("no match!"),
    }

    let _ = y; // y 仍然可以继续使用
}
```
#### 总结：
使用 `match` 解构 `Option` 时，如果直接解构内部的值，会导致所有权的丧失，后续无法再使用该值。为避免这个问题，可以使用 `&` 引用或 `as_ref()` 方法来借用而不转移所有权，从而在后续的代码中仍然可以使用该值。

### Error handling 错误处理
Most errors aren’t serious enough to require the program to stop entirely. Sometimes, when a function fails, it’s for a reason that you can easily interpret and respond to. For example, if you try to open a file and that operation fails because the file doesn’t exist, you might want to create the file instead of terminating the process.

大多数错误没有严重到需要程序完全停止的程度。有时，当函数失败时，你可以轻松解释和响应的原因。例如，如果您尝试打开某个文件，但该操作由于该文件不存在而失败，则可能需要创建该文件，而不是终止进程。

-**50_errors1**:
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn generates_nametag_text_for_a_nonempty_name() {
        assert_eq!(
            generate_nametag_text("Beyoncé".into()),
            Some("Hi! My name is Beyoncé".into()) // 修改为 Some
        );
    }

    #[test]
    fn explains_why_generating_nametag_text_fails() {
        assert_eq!(
            generate_nametag_text("".into()),
            None // 修改为 None
        );
    }
}
```
1. **返回值类型为 `Option<String>`**：`generate_nametag_text` 函数的返回类型是 `Option<String>`，用于表示可能的成功或失败情况。若输入的名称为空字符串，函数返回 `None`，表示无法生成名牌文本。

2. **异常判断的使用**：在测试中，通过 `assert_eq!` 语句验证了 `generate_nametag_text` 函数在处理有效和无效输入时的行为。有效输入（非空字符串）返回 `Some` 类型的值，包含生成的名牌文本；无效输入（空字符串）返回 `None`，指示失败。

3. **与空判断的相似性**：异常判断的逻辑与前面的空判断相似，均使用 `Option` 枚举来简化错误处理和控制流，使得代码更加清晰、易于维护。
-**51_errors2**:
```rust
use std::num::ParseIntError;

pub fn total_cost(item_quantity: &str) -> Result<i32, ParseIntError> {
    let processing_fee = 1;
    let cost_per_item = 5;

    // 解析数量，并使用 match 处理可能的错误
    let qty = item_quantity.parse::<i32>()?;

    // 计算并返回总费用
    Ok(qty * cost_per_item + processing_fee)
}
```
`total_cost` 函数计算项目的总费用，接收一个表示数量的字符串切片作为输入，并返回一个 `Result<i32, ParseIntError>` 类型的结果。
函数内部定义了处理费用和每个项目的费用。它使用 `parse::<i32>()` 方法尝试将输入字符串解析为 `i32` 类型，并通过 `?` 操作符简化了错误处理，如果解析失败则返回错误。
在成功解析后，函数计算并返回总费用，即数量乘以单个项目费用加上处理费用。
这样的设计确保了在输入无效时程序不会崩溃，同时提高了代码的可读性和简洁性。
-**52_errors3**:

-**53_errors4**:

-**54_errors5**:

-**55_errors6**: