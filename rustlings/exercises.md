
# Exercises

## enums

枚举我一直把它当成特殊的类，枚举项为类的静态常量，还带有name、index的属性，这些是所有枚举的常规操作。Rust不同的是：添加了类似于带命名的类构造函数方式，使得可以采用枚举的方式，定义五花八门的命名类枚举项，还有结合match模式非常实用。所以我将其理解为：结构优先趋势下的类功能转移到枚举的实现。

```rust
enum Message {
  Move{x: i32, y: i32},
  Echo(String),
  ChangeColor(i32, i32, i32),
  Quit,
}

let messages = [
    Message::Move { x: 10, y: 30 },
    Message::Echo(String::from("hello world")),
    Message::ChangeColor(200, 255, 255),
    Message::Quit,
];
```

## options

将其理解为空安全语言中的Nullable\<T\>不过分吧.
只是你叫Some和None我是没有想到的🤔

ref

还好之前在C#见过这玩意，可以稍微在Rust这边映射下，不出意外这个语法糖的功能差不多，不过貌似只能创建结构体、元组字段的引用。
暂不深挖，不求甚解，先记下来。

```rust

struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let y: Option<Point> = Some(Point { x: 100, y: 200 });

    match y {
        Some(ref p) => println!("Co-ordinates are {},{} ", p.x, p.y),
        _ => println!("no match"),
    }
    y; // Fix without deleting this line.
}

```

## results

Result<OK, ERR> 是Option关注于出现错误处理的升级版。

如果Result是Err，使用上经常搭配一下链式操作

### expect 

类似于断言，出现Err就panics出自定义消息

### unwrap

直接拆箱取值，仅在保证一定不为Err时才可使用。否则panics满天飞

### unwrap_or 

出现Err就返回提供的自定义值

### unwrap_or_default 

出现Err就返回提供的自定义值类型 T 的默认值 (必须实现 Default trait)

### unwrap_or_else 

出现Err就返回自定义闭包函数执行后的值

### ？

语法糖，出现Err就停止执行并将其Err抛出

### map_err

将Err传入闭包函数，映射为其他对象

```rust

let a: A = getA(a)?;

// 等价于以下

let a: A = match getA(a) {
  Ok(a) => A,
  Err(e) => return Err("Ab Ab Ab...."),
};

```

## vecs

从最开始接触编程的时候就遇到了，Array和Vector，一度认为这两个结构长度可变用Vector不可变用Array。后来写C#和Java的时候没有Vector了，而是以List代替了。再后来就是写Js、Dart，对于之前的语言数据结构少了很多，这时候只有List了，世界顿时清净了很多，当然也会提供List的不可变长度的构造函数(Dart)。
还有对于这种数据结构接触最多的便是迭代器和链式函数，可以理解为后现代语言都有的特性，使用起来方便简洁优雅。

```rust 
fn vec_loop(mut v: Vec<i32>) -> Vec<i32> {
    for mut i in v.iter_mut() {
        *i = *i * 2;
    }
    v
}

fn vec_map(v: &Vec<i32>) -> Vec<i32> {
    v.iter().map(|num| num * 2).collect()
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_vec_loop() {
        let v: Vec<i32> = (1..).filter(|x| x % 2 == 0).take(5).collect();
        let ans = vec_loop(v.clone());

        assert_eq!(ans, v.iter().map(|x| x * 2).collect::<Vec<i32>>());
    }

    #[test]
    fn test_vec_map() {
        let v: Vec<i32> = (1..).filter(|x| x % 2 == 0).take(5).collect();
        let ans = vec_map(&v);

        assert_eq!(ans, v.iter().map(|x| x * 2).collect::<Vec<i32>>());
    }
}

```

## structs

毕竟不是面向对象了，不需要Object了，讲道理单纯是数据对象的话用个Map都可以装数据（js不就喜欢这么干），没必要搞个对象那么抽象复杂，此时感觉结构体错错有余，并且后续可以持续添加实现方法、关联函数等完全可以进化成类去使用，当然面向对象的类也不是仅此而已，这里先不提那么多。

这里的结构体声明挺有意思的，就跟函数的不带命名参数命和带命名参数差不多，不过好像不能一起使用也就是： fun(a, b, c, { d, e, f}), 但不是一种东西也不能一起比较的。

```rust
struct ColorClassicStruct {
    red: u8,
    green: u8,
    blue: u8,
}

struct ColorTupleStruct(u8, u8, u8);

struct UnitLikeStruct();

```

小语法糖 （js中貌似也可以这样）
命名参数赋值时，可以直接将定义相同变量名的变量塞进去当作赋值处理。
同理例子中your_order中塞了order_template，对于未赋值的字段year、made_by_phone、made_by_email、item_number，直接从order_template的同名字段中去取。

切记是两个点，三个点的是rest参数或者合并运算符，rust目前还没有抄过来？大概吧。

```rust
fn create_order_template() -> Order {
    Order {
        name: String::from("Bob"),
        year: 2019,
        made_by_phone: false,
        made_by_mobile: false,
        made_by_email: true,
        item_number: 123,
        count: 0,
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn your_order() {
        let order_template = create_order_template();
        let made_by_mobile = false;
        let your_order = Order{
            name: "Hacker in Rust".to_string(),
            count: 1,
            made_by_mobile,
            ..order_template
        };
        assert_eq!(your_order.name, "Hacker in Rust");
        assert_eq!(your_order.year, order_template.year);
        assert_eq!(your_order.made_by_phone, order_template.made_by_phone);
        assert_eq!(your_order.made_by_mobile, order_template.made_by_mobile);
        assert_eq!(your_order.made_by_email, order_template.made_by_email);
        assert_eq!(your_order.item_number, order_template.item_number);
        assert_eq!(your_order.count, 1);
    }
}
```

## strings

Rust里 String可不是字符串，用双引号引起来的也不是。讲道理之前接触过宽窄字符就知道，字符串没那么简单，你写Dart、Js这种不太需要处理特殊的字符串也就只有String就够了。但对于Rust不一样，还是要细分一下的，但也要明白实际使用还是普通的字符串最多，这里先去了解最基础的字符串。

大概有这么几种：
String、str、&str、&String、Box<str>、Box<&str>
需要弄清楚String、还有他们之间的隐式转换，刚开始还是要带上显示转换比较好，先熟悉他们的转化规则，再去使用隐式转换比较好，

下面的代码先去稍微感受String和&str：
```rust 

fn string_slice(arg: &str) {
    println!("{}", arg);
}
fn string(arg: String) {
    println!("{}", arg);
}

fn main() {
    string_slice("blue");
    string("red".to_string());
    string(String::from("hi"));
    string("rust is fun!".to_owned());
    string_slice("nice weather".into());
    string(format!("Interpolation {}", "Station"));
    string_slice(&String::from("abc")[0..1]);
    string_slice("  hello there ".trim());
    string("Happy Monday!".to_string().replace("Mon", "Tues"));
    string("mY sHiFt KeY iS sTiCkY".to_lowercase());
}

```

拼接字符串：

```rust 

let mut string = String::from(string);
for _ in 0..5 {
    string += "bar";
}

```

## hasmaps

key不能重复，想要重复请出门左转——> Vec<(K, V)>

插值：insert
如存在便覆盖

取值：get
返回Option 动动脚趾头都知道是这个类型

不存在便插入：entry(key).or_insert(value)

取所有key：map.keys()

取所有value：map.values()

长度：map.len()

## traits

介个不就是minix吗 哈哈哈，需要啥就混入啥，然后就成为了啥，并且可以混入多个，很是灵活。

用imp T 表示类型 / 用泛型进行约束类型

```rust 

pub trait Licensed {
    fn licensing_info(&self) -> String {
        "some information".to_string()
    }
}

struct SomeSoftware {}

struct OtherSoftware {}

impl Licensed for SomeSoftware {}
impl Licensed for OtherSoftware {}

fn compare_license_types2(software: impl Licensed, software_two: impl Licensed) -> bool {
    software.licensing_info() == software_two.licensing_info()
}

fn compare_license_types<S, O>(software: S, software_two: O) -> bool where S : Licensed, O: Licensed {
  software.licensing_info() == software_two.licensing_info()
}

```

实现多个trait 用 + 号连接

```rust 

pub trait SomeTrait {
    fn some_function(&self) -> bool {
        true
    }
}

pub trait OtherTrait {
    fn other_function(&self) -> bool {
        true
    }
}

struct SomeStruct {
    name: String,
}

impl SomeTrait for SomeStruct {}
impl OtherTrait for SomeStruct {}

fn some_func2(item: impl SomeTrait + OtherTrait) -> bool {
    item.some_function() && item.other_function()
}

fn some_func<T>(item: T) -> bool where T : SomeTrait + OtherTrait {
  item.some_function() && item.other_function()
}

```

## clippy

### clippy1

```rust
use std::f32::consts::PI;

let pi = 3.14f32; // 可引入预定义常量⬆️，无需自定义

```

### clippy2

Option类型分为Some和None两种，使用时必须考虑到这两种类型，保险的做法是用match或者精简的if let，其他使用方式可能遇到值为None的风险，所以编译将不被通过。

[for_loops_over_fallibles](https://rust-lang.github.io/rust-clippy/master/index.html#for_loops_over_fallibles)

```rust
let mut res = 42;
let option = Some(12);

// 编译器角度option可能为None
for x in option {
  res += x;
}

// 应该改为如下:
if let Some(x) = option {
  res += x;
}

```

## iterators

拿到迭代器之后可以有next、map等一些链式操作，这里先去讨论怎么获取到迭代器。

### into_iter

返回的对应的值

```rust

fn list_of_results() -> Vec<Result<i32, DivisionError>> {
    let numbers = vec![27, 297, 38502, 81];
    numbers.into_iter().map(|n| divide(n, 27)).collect()
}

fn result_with_list() -> Result<Vec<i32>, DivisionError> {
    let numbers = vec![27, 297, 38502, 81];
    let division_results: Vec<i32> = numbers
        .into_iter()
        .map(|n| divide(n, 27).unwrap())
        .collect();
    Ok(division_results)
}

```

### iter

返回的是值的不可变引用

```rust

let my_fav_fruits = vec!["banana", "custard apple", "avocado", "peach", "raspberry"];

let mut my_iterable_fav_fruits = my_fav_fruits.iter();

assert_eq!(my_iterable_fav_fruits.next(), Some(&"banana"));
assert_eq!(my_iterable_fav_fruits.next(), Some(&"custard apple"));
assert_eq!(my_iterable_fav_fruits.next(), Some(&"avocado"));
assert_eq!(my_iterable_fav_fruits.next(), Some(&"peach"));
assert_eq!(my_iterable_fav_fruits.next(), Some(&"raspberry"));
assert_eq!(my_iterable_fav_fruits.next(), None); 

```