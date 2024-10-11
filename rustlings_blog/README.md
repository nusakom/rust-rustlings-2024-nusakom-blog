## 2024/9/30
##### Options 选项
Type Option represents an optional value: every Option is either Some and contains a value, or None, and does not. Option types are very common in Rust code, as they have a number of uses:

Type Option 表示一个可选值：每个 Option 要么是 Some 并包含一个值，要么是 None，但不包含。选项类型在 Rust 代码中非常常见，因为它们有很多用途：

- Initial values 初始值
- Return values for functions that are not defined over their entire input range (partial functions)
未在其整个输入范围内定义的函数（部分函数）的返回值

- Return value for otherwise reporting simple errors, where None is returned on error
否则报告简单错误的返回值，其中 None 在出错时返回 None

- Optional struct fields 可选 struct 字段
- Struct fields that can be loaned or "taken"
可以借用或“获取”的结构体字段

- Optional function arguments
可选函数参数

- Nullable pointers 可为 null 的指针
- Swapping things out of difficult situations
从困难的境地中换出
1.**options1**:
为了改进 raw_value 测试，应该确保代码在 Option 为 None 的情况下不会 panic。可以使用模式匹配或 unwrap_or 等方法来提供默认值，而不是直接 panic。
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn check_icecream() {
        assert_eq!(maybe_icecream(9), Some(0));
        assert_eq!(maybe_icecream(10), Some(5));
        assert_eq!(maybe_icecream(23), Some(0));
        assert_eq!(maybe_icecream(22), Some(5));
        assert_eq!(maybe_icecream(25), None);
    }

    #[test]
    fn raw_value() {
        let icecreams = maybe_icecream(12);
        // 使用 match 语句避免 panic
        match icecreams {
            Some(value) => assert_eq!(value, 5),
            None => panic!("预期为 Some(5)，但得到 None"),
        }
    }
}
```
- 在 raw_value 测试中，我用一个 match 语句替换了 unwrap()，这样可以检查值是否为 Some(value)。如果值为 None，则会带有自定义消息的 panic，而不是无上下文的 panic。
2.**options2**:
1. **更具体的测试用例**：
   - `simple_option` 测试非常直接且有效。你可以考虑添加其他测试，例如对 `None` 的处理，以确保代码的健壮性。

2. **边界情况的处理**：
   - 在 `layered_option` 测试中，你可以测试边界条件，比如确保向量中至少有一个 `None` 元素时的行为。

3. **测试信息更明确**：
   - 你可以在 `assert_eq!` 中添加一些描述性信息，帮助识别测试失败的原因。

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn simple_option() {
        let target = "rustlings";
        let optional_target = Some(target);

        // 使用 if let 来匹配 Some(value) 模式
        if let Some(word) = optional_target {
            assert_eq!(word, target, "预期值与目标不匹配");
        }

        // 测试 None 情况
        let optional_none: Option<&str> = None;
        assert!(optional_none.is_none(), "预期为 None");
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
            assert_eq!(integer, cursor, "预期为 {}, 但得到 {}", cursor, integer);
            cursor -= 1;
        }

        // 测试最后 cursor 的值
        assert_eq!(cursor, 0, "cursor 预期为 0");
        
        // 确保向量最终包含 None
        assert!(optional_integers.contains(&None), "向量中应包含 None");
    }
}
```
3.**opyions3**:
1. **处理 `None` 的情况**：
   - 在生产代码中，触发 `panic` 通常不是最佳实践。可以考虑更优雅的错误处理方式，例如打印提示信息或返回默认值。

2. **使用 `if let` 语句**：
   - 对于简单的模式匹配，你可以使用 `if let` 语句，这样代码会更简洁。

3. **添加更多功能**：
   - 可以添加一些方法来增强 `Point` 结构体的功能，比如计算两点之间的距离等。
```rust
struct Point {
    x: i32,
    y: i32,
}

impl Point {
    // 示例：计算与另一点的距离
    fn distance(&self, other: &Point) -> f64 {
        (((self.x - other.x).pow(2) + (self.y - other.y).pow(2)) as f64).sqrt()
    }
}

fn main() {
    let y: Option<Point> = Some(Point { x: 100, y: 200 });

    if let Some(p) = y {
        println!("Co-ordinates are {},{}", p.x, p.y);
        
        // 例如，计算与另一个点的距离
        let other_point = Point { x: 50, y: 50 };
        println!("Distance to point ({}, {}) is {}", other_point.x, other_point.y, p.distance(&other_point));
    } else {
        println!("No point available.");
    }

    let _ = y; // 使用 let _ 来抑制未使用变量的警告
}
```
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

#### Options 选项
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
#### 问题分析：
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

#### Error handling 错误处理
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
代码中使用了 ? 操作符，但 ? 要求所在的函数返回一个 Result 类型，而代码使用 main 函数并没有返回 Result，因此会导致编译错误。
```RUST
fn main() {
    let mut tokens = 100;
    let pretend_user_input = "8";

    match total_cost(pretend_user_input) {
        Ok(cost) => {
            if cost > tokens {
                println!("You can't afford that many!");
            } else {
                tokens -= cost;
                println!("You now have {} tokens.", tokens);
            }
        }
        Err(e) => {
            println!("Error parsing item quantity: {}", e);
        }
    }
}
```
-**53_errors4**:
代码中存在一个逻辑问题：PositiveNonzeroInteger::new 函数没有正确处理负数和零的情况。当前的实现总是返回 Ok(PositiveNonzeroInteger)，即使输入值是负数或零。

需要在 new 函数中添加对输入值的检查，以根据不同的输入值返回正确的 Result 类型。
```rust
impl PositiveNonzeroInteger {
    fn new(value: i64) -> Result<PositiveNonzeroInteger, CreationError> {
        if value < 0 {
            Err(CreationError::Negative)
        } else if value == 0 {
            Err(CreationError::Zero)
        } else {
            Ok(PositiveNonzeroInteger(value as u64))
        }
    }
}
```
-**54_errors5**:
代码中 main 函数需要返回一个 Result，但当前的返回类型 Box<dyn ???> 还没有定义具体的错误类型。可以通过将返回类型设为 Box<dyn std::error::Error> 来解决这个问题，因为 CreationError 实现了 std::error::Error trait
```rust
fn main() -> Result<(), Box<dyn error::Error>> {
    let pretend_user_input = "42";
    let x: i64 = pretend_user_input.parse()?;
    println!("output={:?}", PositiveNonzeroInteger::new(x)?);
    Ok(())
}
```
-**55_errors6**:
代码中有一个 ParsePosNonzeroError 枚举，它用于表示在解析正非零整数时可能出现的错误。当前的实现只包含一种类型的错误（CreationError），需要添加另一个错误转换函数来处理 ParseIntError
- 添加错误转换函数

1. **添加错误转换函数**：

```rust
impl ParsePosNonzeroError {
    fn from_creation(err: CreationError) -> ParsePosNonzeroError {
        ParsePosNonzeroError::Creation(err)
    }

    fn from_parseint(err: ParseIntError) -> ParsePosNonzeroError {
        ParsePosNonzeroError::ParseInt(err)
    }
}
```

2. **修改 `parse_pos_nonzero` 函数**：

将原来的 `unwrap()` 改为使用 `map_err` 来处理解析错误：

```rust
fn parse_pos_nonzero(s: &str) -> Result<PositiveNonzeroInteger, ParsePosNonzeroError> {
    let x: i64 = s.parse().map_err(ParsePosNonzeroError::from_parseint)?;
    PositiveNonzeroInteger::new(x).map_err(ParsePosNonzeroError::from_creation)
}
```
#### Generics 泛型
Generics is the topic of generalizing types and functionalities to broader cases. 
This is extremely useful for reducing code duplication in many ways, but can call for some rather involved syntax. Namely, 
being generic requires taking great care to specify over which types a generic type is actually considered valid. 
The simplest and most common use of generics is for type parameters.

泛型是将类型和功能推广到更广泛的情况的主题。这对于在许多方面减少代码重复非常有用，但可能需要一些相当复杂的语法。
就是说，泛型需要非常小心地指定泛型类型实际上被视为有效的类型。泛型最简单和最常见的用途是类型参数。
-**第56题**:
在代码中，`Vec<?>` 表示你需要指定向量 `Vec` 的元素类型。由于你正在往 `shopping_list` 中添加 `"milk"` 这一字符串，
因此向量的元素类型应该是字符串类型 `&str` 或者 `String`。
```rust
fn main() {
    let mut shopping_list: Vec<&str> = Vec::new();
    shopping_list.push("milk");
}
```
或者使用 `String` 类型：
```rust
fn main() {
    let mut shopping_list: Vec<String> = Vec::new();
    shopping_list.push("milk".to_string());
}
```
`&str` 是字符串的切片，适合用于静态字符串，而 `String` 是一个可动态分配的字符串类型，适合更复杂的字符串操作。
如果你不需要对字符串进行复杂操作，`&str` 是更简洁的选择。
-**第57题**:
尝试将字符串 "Foo" 存储在 Wrapper 结构体中时会报错，因为 Wrapper 结构体的 value 字段被定义为 u32 类型，尝试传递的是一个 &str 类型的值。
Wrapper 能够存储不同类型的值，可以使用泛型来解决这一题
```rust
struct Wrapper<T> {
    value: T,
}

impl<T> Wrapper<T> {
    pub fn new(value: T) -> Self {
        Wrapper { value }
    }
}
```

#### Traits 性状
A trait is a collection of methods.
trait 是方法的集合。

Data types can implement traits. To do so, the methods making up the trait are defined for the data type. For example, the String data type implements the From<&str> trait. This allows a user to write String::from("hello").
数据类型可以实现特征。为此，为数据类型定义了构成特征的方法。例如，String 数据类型实现 From<&str> trait。这允许用户编写 String：：from（“你好”）。

In this way, traits are somewhat similar to Java interfaces and C++ abstract classes.
通过这种方式，traits 有点类似于 Java 接口和 C++ 抽象类。

Some additional common Rust traits include:
一些其他常见的 Rust 特征包括：

- Clone (the clone method)
  Clone （克隆方法）

- Display (which allows formatted display via {})
  显示（允许通过 {} 进行格式化显示）

- Debug (which allows formatted display via {:?})
  Debug （允许通过 {：？} 进行格式化显示）

Because traits indicate shared behavior between data types, they are useful when writing generics.
由于 trait 表示数据类型之间的共享行为，因此它们在编写泛型时非常有用。

Further information 
更多信息
[Traits 性状](https://doc.rust-lang.org/book/ch10-02-traits.html)
-**第58题**:
要实现 AppendBar trait 以便为 String 类型添加 append_bar 方法，你需要在 append_bar 方法中将字符串 "Bar" 附加到原始字符串的末尾。

以下是如何实现这个 trait 的示例：
```rust
trait AppendBar {
    fn append_bar(self) -> Self;
}

impl AppendBar for String {
    // TODO: Implement `AppendBar` for the type `String`.
    fn append_bar(self) -> Self {
        self + "Bar"
    }
}
```
-**第59题**:
要实现 AppendBar trait，使其可以用于 Vec<String>，你需要在 append_bar 方法中将字符串 "Bar" 添加到向量的末尾。

以下是实现的示例代码：
```rust
trait AppendBar {
    fn append_bar(self) -> Self;
}

// Implement `AppendBar` for `Vec<String>`.
impl AppendBar for Vec<String> {
    fn append_bar(mut self) -> Self {
        self.push(String::from("Bar"));
        self
    }
}
```
-**第60题**:
##### 方法1
为了让 SomeSoftware 和 OtherSoftware 都实现 Licensed trait,并且它们的 licensing_info 方法返回相同的字符串信息，
你需要为这两个结构体实现 licensing_info 方法。下面是如何实现这个功能的示例代码：
```rust
impl Licensed for SomeSoftware {
    fn licensing_info(&self) -> String {
        String::from("Some information")
    }
}

impl Licensed for OtherSoftware {
    fn licensing_info(&self) -> String {
        String::from("Some information")
    }
}
```
通过这样的实现，确保了两个不同类型的软件可以共享相同的许可信息，同时满足了测试用例的要求。
##### 方法2
licensing_info 方法的默认实现可以直接放在 Licensed trait 的定义中。
这样，任何实现了 Licensed trait 的类型都可以使用这个默认实现，除非它们选择重写这个方法。以下是修改后的代码示例：
```rust
trait Licensed {
    fn licensing_info(&self) -> String {
        String::from("Default license")
    }
}
```
通过这种方式，你可以在 Licensed trait 中提供一个通用的许可证信息实现，供所有类型使用，同时保留重写的灵活性。
-**第61题**:
为了使 compare_license_types 函数能够接受实现了 Licensed 特征的任意类型，我们需要在函数签名中使用特征约束。
具体来说，我们可以使用泛型和特征约束来指定两个参数都必须实现 Licensed 特征。以下是修改后的代码：
```rust
// 你只能更改下一行
fn compare_license_types<T: Licensed, U: Licensed>(software: T, software_two: U) -> bool {
    software.licensing_info() == software_two.licensing_info()
}
```
-**第62题**:
要使 some_func 函数接受实现了 SomeTrait 和 OtherTrait 的类型，你可以使用特征约束来指定它接收的类型必须同时实现这两个特征。
具体来说，可以使用 impl Trait 语法来表达这个约束。以下是修改后的代码：
```rust
fn some_func<T: SomeTrait + OtherTrait>(item: T) -> bool {
    item.some_function() && item.other_function()
}

fn main() {
    // 这里可以调用 some_func，使用实现了这两个特征的类型
    println!("{}", some_func(SomeStruct {})); // 输出: true
    println!("{}", some_func(OtherStruct {})); // 输出: true
}
```
#### quiz3 
要使 ReportCard 支持以字母等级（如 A+、A、B 等）输出，而不仅仅是数字成绩，我们可以在 ReportCard 结构体中添加一个方法，
该方法将根据 grade 的值返回相应的字母成绩。在这种情况下，我们可以定义一个新的方法 letter_grade，并在 print 方法中调用它。
```rust
pub struct ReportCard {
    pub grade: f32,
    pub student_name: String,
    pub student_age: u8,
}

impl ReportCard {
    pub fn print(&self) -> String {
        format!(
            "{} ({}) - achieved a grade of {}",
            &self.student_name,
            &self.student_age,
            self.letter_grade() // 调用新方法来获取字母成绩
        )
    }

    // 新增的方法，用于返回字母成绩
    pub fn letter_grade(&self) -> String {
        match self.grade {
            4.0..=5.0 => "A+".to_string(),
            3.5..=3.9 => "A".to_string(),
            3.0..=3.4 => "B".to_string(),
            2.5..=2.9 => "C".to_string(),
            2.0..=2.4 => "D".to_string(),
            0.0..=1.9 => "F".to_string(),
            _ => "Invalid grade".to_string(), // 处理无效的成绩
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn generate_numeric_report_card() {
        let report_card = ReportCard {
            grade: 2.1,
            student_name: "Tom Wriggle".to_string(),
            student_age: 12,
        };
        assert_eq!(
            report_card.print(),
            "Tom Wriggle (12) - achieved a grade of D"
        ); // 更新了预期结果
    }

    #[test]
    fn generate_alphabetic_report_card() {
        // 使用不同的成绩以确保字母成绩的功能正常
        let report_card = ReportCard {
            grade: 4.1, // 改为 4.1 以便测试 A+
            student_name: "Gary Plotter".to_string(),
            student_age: 11,
        };
        assert_eq!(
            report_card.print(),
            "Gary Plotter (11) - achieved a grade of A+"
        );
    }
}
```
#### Lifetimes 寿命
Lifetimes tell the compiler how to check whether references live long enough to be valid in any given situation.
 For example lifetimes say “make sure parameter
 ‘a’ lives as long as parameter ‘b’ so that the return value is valid”.
生命周期告诉编译器如何检查引用的生存期是否足够长，以便在任何给定情况下都有效。例如，lifetimes 表示“确保参数 'a' 的
存续时间与参数 'b' 的存续时间一样长，以便返回值有效”。

They are only necessary on borrows, i.e. references, since copied parameters or moves are owned in their
 scope and cannot be referenced outside. 
Lifetimes mean that calling code of e.g. functions can be checked to make sure their arguments are valid.
 Lifetimes are restrictive of their callers.
它们仅在 borrows（即引用）上是必需的，因为复制的参数或 moves 在其范围内拥有，不能在外部引用。生命周期意味着
可以检查函数等的调用代码以确保其参数有效。生命周期对他们的呼叫者是有限制的。

If you’d like to learn more about lifetime annotations, the lifetimekata project has a similar 
style of exercises to Rustlings, but is all about learning to write lifetime annotations.
如果你想了解更多关于生命周期注解的信息，lifetimekata 项目有与 Rustlings 类似的练习风格，但都是关于学习编写生命周期注解的。

-**第63题**:

`longest` 函数试图返回对 `x` 和 `y` 字符串的引用。然而，存在一个生命周期问题，因为 `string1` 的生命周期在 `longest` 函数返回后会结束，从而导致可能的悬垂引用。

为了解决这个问题，通常有两种方法：

1. **返回一个字符串切片（`&str`）并确保它不会引用已经被释放的内存**。
2. **返回 `String` 类型**，而不是 `&str`，这样可以避免生命周期问题。

这里使用返回 `String` 类型的方法：

```rust
fn longest(x: String, y: &str) -> String {
    if x.len() > y.len() {
        x
    } else {
        y.to_string() // 将 y 转换为 String
    }
}

fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1, string2); // 直接传入 string1
    println!("The longest string is '{}'", result);
}
```
-**第64题**:
在你提供的代码中，`longest` 函数使用了生命周期标注来确保返回值的引用在输入引用的生命周期内有效。然而，代码的实现存在一个问题，因为 `string2` 在其作用域结束时被释放，导致 `result` 可能会引用已被释放的内存，从而造成悬垂引用（dangling reference）。

要解决这个问题，可以使用以下两种方法：

1. **将 `string2` 的作用域移出**，使其在 `result` 的使用中保持有效。
2. **返回一个 `String` 类型** 而不是引用，这样避免了生命周期问题。

以下是修正后的代码示例，采用第一种方法：

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn main() {
    let string1 = String::from("long string is long");
    let string2 = String::from("xyz"); // 将 string2 的声明移到外部

    // 现在 string2 在整个 main 函数的作用域内有效
    let result = longest(string1.as_str(), string2.as_str());
    
    println!("The longest string is '{}'", result);
}
```

##### 代码说明

1. **`string2` 的作用域**：
   - `string2` 的声明被移到了 `main` 函数的外面，确保在调用 `longest` 函数时，`string2` 仍然有效。这样就避免了悬垂引用的问题。

2. **保持生命周期的有效性**：
   - 因为 `string2` 的生命周期在 `main` 函数中，所以 `result` 在使用时不会引用已经释放的内存。

##### 使用 `String` 作为返回值

使用 `String` 作为返回值来避免生命周期问题，以下是相应的代码示例：

```rust
fn longest(x: &str, y: &str) -> String {
    if x.len() > y.len() {
        x.to_string()
    } else {
        y.to_string()
    }
}

fn main() {
    let string1 = String::from("long string is long");
    let string2 = String::from("xyz");

    let result = longest(string1.as_str(), string2.as_str());
    
    println!("The longest string is '{}'", result);
}
```
`longest` 函数返回一个 `String` 类型，消除了生命周期问题，使其更安全。你可以根据需要选择使用哪种方法。
-**第65题**:
结构体 `Book` 使用了字符串切片（`&str`）作为字段类型，但直接使用字符串切片引用 `String` 类型的变量是不可行的，
因为 `String` 的生命周期通常会超出其引用的使用范围，这可能导致悬垂引用。

为了修复这个问题，可以选择两种方法：

1. **使用 `String` 类型作为字段类型**。
2. **在 `Book` 结构体中使用生命周期标注**。

下面是这两种方法的实现示例：

##### 方法 1:使用 `String` 类型

这是最简单的解决方案，将 `Book` 结构体中的字段类型更改为 `String`。

```rust
struct Book {
    author: String,
    title: String,
}

fn main() {
    let name = String::from("Jill Smith");
    let title = String::from("Fish Flying");
    let book = Book { author: name, title }; // 直接移动字符串所有权

    println!("{} by {}", book.title, book.author);
}
```

##### 方法 2:使用生命周期标注

使用字符串切片（`&str`），那么需要添加生命周期标注。

```rust
struct Book<'a> { // 添加生命周期标注
    author: &'a str,
    title: &'a str,
}

fn main() {
    let name = String::from("Jill Smith");
    let title = String::from("Fish Flying");
    
    // 这里需要使用 `as_str()` 方法来获取 `String` 的切片
    let book = Book { author: name.as_str(), title: title.as_str() };

    println!("{} by {}", book.title, book.author);
}
```

1. **方法 1**：在 `Book` 结构体中使用 `String` 类型，允许在创建 `Book` 实例时将 `String` 的所有权转移给结构体。
这消除了生命周期的问题。
   
2. **方法 2**：使用生命周期标注来确保 `author` 和 `title` 的切片引用在 `name` 和 `title` 的有效范围内。
这里使用 `as_str()` 方法获取 `String` 的切片。

请注意，这两种方法都有其适用场景。选择哪种方法取决于你的具体需求和代码结构。
#### Tests 测试
Going out of order from the book to cover tests - many of the following exercises will ask you to make tests pass!

从书中不按顺序来涵盖测试 - 以下许多练习都会要求您通过测试！

tests1:介绍一下test模块是长什么样的。assert!(true); 让它无事发生即可。
tests2:assert_eq!(1,1); 这个断言就是检查是否相等,前面rustlings很多题都出现过了。
tests3：刚开始默认是assert_eq!，然后发现是assert!
```rust
pub fn is_even(num: i32) -> bool {
    num % 2 == 0
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn is_true_when_even() {
        assert!(is_even(2));
    }

    #[test]
    fn is_false_when_odd() {
        assert!(!is_even(5));
    }
}
```
tests4: #[should_panic]特性。当被测试代码panic时来进行处理。

#### Iterators 迭代器
This section will teach you about Iterators.
本节将教你 Iterators。

Further information 更多信息
Iterator 迭 代
Iterator documentation 迭代器文档
-**test1**
你想要在 Rust 中使用迭代器来检查你最喜欢的水果，并且需要在 `TODO` 部分填充代码。这里是如何实现这个目标的示例。

```rust
fn main() {
    let my_fav_fruits = vec!["banana", "custard apple", "avocado", "peach", "raspberry"];

    let mut my_iterable_fav_fruits = my_fav_fruits.iter(); // Step 1: 初始化迭代器

    assert_eq!(my_iterable_fav_fruits.next(), Some(&"banana")); // 检查第一个值
    assert_eq!(my_iterable_fav_fruits.next(), Some(&"custard apple")); // Step 2: 检查第二个值
    assert_eq!(my_iterable_fav_fruits.next(), Some(&"avocado")); // 检查第三个值
    assert_eq!(my_iterable_fav_fruits.next(), Some(&"peach")); // Step 3: 检查第四个值
    assert_eq!(my_iterable_fav_fruits.next(), Some(&"raspberry")); // 检查第五个值
    assert_eq!(my_iterable_fav_fruits.next(), None); // Step 4: 检查没有更多值
}
```

1. **Step 1**: `let mut my_iterable_fav_fruits = my_fav_fruits.iter();`  
   这里使用 `.iter()` 方法创建一个迭代器，允许你逐个访问向量中的元素。

2. **Step 2**: `assert_eq!(my_iterable_fav_fruits.next(), Some(&"custard apple"));`  
   检查第二个元素是否为 `"custard apple"`。

3. **Step 3**: `assert_eq!(my_iterable_fav_fruits.next(), Some(&"peach"));`  
   检查第四个元素是否为 `"peach"`。

4. **Step 4**: `assert_eq!(my_iterable_fav_fruits.next(), None);`  
   最后，检查当没有更多元素时，迭代器应该返回 `None`。

pub fn capitalize_first(input: &str) -> String {
    let mut c = input.chars();
    match c.next() {
        None => String::new(),
        Some(first) => {
            let mut result = String::new();
            result.push(first.to_uppercase().next().unwrap()); // 将首字母转为大写
            result.push_str(c.as_str()); // 添加剩余字符
            result
        }
    }
}
-**test2**
```rust
// Step 2.
// Apply the `capitalize_first` function to a slice of string slices.
// Return a vector of strings.
// ["hello", "world"] -> ["Hello", "World"]
pub fn capitalize_words_vector(words: &[&str]) -> Vec<String> {
    words.iter().map(|&word| capitalize_first(word)).collect() // 使用map和collect
}

// Step 3.
// Apply the `capitalize_first` function again to a slice of string slices.
// Return a single string.
// ["hello", " ", "world"] -> "Hello World"
pub fn capitalize_words_string(words: &[&str]) -> String {
    words.iter()
        .map(|&word| capitalize_first(word))
        .collect::<Vec<String>>() // 收集到Vec<String>
        .join(" ") // 连接成一个单一字符串，用空格分隔
}
```
-**test3**
**-`divide` 函数能够正确处理所有可能的情况。**：
   - **处理除以零的情况**：根据测试用例，当 `b == 0` 时，我们需要返回一个 `Err(DivisionError::DivideByZero)`，这就是添加 `if b == 0` 的原因。
   - **处理无法整除的情况**：当 `a` 不能被 `b` 整除时（即 `a % b != 0`），我们需要返回一个 `Err(DivisionError::NotDivisible(NotDivisibleError { dividend: a, divisor: b }))`，这与测试中“无法整除”的错误处理相符。
   - **处理可以整除的情况**：如果 `a % b == 0`，则返回整除的结果 `Ok(a / b)`。这对应测试中成功的除法操作。

**`result_with_list` 返回整除操作的成功结果，或者遇到错误时终止。**：
   - 原来的函数没有返回值类型，通过分析测试用例可知，需要返回 `Ok([1, 11, 1426, 3])` 这样的成功结果列表。因此，将所有 `divide` 的结果收集成 `Result<Vec<i32>, DivisionError>`。当所有除法都成功时，返回 `Ok(数字列表)`。
   - 使用 `map` 来遍历 `numbers`，并通过 `collect` 将结果聚合成一个 `Result<Vec<i32>, DivisionError>`，确保所有除法结果都成功时返回 `Ok`，否则返回第一个遇到的错误。

**`list_of_results` 返回每次操作的结果，不管是否成功。**：
   - 根据测试用例，该函数应返回 `[Ok(1), Ok(11), Ok(1426), Ok(3)]`，即一个 `Vec<Result<i32, DivisionError>>`。与 `result_with_list` 不同的是，`list_of_results` 无论每次除法是否成功，都会返回每次操作的结果。
   - 使用 `map` 遍历 `numbers`，并通过 `collect` 将每次 `divide` 的结果存储在 `Vec<Result<i32, DivisionError>>` 中。

```rust
pub fn divide(a: i32, b: i32) -> Result<i32, DivisionError> {
    if b == 0 {
        Err(DivisionError::DivideByZero)
    } else if a % b != 0 {
        Err(DivisionError::NotDivisible(NotDivisibleError { dividend: a, divisor: b }))
    } else {
        Ok(a / b)
    }
}

fn result_with_list() -> Result<Vec<i32>, DivisionError> {
    let numbers = vec![27, 297, 38502, 81];
    numbers.into_iter().map(|n| divide(n, 27)).collect()
}

fn list_of_results() -> Vec<Result<i32, DivisionError>> {
    let numbers = vec![27, 297, 38502, 81];
    numbers.into_iter().map(|n| divide(n, 27)).collect()
}
```
-**test4**
- 无需循环或递归：通过使用范围和迭代器，避免了显式的循环或递归。

- 简洁：使用 .product() 方法可以直接计算出所需结果，减少了额外的逻辑处理和变量定义。
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn factorial_of_0() {
        assert_eq!(1, factorial(0)); // 阶乘0的结果为1
    }

    #[test]
    fn factorial_of_1() {
        assert_eq!(1, factorial(1)); // 阶乘1的结果为1
    }

    #[test]
    fn factorial_of_2() {
        assert_eq!(2, factorial(2)); // 阶乘2的结果为2
    }

    #[test]
    fn factorial_of_4() {
        assert_eq!(24, factorial(4)); // 阶乘4的结果为24
    }
}
```
-**test5**
为了实现 count_iterator 和 count_collection_iterator 函数，我们可以使用迭代器来简化代码，使其更加优雅。

- 实现 count_iterator 函数： 该函数可以通过使用迭代器链来简化查找 Progress 状态的次数。我们可以调用 map.values().filter() 过滤出符合条件的值，再使用 count() 来获取符合条件的元素数量。

- 实现 count_collection_iterator 函数： 该函数同样可以使用迭代器链来遍历 collection 中的每个 HashMap，并累计每个 HashMap 中符合条件的 Progress 数量。
```rust
fn count_iterator(map: &HashMap<String, Progress>, value: Progress) -> usize {
    map.values().filter(|&&v| v == value).count()
}

fn count_collection_iterator(collection: &[HashMap<String, Progress>], value: Progress) -> usize {
    collection.iter().map(|map| count_iterator(map, value)).sum()
}
```
#### Smart Pointers 智能指针
In Rust, smart pointers are variables that contain an address in memory and reference some other data, but they also have additional metadata and capabilities. Smart pointers in Rust often own the data they point to, while references only borrow data.

在 Rust 中，智能指针是包含内存中地址并引用一些其他数据的变量，但它们也具有额外的元数据和功能。Rust 中的智能指针通常拥有它们指向的数据，而引用只借用数据。
-**arc1.rs**:
这个问题要求将一个向量（numbers）分发到多个线程中计算每个偏移量（offset）的数字和。我们可以使用 Arc（原子引用计数指针）来共享数据并确保其在多线程环境下是安全的。

- 共享数据： 为了在多个线程之间共享 numbers，我们使用了 Arc，它是原子引用计数智能指针，可以让多个线程安全地共享数据。由于 Vec 不是线程安全的（尤其是当多个线程同时访问时），我们不能直接共享向量引用，需要使用 Arc。

- 克隆 Arc： 在循环中，我们使用 Arc::clone 方法来克隆 Arc，这样每个线程都能拥有一个共享 numbers 的引用。注意，这不是深拷贝，Arc 只会增加引用计数，仍然共享同一份数据。

- 线程安全： 使用 Arc 确保 numbers 在多线程环境下的安全访问，而不需要锁等复杂的同步原语。每个线程都可以安全地读取 numbers 的内容。
```rust
#![forbid(unused_imports)] // Do not change this, (or the next) line.
use std::sync::Arc;
use std::thread;

fn main() {
    let numbers: Vec<_> = (0..100u32).collect();
    let shared_numbers = Arc::new(numbers); // 使用 Arc 来共享数据
    let mut joinhandles = Vec::new();

    for offset in 0..8 {
        let child_numbers = Arc::clone(&shared_numbers); // 克隆 Arc 来共享给每个线程
        joinhandles.push(thread::spawn(move || {
            let sum: u32 = child_numbers.iter().filter(|&&n| n % 8 == offset).sum();
            println!("Sum of offset {} is {}", offset, sum);
        }));
    }
    for handle in joinhandles.into_iter() {
        handle.join().unwrap();
    }
}
```
-**box1.rs**:
为了完成这个代码，首先我们需要正确地实现 List 的枚举类型，然后实现 create_empty_list 和 create_non_empty_list 函数，使它们能够生成一个空的和非空的链表。List 类型是一个递归数据结构，用于实现单向链表。
```rust
#[derive(PartialEq, Debug)]
pub enum List {
    Cons(i32, Box<List>), // 使用 Box 来包裹递归枚举
    Nil,
}

fn main() {
    println!("This is an empty cons list: {:?}", create_empty_list());
    println!(
        "This is a non-empty cons list: {:?}",
        create_non_empty_list()
    );
}

pub fn create_empty_list() -> List {
    List::Nil // 返回空列表
}

pub fn create_non_empty_list() -> List {
    List::Cons(1, Box::new(List::Cons(2, Box::new(List::Nil)))) // 创建一个非空链表
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_create_empty_list() {
        assert_eq!(List::Nil, create_empty_list())
    }

    #[test]
    fn test_create_non_empty_list() {
        assert_ne!(create_empty_list(), create_non_empty_list())
    }
}
```
- 递归枚举类型： 为了实现递归的 List 枚举，我们需要使用 Box，因为 Rust 需要知道枚举的大小，而递归类型不能直接确定大小。通过将 List 包装在 Box 中，能够延迟大小的确定，并实现递归结构。

- create_empty_list 实现： 这个函数非常简单，只是返回 List::Nil，表示空链表。

- create_non_empty_list 实现： 这个函数返回一个非空的链表，通过递归地构造 Cons 节点。Cons 构造器包含一个整数值和下一个 List 节点的 Box，最终以 Nil 结束链表。
-**cow1.rs**:
```rust
use std::borrow::Cow;

fn abs_all<'a, 'b>(input: &'a mut Cow<'b, [i32]>) -> &'a mut Cow<'b, [i32]> {
    for i in 0..input.len() {
        let v = input[i];
        if v < 0 {
            // Clones into a vector if not already owned.
            input.to_mut()[i] = -v;
        }
    }
    input
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn reference_mutation() -> Result<(), &'static str> {
        // Clone occurs because `input` needs to be mutated.
        let slice = [-1, 0, 1];
        let mut input = Cow::from(&slice[..]);
        match abs_all(&mut input) {
            Cow::Owned(_) => Ok(()),
            _ => Err("Expected owned value"),
        }
    }

    #[test]
    fn reference_no_mutation() -> Result<(), &'static str> {
        // No clone occurs because `input` doesn't need to be mutated.
        let slice = [0, 1, 2];
        let mut input = Cow::from(&slice[..]);
        match abs_all(&mut input) {
            Cow::Borrowed(_) => Ok(()), // No mutation, so it remains borrowed
            _ => Err("Expected borrowed value"),
        }
    }

    #[test]
    fn owned_no_mutation() -> Result<(), &'static str> {
        // We can also pass `slice` without `&` so Cow owns it directly. In this
        // case no mutation occurs and thus also no clone, but the result is
        // still owned because it was never borrowed or mutated.
        let slice = vec![0, 1, 2];
        let mut input = Cow::from(slice);
        match abs_all(&mut input) {
            Cow::Owned(_) => Ok(()), // Owned, and no mutation occurred
            _ => Err("Expected owned value"),
        }
    }

    #[test]
    fn owned_mutation() -> Result<(), &'static str> {
        // Of course this is also the case if a mutation does occur. In this
        // case the call to `to_mut()` returns a reference to the same data as
        // before.
        let slice = vec![-1, 0, 1];
        let mut input = Cow::from(slice);
        match abs_all(&mut input) {
            Cow::Owned(_) => Ok(()), // Owned, mutation occurred
            _ => Err("Expected owned value"),
        }
    }
}
```
- reference_no_mutation：
在这个测试用例中，输入是一个借用的 slice 且不需要修改，因为所有元素都是非负的。因此，期望的结果是 Cow::Borrowed，因为没有任何变异发生，也没有必要克隆。

- owned_no_mutation：
在这个测试用例中，Cow 拥有数据（是一个 vector），但不需要修改数据，因为所有元素都是非负的。因此，它保持为 Cow::Owned 类型，并没有发生克隆。

- owned_mutation：
在这个测试用例中，Cow 拥有数据，但因为包含负数，调用 to_mut() 后会修改数据并保持为 Cow::Owned 类型。这就是期望的行为。

通过这些修改，可以正确处理 Cow 类型的借用和所有权情况，并测试在不同条件下是否会发生克隆或变异。
-**rc1.rs**:
在当前代码中，saturn, uranus, 和 neptune 这三个行星是用 Rc::new(Sun {}) 创建的独立的 Sun 实例，因此它们的引用计数不会影响到之前创建的 sun 实例。

要修复这个问题，并确保所有行星都共享同一个 Sun 实例，需要为 Saturn, Uranus, 和 Neptune 也使用 Rc::clone(&sun) 而不是 Rc::new(Sun {})。此外，正确地减少引用计数后也要删除剩下的行星，确保引用计数逐步回到 1。
```rust
use std::rc::Rc;

#[derive(Debug)]
struct Sun {}

#[derive(Debug)]
enum Planet {
    Mercury(Rc<Sun>),
    Venus(Rc<Sun>),
    Earth(Rc<Sun>),
    Mars(Rc<Sun>),
    Jupiter(Rc<Sun>),
    Saturn(Rc<Sun>),
    Uranus(Rc<Sun>),
    Neptune(Rc<Sun>),
}

impl Planet {
    fn details(&self) {
        println!("Hi from {:?}!", self)
    }
}

fn main() {
    let sun = Rc::new(Sun {});
    println!("reference count = {}", Rc::strong_count(&sun)); // 1 reference

    let mercury = Planet::Mercury(Rc::clone(&sun));
    println!("reference count = {}", Rc::strong_count(&sun)); // 2 references
    mercury.details();

    let venus = Planet::Venus(Rc::clone(&sun));
    println!("reference count = {}", Rc::strong_count(&sun)); // 3 references
    venus.details();

    let earth = Planet::Earth(Rc::clone(&sun));
    println!("reference count = {}", Rc::strong_count(&sun)); // 4 references
    earth.details();

    let mars = Planet::Mars(Rc::clone(&sun));
    println!("reference count = {}", Rc::strong_count(&sun)); // 5 references
    mars.details();

    let jupiter = Planet::Jupiter(Rc::clone(&sun));
    println!("reference count = {}", Rc::strong_count(&sun)); // 6 references
    jupiter.details();

    let saturn = Planet::Saturn(Rc::clone(&sun)); // Now using Rc::clone
    println!("reference count = {}", Rc::strong_count(&sun)); // 7 references
    saturn.details();

    let uranus = Planet::Uranus(Rc::clone(&sun)); // Now using Rc::clone
    println!("reference count = {}", Rc::strong_count(&sun)); // 8 references
    uranus.details();

    let neptune = Planet::Neptune(Rc::clone(&sun)); // Now using Rc::clone
    println!("reference count = {}", Rc::strong_count(&sun)); // 9 references
    neptune.details();

    assert_eq!(Rc::strong_count(&sun), 9);

    drop(neptune);
    println!("reference count = {}", Rc::strong_count(&sun)); // 8 references

    drop(uranus);
    println!("reference count = {}", Rc::strong_count(&sun)); // 7 references

    drop(saturn);
    println!("reference count = {}", Rc::strong_count(&sun)); // 6 references

    drop(jupiter);
    println!("reference count = {}", Rc::strong_count(&sun)); // 5 references

    drop(mars);
    println!("reference count = {}", Rc::strong_count(&sun)); // 4 references

    drop(earth); // Dropping Earth
    println!("reference count = {}", Rc::strong_count(&sun)); // 3 references

    drop(venus); // Dropping Venus
    println!("reference count = {}", Rc::strong_count(&sun)); // 2 references

    drop(mercury); // Dropping Mercury
    println!("reference count = {}", Rc::strong_count(&sun)); // 1 reference

    assert_eq!(Rc::strong_count(&sun), 1);
}
```
- 将 Saturn, Uranus, 和 Neptune 改为使用 Rc::clone(&sun)，使得它们与其他行星共享同一个 Sun 实例。
- 在后面的 drop 操作中，依次删除所有行星，逐步减少 Rc 的强引用计数，直到最后只剩下 1 个引用。
- 最后确保引用计数为 1，且每一步操作输出预期的引用计数。
#### Threads 线程
In most current operating systems, an executed program’s code is run in a process, and the operating system manages multiple processes at once. Within your program, you can also have independent parts that run simultaneously. The features that run these independent parts are called threads.
在大多数当前的操作系统中，已执行程序的代码在进程中运行，操作系统一次管理多个进程。在您的程序中，您还可以拥有同时运行的独立部分。运行这些独立部分的功能称为线程。
-**threads1.rs**:
```rust
// Join the thread and retrieve the result
let result = handle.join().expect("Thread panicked");
results.push(result); // Push the result into the results vector
```
- 线程返回值：thread::spawn 返回一个 JoinHandle，你需要通过 handle.join() 来等待线程完成并获取其返回值。这是线程的输出，即每个线程执行的耗时。

- 错误处理：使用 expect("Thread panicked") 来处理可能的线程崩溃，这样可以确保在主线程中捕获到任何错误，并避免在运行时出现未处理的错误。

- 结果存储：将每个线程的执行时间（以毫秒为单位）存储在 results 向量中，以便在所有线程完成后进行输出。
-**threads2.rs**:
需要对共享的 JobStatus 结构体的 jobs_completed 字段进行安全地更新和读取。由于多个线程可能同时访问这个字段，您需要使用某种同步机制，例如 Mutex，以确保线程安全
1. **使用 `Mutex`**：

   将 `JobStatus` 包装在一个 `Mutex` 中，以确保对 `jobs_completed` 的安全访问。

2. **更新代码**：

   修改 `JobStatus` 和线程代码如下：

```rust
use std::sync::{Arc, Mutex}; // Import Mutex

struct JobStatus {
    jobs_completed: u32,
}

fn main() {
    let status = Arc::new(Mutex::new(JobStatus { jobs_completed: 0 })); // Wrap in Mutex
    let mut handles = vec![];

    for _ in 0..10 {
        let status_shared = Arc::clone(&status);
        let handle = thread::spawn(move || {
            thread::sleep(Duration::from_millis(250));
            // Lock the mutex before updating
            let mut status = status_shared.lock().unwrap(); // Lock the mutex
            status.jobs_completed += 1; // Update jobs_completed
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
        // Lock the mutex before reading
        let status = status.lock().unwrap(); // Lock the mutex
        println!("jobs completed {}", status.jobs_completed); // Print the value
    }
}
```
**线程安全**：`Mutex` 确保在某一时刻只有一个线程能够访问 `jobs_completed` 字段，避免数据竞争和不一致的状态。

**锁的使用**：在更新和读取 `jobs_completed` 时，您需要先锁定 `Mutex`，这确保了在对其进行操作时，其他线程无法同时访问。

**解锁**：`Mutex` 的作用域确保在离开块时自动释放锁，确保线程能够顺利地执行。
-**threads3.rs**:
在这段代码中，您希望将数据从 `Queue` 的两个部分通过多线程发送到主线程。然而，当前的实现中存在一些问题，特别是在 `send_tx` 函数中，线程的生命周期管理和接收的数据处理。我们将做一些必要的修改来确保代码正常运行，并且在发送完数据后能正确关闭发送端。

1. **确保线程完成**：在 `send_tx` 函数中，我们应该等待所有线程完成其工作，以确保主线程不会在它们完成之前退出。
   
2. **使用 `join`**：为了确保发送的顺序不会混淆，可以使用 `join` 来等待线程结束。

3. **需要考虑 `mpsc::Sender` 的范围**：需要将 `tx` 传递到每个线程中，以确保发送操作不会在主线程中提前结束。

```rust
use std::sync::mpsc;
use std::sync::{Arc, Mutex}; // 引入 Mutex
use std::thread;
use std::time::Duration;

struct Queue {
    length: u32,
    first_half: Vec<u32>,
    second_half: Vec<u32>,
}

impl Queue {
    fn new() -> Self {
        Queue {
            length: 10,
            first_half: vec![1, 2, 3, 4, 5],
            second_half: vec![6, 7, 8, 9, 10],
        }
    }
}

fn send_tx(q: Arc<Mutex<Queue>>, tx: mpsc::Sender<u32>) {
    let qc1 = Arc::clone(&q);
    let qc2 = Arc::clone(&q);

    // 启动发送 first_half 的线程
    let handle1 = thread::spawn(move || {
        let queue = qc1.lock().unwrap(); // 锁住 Queue
        for val in &queue.first_half {
            println!("sending {:?}", val);
            tx.send(*val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    // 启动发送 second_half 的线程
    let handle2 = thread::spawn(move || {
        let queue = qc2.lock().unwrap(); // 锁住 Queue
        for val in &queue.second_half {
            println!("sending {:?}", val);
            tx.send(*val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    handle1.join().unwrap(); // 等待线程1完成
    handle2.join().unwrap(); // 等待线程2完成
}

fn main() {
    let (tx, rx) = mpsc::channel();
    let queue = Arc::new(Mutex::new(Queue::new())); // 将 Queue 包装在 Arc 和 Mutex 中

    send_tx(queue, tx);

    let mut total_received: u32 = 0;
    for received in rx {
        println!("Got: {}", received);
        total_received += 1;
    }

    println!("total numbers received: {}", total_received);
    assert_eq!(total_received, 10); // 因为长度是 10
}
```

1. **使用 `Arc<Mutex<Queue>>`**：通过将 `Queue` 包装在 `Arc` 和 `Mutex` 中，确保多个线程能够安全访问 `Queue`。

2. **线程同步**：通过使用 `join()` 方法，确保主线程在所有发送线程完成之前不会继续执行，这样可以确保所有数据都被发送。

3. **锁定 `Queue`**：在发送数据之前，使用 `lock()` 方法锁定 `Mutex`，保证在访问 `Queue` 的同时不会发生数据竞争。
#### Macros 宏
Rust's macro system is very powerful, but also kind of difficult to wrap your head around. We're not going to teach you how to write your own fully-featured macros. Instead, we'll show you how to use and create them.

Rust 的宏系统非常强大，但也很难理解。我们不会教你如何编写自己的功能齐全的宏。相反，我们将向您展示如何使用和创建它们。

If you'd like to learn more about writing your own macros, the macrokata project has a similar style of exercises to Rustlings, but is all about learning to write Macros.

如果您想了解有关编写自己的宏的更多信息，macrokata 项目具有与 Rustlings 类似的练习风格，但都是关于学习编写宏的。
-**macros1.rs**:
定义了一个简单的 Rust 宏 my_macro，并在 main 函数中调用它。这个宏在执行时会打印出一条消息。
```rust
macro_rules! my_macro {
    () => {
    println!("Check out my macro!");
     };
    }
    
    fn main() {
    my_macro!();
    }
```
宏定义：
1.-**macro_rules! my_macro**:这行代码声明了一个宏，名字为 my_macro。
() => { ... }: 这是宏的模式和替换。() 表示该宏不接受任何参数，=> 后面的内容是宏展开时的代码。
println!("Check out my macro!");: 这是宏展开后将要执行的代码。
宏调用：
2.-**my_macro()**: 这行代码在 main 函数中调用了 my_macro，执行时会输出 Check out my macro!。
-**macros2.rs**:
my_macro 的调用在 main 函数中发生，但宏的定义位于 main 函数之后。这会导致编译错误，因为 Rust 需要在使用宏之前已经知道其定义。
```rust
macro_rules! my_macro {
    () => {
        println!("Check out my macro!");
    };
}

fn main() {
    my_macro!(); // 调用宏
}
```
-**macros3.rs**:
-**宏定义**：
- macro_rules! my_macro: 声明一个宏，命名为 my_macro。
- () => { ... }: 表示该宏不接受参数，=> 后面的代码是当宏被调用时将执行的内容。
- println!("Check out my macro!");: 当宏被调用时，它将打印这条消息。
-**宏调用**：
-在 main 函数中调用 my_macro!();，宏将展开并执行其定义中的代码。
-**macros4.rs**:
my_macro 的定义包含两个不同的模式，但在语法上存在问题。您无法在同一个宏定义中使用两个相同的宏名称而没有分隔符。
```rust
#[rustfmt::skip]
macro_rules! my_macro {
    () => {
        println!("Check out my macro!");
    };
    ($val:expr) => {
        println!("Look at this other macro: {}", $val);
    };
}

fn main() {
    my_macro!();        // 调用不带参数的宏
    my_macro!(7777);    // 调用带参数的宏
}
```
要在同一个宏中定义多个模式，您需要确保它们之间用逗号分隔。
-**宏定义**：
- 使用 macro_rules! 来定义一个名为 my_macro 的宏。
- 第一个模式 () => { ... }：当宏被调用而不带参数时，打印 "Check out my macro!"。
- 第二个模式 ($val:expr) => { ... }：当宏被调用并传递一个表达式时，打印 "Look at this other macro: {值}"。
-**宏调用**：
- my_macro!()：调用不带参数的宏，输出 "Check out my macro!"。
- my_macro!(7777)：调用带参数的宏，输出 "Look at this other macro: 7777"。
#### Clippy 乐队
The Clippy tool is a collection of lints to analyze your code so you can catch common mistakes and improve your Rust code.

Clippy 工具是一组 lint 来分析您的代码，以便您可以捕获常见错误并改进 Rust 代码。

If you used the installation script for Rustlings, Clippy should be already installed. If not you can install it manually via rustup component add clippy.

如果您使用了 Rustlings 的安装脚本，则应该已经安装了 Clippy。如果没有，你可以通过 rustup 组件 add clippy 手动安装它。
-**clippy1.rs**:
可以使用 Rust 的标准库中的 std::f32::consts::PI
```rust
fn main() {
    let radius = 5.00f32;

    let area = std::f32::consts::PI * f32::powi(radius, 2);

    println!(
        "The area of a circle with radius {:.2} is {:.5}!",
        radius, area
    );
}
```
-**clippy2.rs**:
```rust
fn main() {  
    let mut res = 42;  
    let option = Some(12);  
  
    for x in option.iter() {  
        res += x;  
    }  
  
    println!("{}", res);  
}
```
-**clippy3.rs**:
```rust
fn main() {
    let my_option: Option<()> = None;
    if my_option.is_none() {
        // 使用if let来处理None的情况
        println!("This will not be printed because my_option is None");
    }

    let my_arr = &[
        -1, -2, -3, // 添加了逗号
        -4, -5, -6,
    ];
    println!("My array! Here it is: {:?}", my_arr);

    let mut my_empty_vec = vec![1, 2, 3, 4, 5];
    my_empty_vec.clear(); // 使用clear方法来清空向量
    println!("This Vec is empty, see? {:?}", my_empty_vec);

    let mut value_a = 45;
    let mut value_b = 66;
    // 使用模式匹配来交换变量
    (value_a, value_b) = (value_b, value_a);

    println!("value a: {}; value b: {}", value_a, value_b);
}
```
1. **`unwrap` 的使用**：
   ```rust
   my_option.unwrap();
   ```
   - 由于 `my_option` 是 `None`，调用 `unwrap()` 会导致程序在运行时崩溃。这里可以使用 `if my_option.is_none()` 来检查它是否为 `None`，并根据需要采取适当的措施（如打印消息）。
2. **数组定义中的缺少逗号**：
   ```rust
   let my_arr = &[
       -1, -2, -3 // 这里缺少逗号
       -4, -5, -6
   ];
   ```
   - 在数组元素 `-3` 和 `-4` 之间缺少逗号。正确的语法应该是：
   ```rust
   let my_arr = &[
       -1, -2, -3,
       -4, -5, -6,
   ];
   ```
3. **`resize` 方法的使用**：
   ```rust
   let my_empty_vec = vec![1, 2, 3, 4, 5].resize(0, 5);
   ```
   - `resize` 方法会返回一个 `()` 类型，而不是一个新的 `Vec`。需要先创建一个 `Vec`，然后再调用 `resize`。因此，应该修改为：
   ```rust
   let my_empty_vec: Vec<i32> = vec![1, 2, 3, 4, 5]; // 先创建向量
   let my_empty_vec = my_empty_vec.resize(0, 5); // 这将返回一个新的 Vec，因此您需要分配它
   ```
   - 注意：在 Rust 中，`resize` 方法直接在 `Vec` 上使用，因此上面的代码可能会导致不必要的混淆。正确的方式是这样：
   ```rust
   let mut my_vec = vec![1, 2, 3, 4, 5];
   my_vec.resize(0, 5);
   ```
4. **交换两个变量的值**：
   ```rust
   value_a = value_b;
   value_b = value_a;
   ```
   - 这种方式并不会交换两个值，最后 `value_a` 和 `value_b` 都将是 `66`。使用 `std::mem::swap` 函数可以更清晰地交换两个变量：
   ```rust
   std::mem::swap(&mut value_a, &mut value_b);
   ```
#### ype conversions 类型转换
Rust offers a multitude of ways to convert a value of a given type into another type.

Rust 提供了多种方法将给定类型的值转换为另一种类型。

The simplest form of type conversion is a type cast expression. It is denoted with the binary operator as. For instance, println!("{}", 1 + 1.0); would not compile, since 1 is an integer while 1.0 is a float. However, println!("{}", 1 as f32 + 1.0) should compile. The exercise using_as tries to cover this.

类型转换的最简单形式是类型转换表达式。它用二元运算符表示为 。

例如，println！（”{}“， 1 + 1.0）;不会编译，因为 1 是整数，而 1.0 是浮点数。但是， println!("{}", 1 as f32 + 1.0) 应该编译。练习 using_as 试图涵盖这一点。

Rust also offers traits that facilitate type conversions upon implementation. These traits can be found under the convert module. The traits are the following:

Rust 还提供了有助于在实现时进行类型转换的 trait。这些特征可以在 convert 模块下找到。特征如下：

- From and Into covered in from_into
 From 并 Into 覆盖在 from_into

- TryFrom and TryInto covered in try_from_into
 TryFrom 并 TryInto 覆盖在 try_from_into

- AsRef and AsMut covered in as_ref_mut
 AsRef 并 AsMut 覆盖在 as_ref_mut

Furthermore, the std::str module offers a trait called FromStr which helps with converting strings into target types via the parse method on strings. If properly implemented for a given type Person, then let p: Person = "Mark,20".parse().unwrap() should both compile and run without panicking.

此外，std：：str 模块提供了一个名为 FromStr 的 trait，它有助于通过字符串的 parse 方法将字符串转换为目标类型。如果为给定类型的 Person 正确实现，那么 let p: Person = "Mark,20".parse().unwrap() 编译和运行都应该不会出现 panic。

These should be the main ways within the standard library to convert data into your desired types.

这些应该是标准库中将数据转换为所需类型的主要方法。
-**as_ref_mut.rs**:
代码定义了一些函数来计算字符串的字节和字符数，并实现了一个对数字进行平方的函数。代码整体看起来很好，但有一个问题需要解决：`num_sq` 函数的 `arg` 参数缺少适当的 trait bound。以下是修改后的代码以及对每个部分的详细说明。
```rust
fn byte_counter<T: AsRef<str>>(arg: T) -> usize {
    arg.as_ref().as_bytes().len()
}

fn char_counter<T: AsRef<str>>(arg: T) -> usize {
    arg.as_ref().chars().count()
}

// Squares a number using as_mut().
// Adding the appropriate trait bound for &mut T where T: std::ops::MulAssign
fn num_sq<T: std::ops::MulAssign>(arg: &mut T) {
    *arg *= *arg;
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn different_counts() {
        let s = "Café au lait";
        assert_ne!(char_counter(s), byte_counter(s));
    }

    #[test]
    fn same_counts() {
        let s = "Cafe au lait";
        assert_eq!(char_counter(s), byte_counter(s));
    }

    #[test]
    fn different_counts_using_string() {
        let s = String::from("Café au lait");
        assert_ne!(char_counter(s.clone()), byte_counter(s));
    }

    #[test]
    fn same_counts_using_string() {
        let s = String::from("Cafe au lait");
        assert_eq!(char_counter(s.clone()), byte_counter(s));
    }

    #[test]
    fn mult_box() {
        let mut num: Box<u32> = Box::new(3);
        num_sq(&mut num);
        assert_eq!(*num, 9);
    }
}
```
1. **`num_sq` 函数的 trait bound**：
   - 原始代码中，`num_sq` 函数的参数缺少适当的 trait bound。为了让 `*arg *= *arg` 能够正常工作，必须确保 `T` 实现 `std::ops::MulAssign` trait，这样才能进行乘法赋值操作。修改后的函数签名如下：
     ```rust
     fn num_sq<T: std::ops::MulAssign>(arg: &mut T) {
         *arg *= *arg;
     }
     ```
2. **测试模块**：
   - 测试代码没有变化，仍然验证了字节和字符计数的不同情况，以及 `num_sq` 函数是否能正确地平方一个 `Box<u32>` 类型的数值。
-**from_into.rs**:
代码定义了一个 `Person` 结构体，并实现了从字符串转换为 `Person` 的逻辑。实现的目标是使得 `let p = Person::from("Mark,20")` 这样的代码能够正确编译并执行。代码已经相当完善，但我们可以稍微优化一下注释，并确保代码可读性。
```rust
#[derive(Debug)]
struct Person {
    name: String,
    age: usize,
}

// We implement the Default trait to use it as a fallback
// when the provided string is not convertible into a Person object
impl Default for Person {
    fn default() -> Person {
        Person {
            name: String::from("John"),
            age: 30,
        }
    }
}

// Implementing From trait for Person to convert from a string to a Person object
impl From<&str> for Person {
    fn from(s: &str) -> Person {
        // Step 1: If the length of the provided string is 0, return the default Person
        if s.is_empty() {
            return Person::default();
        }

        // Step 2: Split the string on commas
        let parts: Vec<&str> = s.split(',').collect();
        
        // Step 3: Check if there are enough parts (at least 2)
        if parts.len() < 2 {
            return Person::default();
        }

        // Step 4: Extract the name
        let name = parts[0].trim().to_string();
        if name.is_empty() {
            return Person::default();
        }

        // Step 5: Extract and parse the age
        let age_result = parts[1].trim().parse::<usize>();
        if let Ok(age) = age_result {
            return Person { name, age };
        }

        Person::default()
    }
}

fn main() {
    // Use the `from` function
    let p1 = Person::from("Mark,20");
    // Since From is implemented for Person, we can use Into
    let p2: Person = "Gerald,70".into();
    println!("{:?}", p1);
    println!("{:?}", p2);
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_default() {
        let dp = Person::default();
        assert_eq!(dp.name, "John");
        assert_eq!(dp.age, 30);
    }

    #[test]
    fn test_bad_convert() {
        let p = Person::from("");
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 30);
    }

    #[test]
    fn test_good_convert() {
        let p = Person::from("Mark,20");
        assert_eq!(p.name, "Mark");
        assert_eq!(p.age, 20);
    }

    #[test]
    fn test_bad_age() {
        let p = Person::from("Mark,twenty");
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 30);
    }

    #[test]
    fn test_missing_comma_and_age() {
        let p: Person = Person::from("Mark");
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 30);
    }

    #[test]
    fn test_missing_age() {
        let p: Person = Person::from("Mark,");
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 30);
    }

    #[test]
    fn test_missing_name() {
        let p: Person = Person::from(",1");
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 30);
    }

    #[test]
    fn test_missing_name_and_age() {
        let p: Person = Person::from(",");
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 30);
    }

    #[test]
    fn test_missing_name_and_invalid_age() {
        let p: Person = Person::from(",one");
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 30);
    }

    #[test]
    fn test_trailing_comma() {
        let p: Person = Person::from("Mike,32,");
        assert_eq!(p.name, "Mike");
        assert_eq!(p.age, 32);
    }

    #[test]
    fn test_trailing_comma_and_some_string() {
        let p: Person = Person::from("Mike,32,man");
        assert_eq!(p.name, "Mike");
        assert_eq!(p.age, 32);
    }
}
```
##### 主要实现逻辑

1. **`Default` Trait**：实现 `Default` trait 以提供默认的 `Person` 实例。
2. **`From` Trait**：
   - `from` 函数从字符串转换为 `Person` 实例。
   - 在转换过程中，首先检查字符串是否为空；如果为空，返回默认的 `Person`。
   - 将字符串按逗号分割，提取名称和年龄。如果名称为空或解析年龄失败，返回默认的 `Person`。
   - 使用 `trim()` 方法清理可能的空白字符，确保名称和年龄被正确解析。

##### 测试

包含多个测试用例，验证了不同情况下 `from` 方法的正确性，包括：
- 空字符串转换为默认值。
- 正确转换的字符串。
- 错误格式字符串的处理。
- 各种缺失组件的字符串的处理。
-**from_str.rs**:
```rust
use std::num::ParseIntError;
use std::str::FromStr;

#[derive(Debug, PartialEq)]
struct Person {
    name: String,
    age: usize,
}

// We will use this error type for the `FromStr` implementation.
#[derive(Debug, PartialEq)]
enum ParsePersonError {
    // Empty input string
    Empty,
    // Incorrect format of input string
    InvalidFormat,
    // Empty name field
    NoName,
    // Wrapped error from parse::<usize>()
    ParseInt(ParseIntError),
}

// Steps:
// 1. If the length of the provided string is 0, an error should be returned
// 2. Split the given string on the commas present in it
// 3. Only 2 elements should be returned from the split, otherwise return an
//    error
// 4. Extract the first element from the split operation and use it as the name
// 5. Extract the other element from the split operation and parse it into a
//    `usize` as the age with something like `"4".parse::<usize>()`
// 6. If while extracting the name and the age something goes wrong, an error
//    should be returned
// If everything goes well, then return a Result of a Person object

impl FromStr for Person {
    type Err = ParsePersonError;

    fn from_str(s: &str) -> Result<Person, Self::Err> {
        if s.is_empty() {
            return Err(ParsePersonError::Empty);
        }

        let parts: Vec<&str> = s.split(',').collect();
        if parts.len() != 2 {
            return Err(ParsePersonError::InvalidFormat);
        }

        let name = parts[0].to_string();
        if name.is_empty() {
            return Err(ParsePersonError::NoName);
        }

        let age: Result<usize, ParseIntError> = parts[1].parse();
        let age = age.map_err(ParsePersonError::ParseInt)?;

        Ok(Person { name, age })
    }
}

fn main() {
    let p = "Mark,20".parse::<Person>().unwrap();
    println!("{:?}", p);
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn empty_input() {
        assert_eq!("".parse::<Person>(), Err(ParsePersonError::Empty));
    }

    #[test]
    fn good_input() {
        let p = "John,32".parse::<Person>();
        assert!(p.is_ok());
        let p = p.unwrap();
        assert_eq!(p.name, "John");
        assert_eq!(p.age, 32);
    }

    #[test]
    fn missing_age() {
        assert!(matches!(
            "John,".parse::<Person>(),
            Err(ParsePersonError::ParseInt(_))
        ));
    }

    #[test]
    fn invalid_age() {
        assert!(matches!(
            "John,twenty".parse::<Person>(),
            Err(ParsePersonError::ParseInt(_))
        ));
    }

    #[test]
    fn missing_comma_and_age() {
        assert_eq!("John".parse::<Person>(), Err(ParsePersonError::InvalidFormat));
    }

    #[test]
    fn missing_name() {
        assert_eq!(",1".parse::<Person>(), Err(ParsePersonError::NoName));
    }

    #[test]
    fn missing_name_and_age() {
        assert!(matches!(
            ",".parse::<Person>(),
            Err(ParsePersonError::NoName | ParsePersonError::ParseInt(_))
        ));
    }

    #[test]
    fn missing_name_and_invalid_age() {
        assert!(matches!(
            ",one".parse::<Person>(),
            Err(ParsePersonError::NoName | ParsePersonError::ParseInt(_))
        ));
    }

    #[test]
    fn trailing_comma() {
        assert_eq!("John,32,".parse::<Person>(), Err(ParsePersonError::InvalidFormat));
    }

    #[test]
    fn trailing_comma_and_some_string() {
        assert_eq!(
            "John,32,man".parse::<Person>(),
            Err(ParsePersonError::InvalidFormat)
        );
    }
}
```

1. **`ParsePersonError` 枚举的修改：**
   - 将 `BadLen` 改为 `InvalidFormat`，使错误信息更明确。

2. **`from_str` 方法中的错误处理：**
   - 将 `parts.len() != 2` 的错误返回修改为 `InvalidFormat`，确保错误类型与修改后的枚举一致。
-**try_from_into.rs**:
```rust
use std::convert::{TryFrom, TryInto};

#[derive(Debug, PartialEq)]
struct Color {
    red: u8,
    green: u8,
    blue: u8,
}

// We will use this error type for these `TryFrom` conversions.
#[derive(Debug, PartialEq)]
enum IntoColorError {
    // Incorrect length of slice
    BadLen,
    // Integer conversion error
    IntConversion,
}

// Implement TryFrom for tuple
impl TryFrom<(i16, i16, i16)> for Color {
    type Error = IntoColorError;

    fn try_from(tuple: (i16, i16, i16)) -> Result<Self, Self::Error> {
        let (red, green, blue) = tuple;
        if red < 0 || red > 255 || green < 0 || green > 255 || blue < 0 || blue > 255 {
            return Err(IntoColorError::IntConversion);
        }
        Ok(Color {
            red: red as u8,
            green: green as u8,
            blue: blue as u8,
        })
    }
}

// Implement TryFrom for array
impl TryFrom<[i16; 3]> for Color {
    type Error = IntoColorError;

    fn try_from(arr: [i16; 3]) -> Result<Self, Self::Error> {
        let [red, green, blue] = arr;
        if red < 0 || red > 255 || green < 0 || green > 255 || blue < 0 || blue > 255 {
            return Err(IntoColorError::IntConversion);
        }
        Ok(Color {
            red: red as u8,
            green: green as u8,
            blue: blue as u8,
        })
    }
}

// Implement TryFrom for slice
impl TryFrom<&[i16]> for Color {
    type Error = IntoColorError;

    fn try_from(slice: &[i16]) -> Result<Self, Self::Error> {
        if slice.len() != 3 {
            return Err(IntoColorError::BadLen);
        }
        let (red, green, blue) = (slice[0], slice[1], slice[2]);
        if red < 0 || red > 255 || green < 0 || green > 255 || blue < 0 || blue > 255 {
            return Err(IntoColorError::IntConversion);
        }
        Ok(Color {
            red: red as u8,
            green: green as u8,
            blue: blue as u8,
        })
    }
}

fn main() {
    // Use the `try_from` function
    let c1 = Color::try_from((183, 65, 14));
    println!("{:?}", c1);

    // Since TryFrom is implemented for Color, we should be able to use TryInto
    let c2: Result<Color, _> = [183, 65, 14].try_into();
    println!("{:?}", c2);

    let v = vec![183, 65, 14];
    // With slice we should use `try_from` function
    let c3 = Color::try_from(&v[..]);
    println!("{:?}", c3);
    // or take slice within round brackets and use TryInto
    let c4: Result<Color, _> = (&v[..]).try_into();
    println!("{:?}", c4);
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_tuple_out_of_range_positive() {
        assert_eq!(
            Color::try_from((256, 1000, 10000)),
            Err(IntoColorError::IntConversion)
        );
    }

    #[test]
    fn test_tuple_out_of_range_negative() {
        assert_eq!(
            Color::try_from((-1, -10, -256)),
            Err(IntoColorError::IntConversion)
        );
    }

    #[test]
    fn test_tuple_sum() {
        assert_eq!(
            Color::try_from((-1, 255, 255)),
            Err(IntoColorError::IntConversion)
        );
    }

    #[test]
    fn test_tuple_correct() {
        let c: Result<Color, _> = (183, 65, 14).try_into();
        assert!(c.is_ok());
        assert_eq!(
            c.unwrap(),
            Color {
                red: 183,
                green: 65,
                blue: 14
            }
        );
    }

    #[test]
    fn test_array_out_of_range_positive() {
        let c: Result<Color, _> = [1000, 10000, 256].try_into();
        assert_eq!(c, Err(IntoColorError::IntConversion));
    }

    #[test]
    fn test_array_out_of_range_negative() {
        let c: Result<Color, _> = [-10, -256, -1].try_into();
        assert_eq!(c, Err(IntoColorError::IntConversion));
    }

    #[test]
    fn test_array_sum() {
        let c: Result<Color, _> = [-1, 255, 255].try_into();
        assert_eq!(c, Err(IntoColorError::IntConversion));
    }

    #[test]
    fn test_array_correct() {
        let c: Result<Color, _> = [183, 65, 14].try_into();
        assert!(c.is_ok());
        assert_eq!(
            c.unwrap(),
            Color {
                red: 183,
                green: 65,
                blue: 14
            }
        );
    }

    #[test]
    fn test_slice_out_of_range_positive() {
        let arr = [10000, 256, 1000];
        assert_eq!(
            Color::try_from(&arr[..]),
            Err(IntoColorError::IntConversion)
        );
    }

    #[test]
    fn test_slice_out_of_range_negative() {
        let arr = [-256, -1, -10];
        assert_eq!(
            Color::try_from(&arr[..]),
            Err(IntoColorError::IntConversion)
        );
    }

    #[test]
    fn test_slice_sum() {
        let arr = [-1, 255, 255];
        assert_eq!(
            Color::try_from(&arr[..]),
            Err(IntoColorError::IntConversion)
        );
    }

    #[test]
    fn test_slice_correct() {
        let v = vec![183, 65, 14];
        let c: Result<Color, _> = Color::try_from(&v[..]);
        assert!(c.is_ok());
        assert_eq!(
            c.unwrap(),
            Color {
                red: 183,
                green: 65,
                blue: 14
            }
        );
    }

    #[test]
    fn test_slice_excess_length() {
        let v = vec![0, 0, 0, 0];
        assert_eq!(Color::try_from(&v[..]), Err(IntoColorError::BadLen));
    }

    #[test]
    fn test_slice_insufficient_length() {
        let v = vec![0, 0];
        assert_eq!(Color::try_from(&v[..]), Err(IntoColorError::BadLen));
    }
}
```
1. **代码格式化和注释：**
   - 代码经过格式化，以提升可读性，尤其是在 `try_from` 实现中，确保每一段逻辑清晰。
   - 添加了注释以解释每个实现的目的，便于后续维护和理解。

2. **使用元组解构：**
   - 在 `impl TryFrom<&[i16]> for Color` 中，将 `let red = slice[0];` 等改为 `let (red, green, blue) = (slice[0], slice[1], slice[2]);`，使代码更简洁。

3. **保持错误类型一致性：**
   - 确保所有错误处理的一致性，返回类型与错误枚举 `IntoColorError` 一致。

4. **测试用例的完整性：**
   - 确保所有测试用例都涵盖了边界情况和正常情况，包括正负值、超出范围和正确范围的测试。
-**using_as.rs**:
如果切片为空，调用 `len()` 会返回 `0`，这将导致除以零的错误
```rust
fn average(values: &[f64]) -> f64 {
    if values.is_empty() {
        return 0.0; // 或者你可以选择 panic 或返回 `Option<f64>`
    }
    let total = values.iter().sum::<f64>();
    total / values.len() as f64
}

fn main() {
    let values = [3.5, 0.3, 13.0, 11.7];
    println!("{}", average(&values));
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn returns_proper_type_and_value() {
        assert_eq!(average(&[3.5, 0.3, 13.0, 11.7]), 7.125);
    }

    #[test]
    fn handles_empty_slice() {
        assert_eq!(average(&[]), 0.0);
    }
}
```
1. **处理空切片**：增加了一个检查，如果切片为空，则返回 `0.0`。你也可以考虑其他方法，比如返回 `Option<f64>` 或者直接 panic，具体取决于你的需求。
2. **将 `values.len()` 转换为 `f64`**：虽然 `values.len()` 是一个整数，但在进行除法时将其转换为 `f64` 是一种良好的实践，以避免潜在的类型问题。
#### algorithm
-**algorithm1.rs**：
`merge` 方法的目的是将两个已排序的链表合并成一个新的已排序链表。我们将使用双指针的方法遍历两个链表并依次将节点添加到新的链表中。以下是实现代码：
```rust
pub fn merge(mut list_a: LinkedList<T>, mut list_b: LinkedList<T>) -> Self {
    let mut merged_list = LinkedList::new();

    let mut current_a = list_a.start;
    let mut current_b = list_b.start;

    while current_a.is_some() || current_b.is_some() {
        match (current_a, current_b) {
            (Some(node_a), Some(node_b)) => {
                let val_a = unsafe { node_a.as_ref() };
                let val_b = unsafe { node_b.as_ref() };

                if val_a.val <= val_b.val {
                    merged_list.add(val_a.val.clone());
                    current_a = val_a.next; // Move to the next node in list_a
                } else {
                    merged_list.add(val_b.val.clone());
                    current_b = val_b.next; // Move to the next node in list_b
                }
            }
            (Some(node_a), None) => {
                let val_a = unsafe { node_a.as_ref() };
                merged_list.add(val_a.val.clone());
                current_a = val_a.next; // Move to the next node in list_a
            }
            (None, Some(node_b)) => {
                let val_b = unsafe { node_b.as_ref() };
                merged_list.add(val_b.val.clone());
                current_b = val_b.next; // Move to the next node in list_b
            }
        }
    }

    merged_list
}
```
1. **合并逻辑**：使用 `current_a` 和 `current_b` 指针遍历 `list_a` 和 `list_b`。在循环中比较当前节点的值，选择较小的值添加到 `merged_list`。
2. **节点克隆**：我们假设 `T` 实现了 `Clone` 特征，因此可以使用 `clone()` 方法。这样可以确保我们添加的是值的副本而不是原始节点的引用，避免潜在的生命周期问题。
3. **处理剩余节点**：如果某个链表中的节点处理完后，另一个链表还有剩余节点，继续将其添加到新链表中。

- **内存安全**：尽量避免使用 `unsafe`，如果能通过其他方法实现链表操作则更好，例如使用 `Rc<RefCell<Node<T>>>` 或 `Box<Node<T>>` 代替 `NonNull`，这样能更好地管理内存。
- **链表展示**：在 `Display` 实现中，链表的打印格式可以改进以便更好地阅读。可以考虑在打印时加入分隔符。
-**algorithm2.rs**：
 `reverse` 方法目前尚未实现，以下是对 `reverse` 函数的实现，它可以正确地反转链表，通过更新每个节点的 `next` 和 `prev` 指针。
```rust
use std::fmt::{self, Display, Formatter};
use std::ptr::NonNull;

#[derive(Debug)]
struct Node<T> {
    val: T,
    next: Option<NonNull<Node<T>>>,
    prev: Option<NonNull<Node<T>>>,
}

impl<T> Node<T> {
    fn new(t: T) -> Node<T> {
        Node {
            val: t,
            prev: None,
            next: None,
        }
    }
}

#[derive(Debug)]
struct LinkedList<T> {
    length: u32,
    start: Option<NonNull<Node<T>>>,
    end: Option<NonNull<Node<T>>>,
}

impl<T> Default for LinkedList<T> {
    fn default() -> Self {
        Self::new()
    }
}

impl<T> LinkedList<T> {
    pub fn new() -> Self {
        Self {
            length: 0,
            start: None,
            end: None,
        }
    }

    pub fn add(&mut self, obj: T) {
        let mut node = Box::new(Node::new(obj));
        node.next = None;
        node.prev = self.end;
        let node_ptr = Some(unsafe { NonNull::new_unchecked(Box::into_raw(node)) });
        match self.end {
            None => self.start = node_ptr,
            Some(end_ptr) => unsafe { (*end_ptr.as_ptr()).next = node_ptr },
        }
        self.end = node_ptr;
        self.length += 1;
    }

    pub fn get(&mut self, index: i32) -> Option<&T> {
        self.get_ith_node(self.start, index)
    }

    fn get_ith_node(&mut self, node: Option<NonNull<Node<T>>>, index: i32) -> Option<&T> {
        match node {
            None => None,
            Some(next_ptr) => match index {
                0 => Some(unsafe { &(*next_ptr.as_ptr()).val }),
                _ => self.get_ith_node(unsafe { (*next_ptr.as_ptr()).next }, index - 1),
            },
        }
    }

    pub fn reverse(&mut self) {
        let mut current = self.start;

        // 反转每个节点的 next 和 prev 指针
        while let Some(node_ptr) = current {
            let mut node = unsafe { node_ptr.as_mut() };
            // 交换 next 和 prev 指针
            let temp = node.next;
            node.next = node.prev;
            node.prev = temp;

            // 移动到下一个节点（原来的前一个节点）
            current = node.prev; // 现在的前一个节点是原链表中的下一个节点
        }

        // 交换 start 和 end 指针
        std::mem::swap(&mut self.start, &mut self.end);
    }
}

impl<T> Display for LinkedList<T>
where
    T: Display,
{
    fn fmt(&self, f: &mut Formatter) -> fmt::Result {
        match self.start {
            Some(node) => write!(f, "{}", unsafe { node.as_ref() }),
            None => Ok(()),
        }
    }
}

impl<T> Display for Node<T>
where
    T: Display,
{
    fn fmt(&self, f: &mut Formatter) -> fmt::Result {
        match self.next {
            Some(node) => write!(f, "{}, {}", self.val, unsafe { node.as_ref() }),
            None => write!(f, "{}", self.val),
        }
    }
}

#[cfg(test)]
mod tests {
    use super::LinkedList;

    #[test]
    fn create_numeric_list() {
        let mut list = LinkedList::<i32>::new();
        list.add(1);
        list.add(2);
        list.add(3);
        println!("Linked List is {}", list);
        assert_eq!(3, list.length);
    }

    #[test]
    fn create_string_list() {
        let mut list_str = LinkedList::<String>::new();
        list_str.add("A".to_string());
        list_str.add("B".to_string());
        list_str.add("C".to_string());
        println!("Linked List is {}", list_str);
        assert_eq!(3, list_str.length);
    }

    #[test]
    fn test_reverse_linked_list_1() {
        let mut list = LinkedList::<i32>::new();
        let original_vec = vec![2, 3, 5, 11, 9, 7];
        let reverse_vec = vec![7, 9, 11, 5, 3, 2];
        for i in 0..original_vec.len() {
            list.add(original_vec[i]);
        }
        println!("Linked List is {}", list);
        list.reverse();
        println!("Reversed Linked List is {}", list);
        for i in 0..original_vec.len() {
            assert_eq!(reverse_vec[i], *list.get(i as i32).unwrap());
        }
    }

    #[test]
    fn test_reverse_linked_list_2() {
        let mut list = LinkedList::<i32>::new();
        let original_vec = vec![34, 56, 78, 25, 90, 10, 19, 34, 21, 45];
        let reverse_vec = vec![45, 21, 34, 19, 10, 90, 25, 78, 56, 34];
        for i in 0..original_vec.len() {
            list.add(original_vec[i]);
        }
        println!("Linked List is {}", list);
        list.reverse();
        println!("Reversed Linked List is {}", list);
        for i in 0..original_vec.len() {
            assert_eq!(reverse_vec[i], *list.get(i as i32).unwrap());
        }
    }
}
```
- **遍历链表**：`reverse` 函数通过遍历链表中的每个节点，交换每个节点的 `next` 和 `prev` 指针。
- **更新指针**：交换后，函数移动到原来的前一个节点。
- **最终交换**：在循环结束后，交换链表的 `start` 和 `end` 指针，以反映反转后的顺序。

-**algorithm3.rs**：
为了实现一个简单的排序函数，我们可以使用 Rust 中的快速排序算法。下面是修改后的代码：
```rust
fn sort<T: Ord>(array: &mut [T]) {
    if array.len() <= 1 {
        return;
    }

    let pivot_index = partition(array);
    sort(&mut array[0..pivot_index]);
    sort(&mut array[pivot_index + 1..]);
}

fn partition<T: Ord>(array: &mut [T]) -> usize {
    let pivot_index = array.len() - 1;
    let pivot_value = &array[pivot_index];
    let mut i = 0;

    for j in 0..pivot_index {
        if &array[j] < pivot_value {
            array.swap(i, j);
            i += 1;
        }
    }
    array.swap(i, pivot_index);
    i
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_sort_1() {
        let mut vec = vec![37, 73, 57, 75, 91, 19, 46, 64];
        sort(&mut vec);
        assert_eq!(vec, vec![19, 37, 46, 57, 64, 73, 75, 91]);
    }
    #[test]
    fn test_sort_2() {
        let mut vec = vec![1];
        sort(&mut vec);
        assert_eq!(vec, vec![1]);
    }
    #[test]
    fn test_sort_3() {
        let mut vec = vec![99, 88, 77, 66, 55, 44, 33, 22, 11];
        sort(&mut vec);
        assert_eq!(vec, vec![11, 22, 33, 44, 55, 66, 77, 88, 99]);
    }
}
```
1. **泛型约束**：
   - 在 `sort` 函数的签名中添加了 `T: Ord` 约束，表示 `T` 类型的元素需要实现 `Ord` trait。这是因为排序需要比较元素的大小。

2. **递归排序逻辑**：
   - 添加了 `partition` 函数来实现快速排序的核心逻辑。这个函数选择最后一个元素作为基准（pivot），然后将数组重新排列，使得比基准小的元素在左侧，比基准大的元素在右侧。最后返回基准元素的最终位置。

3. **递归调用**：
   - 在 `sort` 函数中，对基准元素左右的子数组递归调用 `sort` 函数。这样将数组逐步分解并排序。
-**algorithm4.rs**：
为了完成二叉搜索树（Binary Search Tree, BST）实现，我们需要添加插入（`insert`）和搜索（`search`）的方法。下面是修改后的代码，包括这两个方法的实现：
```rust
use std::cmp::Ordering;
use std::fmt::Debug;

#[derive(Debug)]
struct TreeNode<T>
where
    T: Ord,
{
    value: T,
    left: Option<Box<TreeNode<T>>>,
    right: Option<Box<TreeNode<T>>>,
}

#[derive(Debug)]
struct BinarySearchTree<T>
where
    T: Ord,
{
    root: Option<Box<TreeNode<T>>>,
}

impl<T> TreeNode<T>
where
    T: Ord,
{
    fn new(value: T) -> Self {
        TreeNode {
            value,
            left: None,
            right: None,
        }
    }

    // Insert a node into the tree
    fn insert(&mut self, value: T) {
        match value.cmp(&self.value) {
            Ordering::Less => {
                if let Some(ref mut left) = self.left {
                    left.insert(value);
                } else {
                    self.left = Some(Box::new(TreeNode::new(value)));
                }
            }
            Ordering::Greater => {
                if let Some(ref mut right) = self.right {
                    right.insert(value);
                } else {
                    self.right = Some(Box::new(TreeNode::new(value)));
                }
            }
            Ordering::Equal => {
                // Duplicate values are ignored
            }
        }
    }
}

impl<T> BinarySearchTree<T>
where
    T: Ord,
{
    fn new() -> Self {
        BinarySearchTree { root: None }
    }

    // Insert a value into the BST
    fn insert(&mut self, value: T) {
        match self.root {
            Some(ref mut node) => node.insert(value),
            None => {
                self.root = Some(Box::new(TreeNode::new(value)));
            }
        }
    }

    // Search for a value in the BST
    fn search(&self, value: T) -> bool {
        self.search_recursive(&self.root, value)
    }

    // Helper function for search
    fn search_recursive(&self, node: &Option<Box<TreeNode<T>>>, value: T) -> bool {
        match node {
            Some(n) => {
                match value.cmp(&n.value) {
                    Ordering::Less => self.search_recursive(&n.left, value),
                    Ordering::Greater => self.search_recursive(&n.right, value),
                    Ordering::Equal => true, // Found the value
                }
            }
            None => false, // Reached a leaf without finding the value
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_insert_and_search() {
        let mut bst = BinarySearchTree::new();

        assert_eq!(bst.search(1), false);

        bst.insert(5);
        bst.insert(3);
        bst.insert(7);
        bst.insert(2);
        bst.insert(4);

        assert_eq!(bst.search(5), true);
        assert_eq!(bst.search(3), true);
        assert_eq!(bst.search(7), true);
        assert_eq!(bst.search(2), true);
        assert_eq!(bst.search(4), true);

        assert_eq!(bst.search(1), false);
        assert_eq!(bst.search(6), false);
    }

    #[test]
    fn test_insert_duplicate() {
        let mut bst = BinarySearchTree::new();

        bst.insert(1);
        bst.insert(1);

        assert_eq!(bst.search(1), true);

        match bst.root {
            Some(ref node) => {
                assert!(node.left.is_none());
                assert!(node.right.is_none());
            },
            None => panic!("Root should not be None after insertion"),
        }
    }
}
```
1. **插入方法** (`insert`):
   - 在 `TreeNode` 中实现了 `insert` 方法，该方法通过比较当前节点的值和要插入值的大小，将值插入到左子树或右子树。对于重复值，直接忽略。

2. **插入逻辑**:
   - 在 `BinarySearchTree` 的 `insert` 方法中，如果根节点存在，则调用相应的 `TreeNode` 的 `insert` 方法；如果根节点不存在，则创建一个新的根节点。

3. **搜索方法** (`search`):
   - 在 `BinarySearchTree` 中实现了 `search` 方法，它使用递归的方式查找树中的值。`search_recursive` 是一个辅助函数，根据值的比较结果在左子树或右子树中递归搜索。
-**algorithm5.rs**：
为了完成图的广度优先搜索（BFS）实现，我们需要在 `Graph` 结构体中实现 `bfs_with_return` 方法。下面是修改后的代码，包含了 BFS 的实现：
```rust
use std::collections::VecDeque;

// Define a graph
struct Graph {
    adj: Vec<Vec<usize>>,
}

impl Graph {
    // Create a new graph with n vertices
    fn new(n: usize) -> Self {
        Graph {
            adj: vec![vec![]; n],
        }
    }

    // Add an edge to the graph
    fn add_edge(&mut self, src: usize, dest: usize) {
        self.adj[src].push(dest);
        self.adj[dest].push(src);
    }

    // Perform a breadth-first search on the graph, return the order of visited nodes
    fn bfs_with_return(&self, start: usize) -> Vec<usize> {
        let mut visit_order = Vec::new(); // To keep track of the order of visited nodes
        let mut visited = vec![false; self.adj.len()]; // Track visited nodes
        let mut queue = VecDeque::new(); // Queue for BFS

        // Start BFS from the starting node
        queue.push_back(start);
        visited[start] = true;

        while let Some(node) = queue.pop_front() {
            visit_order.push(node); // Add the current node to the visit order

            // Iterate over the neighbors of the current node
            for &neighbor in &self.adj[node] {
                if !visited[neighbor] { // If the neighbor hasn't been visited
                    visited[neighbor] = true; // Mark it as visited
                    queue.push_back(neighbor); // Add it to the queue
                }
            }
        }

        visit_order // Return the order of visited nodes
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_bfs_all_nodes_visited() {
        let mut graph = Graph::new(5);
        graph.add_edge(0, 1);
        graph.add_edge(0, 4);
        graph.add_edge(1, 2);
        graph.add_edge(1, 3);
        graph.add_edge(1, 4);
        graph.add_edge(2, 3);
        graph.add_edge(3, 4);

        let visited_order = graph.bfs_with_return(0);
        assert_eq!(visited_order, vec![0, 1, 4, 2, 3]);
    }

    #[test]
    fn test_bfs_different_start() {
        let mut graph = Graph::new(3);
        graph.add_edge(0, 1);
        graph.add_edge(1, 2);

        let visited_order = graph.bfs_with_return(2);
        assert_eq!(visited_order, vec![2, 1, 0]);
    }

    #[test]
    fn test_bfs_with_cycle() {
        let mut graph = Graph::new(3);
        graph.add_edge(0, 1);
        graph.add_edge(1, 2);
        graph.add_edge(2, 0);

        let visited_order = graph.bfs_with_return(0);
        assert_eq!(visited_order, vec![0, 1, 2]);
    }

    #[test]
    fn test_bfs_single_node() {
        let mut graph = Graph::new(1);

        let visited_order = graph.bfs_with_return(0);
        assert_eq!(visited_order, vec![0]);
    }
}
```
**BFS 方法实现** (`bfs_with_return`):
   - **初始化**: 创建一个 `visit_order` 向量来存储访问顺序，`visited` 向量来标记节点是否已访问，以及一个 `queue` 来处理 BFS 的节点。
   - **开始 BFS**: 将起始节点入队，并标记为已访问。
   - **遍历循环**: 使用 `while let` 结构从队列中弹出节点，记录其访问顺序，并遍历其邻居节点。对于未访问的邻居，标记为已访问并入队。
-**algorithm6.rs**：
要完成图的深度优先搜索（DFS）实现，我们需要在 `Graph` 结构体中实现 `dfs_util` 方法。以下是修改后的代码，包含了 DFS 的实现：
```rust
use std::collections::HashSet;

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

    fn dfs_util(&self, v: usize, visited: &mut HashSet<usize>, visit_order: &mut Vec<usize>) {
        // Mark the current node as visited
        visited.insert(v);
        visit_order.push(v); // Add the current node to the visit order

        // Iterate through all adjacent nodes
        for &neighbor in &self.adj[v] {
            if !visited.contains(&neighbor) { // If the neighbor hasn't been visited
                self.dfs_util(neighbor, visited, visit_order); // Recur for the neighbor
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
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_dfs_simple() {
        let mut graph = Graph::new(3);
        graph.add_edge(0, 1);
        graph.add_edge(1, 2);

        let visit_order = graph.dfs(0);
        assert_eq!(visit_order, vec![0, 1, 2]);
    }

    #[test]
    fn test_dfs_with_cycle() {
        let mut graph = Graph::new(4);
        graph.add_edge(0, 1);
        graph.add_edge(0, 2);
        graph.add_edge(1, 2);
        graph.add_edge(2, 3);
        graph.add_edge(3, 3); 

        let visit_order = graph.dfs(0);
        assert_eq!(visit_order, vec![0, 1, 2, 3]);
    }

    #[test]
    fn test_dfs_disconnected_graph() {
        let mut graph = Graph::new(5);
        graph.add_edge(0, 1);
        graph.add_edge(0, 2);
        graph.add_edge(3, 4); 

        let visit_order = graph.dfs(0);
        assert_eq!(visit_order, vec![0, 1, 2]); 
        let visit_order_disconnected = graph.dfs(3);
        assert_eq!(visit_order_disconnected, vec![3, 4]); 
    }
}
```
 **DFS 方法实现** (`dfs_util`):
   - **递归遍历**: 在 `dfs_util` 中，首先将当前节点 `v` 标记为已访问，并将其加入访问顺序 `visit_order`。
   - **邻接节点处理**: 遍历当前节点的所有邻接节点。如果邻接节点尚未访问，则递归调用 `dfs_util`。
-**algorithm7.rs**：
要实现 `bracket_match` 函数以检查字符串中的括号是否匹配，可以使用栈数据结构。这个函数会遍历输入字符串，将打开的括号推入栈中，当遇到关闭的括号时，它会检查栈顶的开括号是否与之匹配。
```rust
#[derive(Debug)]
struct Stack<T> {
    size: usize,
    data: Vec<T>,
}

impl<T> Stack<T> {
    fn new() -> Self {
        Self {
            size: 0,
            data: Vec::new(),
        }
    }

    fn is_empty(&self) -> bool {
        0 == self.size
    }

    fn len(&self) -> usize {
        self.size
    }

    fn clear(&mut self) {
        self.size = 0;
        self.data.clear();
    }

    fn push(&mut self, val: T) {
        self.data.push(val);
        self.size += 1;
    }

    fn pop(&mut self) -> Option<T> {
        if self.is_empty() {
            None
        } else {
            self.size -= 1;
            self.data.pop()
        }
    }

    fn peek(&self) -> Option<&T> {
        if self.is_empty() {
            None
        } else {
            self.data.get(self.size - 1)
        }
    }

    fn peek_mut(&mut self) -> Option<&mut T> {
        if self.is_empty() {
            None
        } else {
            self.data.get_mut(self.size - 1)
        }
    }

    fn into_iter(self) -> IntoIter<T> {
        IntoIter(self)
    }

    fn iter(&self) -> Iter<T> {
        let mut iterator = Iter {
            stack: Vec::new(),
        };
        for item in self.data.iter() {
            iterator.stack.push(item);
        }
        iterator
    }

    fn iter_mut(&mut self) -> IterMut<T> {
        let mut iterator = IterMut {
            stack: Vec::new(),
        };
        for item in self.data.iter_mut() {
            iterator.stack.push(item);
        }
        iterator
    }
}

struct IntoIter<T>(Stack<T>);

impl<T: Clone> Iterator for IntoIter<T> {
    type Item = T;

    fn next(&mut self) -> Option<Self::Item> {
        if !self.0.is_empty() {
            self.0.size -= 1;
            self.0.data.pop()
        } else {
            None
        }
    }
}

struct Iter<'a, T: 'a> {
    stack: Vec<&'a T>,
}

impl<'a, T> Iterator for Iter<'a, T> {
    type Item = &'a T;

    fn next(&mut self) -> Option<Self::Item> {
        self.stack.pop()
    }
}

struct IterMut<'a, T: 'a> {
    stack: Vec<&'a mut T>,
}

impl<'a, T> Iterator for IterMut<'a, T> {
    type Item = &'a mut T;

    fn next(&mut self) -> Option<Self::Item> {
        self.stack.pop()
    }
}

fn bracket_match(bracket: &str) -> bool {
    let mut stack = Stack::new();

    for ch in bracket.chars() {
        match ch {
            '(' | '{' | '[' => stack.push(ch),
            ')' => {
                if stack.pop() != Some('(') {
                    return false;
                }
            }
            '}' => {
                if stack.pop() != Some('{') {
                    return false;
                }
            }
            ']' => {
                if stack.pop() != Some('[') {
                    return false;
                }
            }
            _ => {}
        }
    }

    // Check if the stack is empty (all brackets matched)
    stack.is_empty()
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn bracket_matching_1() {
        let s = "(2+3){func}[abc]";
        assert_eq!(bracket_match(s), true);
    }
    #[test]
    fn bracket_matching_2() {
        let s = "(2+3)*(3-1";
        assert_eq!(bracket_match(s), false);
    }
    #[test]
    fn bracket_matching_3() {
        let s = "{{([])}}";
        assert_eq!(bracket_match(s), true);
    }
    #[test]
    fn bracket_matching_4() {
        let s = "{{(}[)]}";
        assert_eq!(bracket_match(s), false);
    }
    #[test]
    fn bracket_matching_5() {
        let s = "[[[]]]]]]]]]";
        assert_eq!(bracket_match(s), false);
    }
    #[test]
    fn bracket_matching_6() {
        let s = "";
        assert_eq!(bracket_match(s), true);
    }
}
```
1. **Stack 结构**：
   - 实现了基本的栈操作，包括 `push`, `pop`, `peek`, `is_empty`, `len`, 和 `clear` 等。

2. **bracket_match 函数**：
   - 遍历输入字符串的每个字符。
   - 如果字符是开括号（`(`、`{`、`[`），则将其推入栈。
   - 如果字符是闭括号（`)`、`}`、`]`），则从栈中弹出一个开括号并进行匹配检查。
   - 如果匹配失败，立即返回 `false`。
   - 最后，检查栈是否为空以确定所有的括号是否都匹配。
-**algorithm8.rs**：
要实现一个基于两个队列的栈 `myStack`，我们可以使用两个队列 (`q1` 和 `q2`) 来模拟栈的行为。栈的主要操作是 `push`（压入元素）和 `pop`（弹出元素），我们需要确保 `pop` 操作总是返回最后插入的元素。
```rust
#[derive(Debug)]
pub struct Queue<T> {
    elements: Vec<T>,
}

impl<T> Queue<T> {
    pub fn new() -> Queue<T> {
        Queue {
            elements: Vec::new(),
        }
    }

    pub fn enqueue(&mut self, value: T) {
        self.elements.push(value)
    }

    pub fn dequeue(&mut self) -> Result<T, &str> {
        if !self.elements.is_empty() {
            Ok(self.elements.remove(0))
        } else {
            Err("Queue is empty")
        }
    }

    pub fn peek(&self) -> Result<&T, &str> {
        match self.elements.first() {
            Some(value) => Ok(value),
            None => Err("Queue is empty"),
        }
    }

    pub fn size(&self) -> usize {
        self.elements.len()
    }

    pub fn is_empty(&self) -> bool {
        self.elements.is_empty()
    }
}

impl<T> Default for Queue<T> {
    fn default() -> Queue<T> {
        Queue {
            elements: Vec::new(),
        }
    }
}

pub struct myStack<T> {
    q1: Queue<T>,
    q2: Queue<T>,
}

impl<T> myStack<T> {
    pub fn new() -> Self {
        Self {
            q1: Queue::new(),
            q2: Queue::new(),
        }
    }

    pub fn push(&mut self, elem: T) {
        // 将元素压入 q2
        self.q2.enqueue(elem);
        // 将 q1 中的所有元素转移到 q2 中
        while let Ok(value) = self.q1.dequeue() {
            self.q2.enqueue(value);
        }
        // 交换 q1 和 q2
        std::mem::swap(&mut self.q1, &mut self.q2);
    }

    pub fn pop(&mut self) -> Result<T, &str> {
        // 从 q1 中弹出元素
        self.q1.dequeue()
    }

    pub fn is_empty(&self) -> bool {
        self.q1.is_empty()
    }
}

#[cfg(test)]
mod tests {
    use super::*;
	
	#[test]
	fn test_queue() {
		let mut s = myStack::<i32>::new();
		assert_eq!(s.pop(), Err("Stack is empty"));
        s.push(1);
        s.push(2);
        s.push(3);
        assert_eq!(s.pop(), Ok(3));
        assert_eq!(s.pop(), Ok(2));
        s.push(4);
        s.push(5);
        assert_eq!(s.is_empty(), false);
        assert_eq!(s.pop(), Ok(5));
        assert_eq!(s.pop(), Ok(4));
        assert_eq!(s.pop(), Ok(1));
        assert_eq!(s.pop(), Err("Stack is empty"));
        assert_eq!(s.is_empty(), true);
	}
}
```
1. **Queue 结构**：
   - `Queue` 提供了基本的队列操作，包括 `enqueue`、`dequeue`、`peek`、`size` 和 `is_empty`。
   - 使用 `Vec<T>` 来存储元素。

2. **myStack 结构**：
   - `myStack` 由两个队列 `q1` 和 `q2` 组成。
   - `push` 方法：
     - 将新元素添加到 `q2` 中。
     - 将 `q1` 中的所有元素转移到 `q2` 中，这样最后插入的元素总是位于 `q1` 的前面。
     - 使用 `std::mem::swap` 来交换 `q1` 和 `q2`，使得 `q1` 总是保持栈的顺序。
   - `pop` 方法：
     - 从 `q1` 中移除并返回顶部元素。
   - `is_empty` 方法：
     - 检查 `q1` 是否为空。
-**algorithm9.rs**：
在你的实现中，`Heap` 是一个抽象的数据结构，支持最小堆和最大堆的功能。要完成这个 `Heap` 的实现，需要为 `add`、`smallest_child_idx` 和 `next` 等方法提供具体的实现。
```rust
use std::cmp::Ord;
use std::default::Default;

pub struct Heap<T>
where
    T: Default,
{
    count: usize,
    items: Vec<T>,
    comparator: fn(&T, &T) -> bool,
}

impl<T> Heap<T>
where
    T: Default,
{
    pub fn new(comparator: fn(&T, &T) -> bool) -> Self {
        Self {
            count: 0,
            items: vec![T::default()],
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
        self.count += 1;
        if self.count >= self.items.len() {
            self.items.push(value);
        } else {
            self.items[self.count] = value;
        }
        self.bubble_up(self.count);
    }

    fn bubble_up(&mut self, idx: usize) {
        let mut index = idx;
        while index > 1 {
            let parent_index = self.parent_idx(index);
            if (self.comparator)(&self.items[index], &self.items[parent_index]) {
                self.items.swap(index, parent_index);
                index = parent_index;
            } else {
                break;
            }
        }
    }

    fn parent_idx(&self, idx: usize) -> usize {
        idx / 2
    }

    fn children_present(&self, idx: usize) -> bool {
        self.left_child_idx(idx) <= self.count
    }

    fn left_child_idx(&self, idx: usize) -> usize {
        idx * 2
    }

    fn right_child_idx(&self, idx: usize) -> usize {
        self.left_child_idx(idx) + 1
    }

    fn smallest_child_idx(&self, idx: usize) -> usize {
        if !self.children_present(idx) {
            return 0; // No children
        }
        
        let left_child = self.left_child_idx(idx);
        let right_child = self.right_child_idx(idx);

        if right_child <= self.count && (self.comparator)(&self.items[right_child], &self.items[left_child]) {
            right_child
        } else {
            left_child
        }
    }

    pub fn remove(&mut self) -> Option<T> {
        if self.is_empty() {
            return None;
        }
        let top = self.items[1].clone();
        self.items[1] = self.items[self.count]; // Move last item to the top
        self.count -= 1;
        self.bubble_down(1);
        Some(top)
    }

    fn bubble_down(&mut self, idx: usize) {
        let mut index = idx;
        while self.children_present(index) {
            let smallest_child = self.smallest_child_idx(index);
            if (self.comparator)(&self.items[smallest_child], &self.items[index]) {
                self.items.swap(index, smallest_child);
                index = smallest_child;
            } else {
                break;
            }
        }
    }
}

impl<T> Heap<T>
where
    T: Default + Ord + Clone,
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

impl<T> Iterator for Heap<T>
where
    T: Default + Clone,
{
    type Item = T;

    fn next(&mut self) -> Option<T> {
        self.remove()
    }
}

pub struct MinHeap;

impl MinHeap {
    #[allow(clippy::new_ret_no_self)]
    pub fn new<T>() -> Heap<T>
    where
        T: Default + Ord + Clone,
    {
        Heap::new(|a, b| a < b)
    }
}

pub struct MaxHeap;

impl MaxHeap {
    #[allow(clippy::new_ret_no_self)]
    pub fn new<T>() -> Heap<T>
    where
        T: Default + Ord + Clone,
    {
        Heap::new(|a, b| a > b)
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_empty_heap() {
        let mut heap = MaxHeap::new::<i32>();
        assert_eq!(heap.next(), None);
    }

    #[test]
    fn test_min_heap() {
        let mut heap = MinHeap::new();
        heap.add(4);
        heap.add(2);
        heap.add(9);
        heap.add(11);
        assert_eq!(heap.len(), 4);
        assert_eq!(heap.next(), Some(2));
        assert_eq!(heap.next(), Some(4));
        assert_eq!(heap.next(), Some(9));
        heap.add(1);
        assert_eq!(heap.next(), Some(1));
    }

    #[test]
    fn test_max_heap() {
        let mut heap = MaxHeap::new();
        heap.add(4);
        heap.add(2);
        heap.add(9);
        heap.add(11);
        assert_eq!(heap.len(), 4);
        assert_eq!(heap.next(), Some(11));
        assert_eq!(heap.next(), Some(9));
        assert_eq!(heap.next(), Some(4));
        heap.add(1);
        assert_eq!(heap.next(), Some(2));
    }
}
```
1. **添加元素 (`add`)**：
   - 将新元素添加到堆的末尾，并调用 `bubble_up` 方法调整堆以维护其性质（最小堆或最大堆）。

2. **上浮操作 (`bubble_up`)**：
   - 在 `bubble_up` 方法中，比较新添加的元素与其父元素，若需要则交换它们的位置，直到堆的性质得到满足。

3. **移除元素 (`remove`)**：
   - 从堆中移除根元素（最小或最大），将最后一个元素放到根位置，然后调用 `bubble_down` 方法调整堆。

4. **下沉操作 (`bubble_down`)**：
   - 在 `bubble_down` 方法中，比较当前节点与其子节点，若需要则交换它们的位置，直到堆的性质得到满足。

5. **最小子节点索引 (`smallest_child_idx`)**：
   - 此方法计算并返回当前节点的最小子节点的索引，用于维护堆的性质。

6. **实现 `Iterator`**：
   - 实现 `Iterator` 特性以便通过 `next()` 方法顺序获取堆中的元素。
-**algorithm10.rs**：
```rust
use std::collections::{HashMap, HashSet};
use std::fmt;

/// 自定义错误类型：图中没有该节点
#[derive(Debug, Clone)]
pub struct NodeNotInGraph;

impl fmt::Display for NodeNotInGraph {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "accessing a node that is not in the graph")
    }
}

/// 无向图的结构体
pub struct UndirectedGraph {
    adjacency_table: HashMap<String, Vec<(String, i32)>>,
}

/// 定义图的操作的特征
pub trait Graph {
    fn new() -> Self;
    fn adjacency_table_mutable(&mut self) -> &mut HashMap<String, Vec<(String, i32)>>;
    fn adjacency_table(&self) -> &HashMap<String, Vec<(String, i32)>>;

    /// 添加节点到图中，返回 true 如果节点成功添加
    fn add_node(&mut self, node: &str) -> bool {
        if self.contains(node) {
            return false; // 节点已存在
        }
        self.adjacency_table_mutable().insert(node.to_string(), Vec::new());
        true
    }

    /// 添加两个节点之间的边以及权重
    fn add_edge(&mut self, edge: (&str, &str, i32)) {
        let (from, to, weight) = edge;
        self.add_node(from); // 确保节点存在
        self.add_node(to);

        // 在两个方向上添加边（无向图）
        self.adjacency_table_mutable()
            .entry(from.to_string())
            .or_insert_with(Vec::new)
            .push((to.to_string(), weight));
        self.adjacency_table_mutable()
            .entry(to.to_string())
            .or_insert_with(Vec::new)
            .push((from.to_string(), weight));
    }

    /// 检查节点是否在图中
    fn contains(&self, node: &str) -> bool {
        self.adjacency_table().get(node).is_some()
    }

    /// 返回图中的所有节点
    fn nodes(&self) -> HashSet<&String> {
        self.adjacency_table().keys().collect()
    }

    /// 返回图中的所有边
    fn edges(&self) -> Vec<(&String, &String, i32)> {
        let mut edges = Vec::new();
        for (from_node, from_node_neighbours) in self.adjacency_table() {
            for (to_node, weight) in from_node_neighbours {
                if from_node < to_node { // 避免无向图中的重复边
                    edges.push((from_node, to_node, *weight));
                }
            }
        }
        edges
    }
}

impl Graph for UndirectedGraph {
    fn new() -> UndirectedGraph {
        UndirectedGraph {
            adjacency_table: HashMap::new(),
        }
    }

    fn adjacency_table_mutable(&mut self) -> &mut HashMap<String, Vec<(String, i32)>> {
        &mut self.adjacency_table
    }

    fn adjacency_table(&self) -> &HashMap<String, Vec<(String, i32)>> {
        &self.adjacency_table
    }
}

#[cfg(test)]
mod test_undirected_graph {
    use super::{Graph, UndirectedGraph};

    #[test]
    fn test_add_edge() {
        let mut graph = UndirectedGraph::new();
        graph.add_edge(("a", "b", 5));
        graph.add_edge(("b", "c", 10));
        graph.add_edge(("c", "a", 7));

        let expected_edges = [
            ("a", "b", 5),
            ("b", "a", 5),
            ("b", "c", 10),
            ("c", "b", 10),
            ("c", "a", 7),
            ("a", "c", 7),
        ];

        let edges = graph.edges();
        for edge in expected_edges.iter() {
            assert!(edges.contains(&(&edge.0.to_string(), &edge.1.to_string(), edge.2)));
        }
    }
}
```
1. **数据结构**：
   - 使用 `HashMap<String, Vec<(String, i32)>>` 作为邻接表来表示图，其中每个节点映射到一个包含相邻节点及边权重的向量。

2. **核心特性**：
   - **添加节点** (`add_node`): 确保节点存在于图中，如果不存在则添加该节点。
   - **添加边** (`add_edge`): 在无向图中添加边时，确保两个节点都存在，并在邻接表中以双向方式添加边。
   - **检查节点存在性** (`contains`): 确定一个节点是否在图中。
   - **获取所有节点** (`nodes`): 返回图中所有节点的集合。
   - **获取所有边** (`edges`): 返回图中所有边的列表，避免重复边的出现。

3. **错误处理**：
   - 定义了 `NodeNotInGraph` 错误类型，以便在访问图中不存在的节点时进行处理（虽然在当前代码中未使用）。

4. **测试用例**：
   - 使用 Rust 测试模块对 `add_edge` 方法进行单元测试，验证边的添加和存在性。
- **图特征** (`Graph` trait): 定义图的基本操作。
- **无向图实现** (`UndirectedGraph`): 实现 `Graph` 特征的具体结构体。
- **错误类型** (`NodeNotInGraph`): 自定义错误类型，表示访问图中不存在的节点。