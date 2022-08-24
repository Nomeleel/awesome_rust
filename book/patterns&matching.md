# 模式匹配

Rust模式匹配可真是太秀了，给我的感觉就是所见即所得，只要写出来表达式感觉可以它就可以满足。

## 一对一简单模式

只要是 表达式 和  变量 两边结构能够对的上就满足匹配

```rust 

let (x, y, z) = (1, 2, 3);

//----------------------------------------------------------

let point = (3, 5);
print_coordinates(&point);
fn print_coordinates(&(x, y): &(i32, i32)) {
    println!("Current location: ({}, {})", x, y);
}

//----------------------------------------------------------

let ((feet, inches), Point { x, y }) = ((3, 10), Point { x: 3, y: -10 });

//----------------------------------------------------------

let favorite_color: Option<&str> = None;
if let Some(color) = favorite_color {
  
}

//----------------------------------------------------------

let mut stack = Vec::new();
stack.push(1);
stack.push(2);
stack.push(3);
while let Some(top) = stack.pop() {
    println!("{}", top);
}

//----------------------------------------------------------

let v = vec!['a', 'b', 'c'];
for (index, value) in v.iter().enumerate() {
    println!("{} is at index {}", value, index);
}

//----------------------------------------------------------

struct Point {
    x: i32,
    y: i32,
}
let p = Point { x: 0, y: 7 };
let Point { x, y } = p;
assert_eq!(0, x);
assert_eq!(7, y);

//----------------------------------------------------------

enum Color {
    Rgb(i32, i32, i32),
    Hsv(i32, i32, i32),
}
enum Message {
    Quit,
    ChangeColor(Color),
}
let msg = Message::ChangeColor(Color::Hsv(0, 160, 255));
match msg {
    Message::ChangeColor(Color::Rgb(r, g, b)) => println!(
        "Change the color to red {}, green {}, and blue {}",
        r, g, b
    ),
    Message::ChangeColor(Color::Hsv(h, s, v)) => println!(
        "Change the color to hue {}, saturation {}, and value {}",
        h, s, v
    ),
    _ => (),
}

```

## 复杂模式

### ｜ 或

```rust 

let x = 1;

match x {
    1 | 2 => println!("one or two"),
    _ => println!("anything"),
}

```

### ..= 范围

```rust 

let x = 5;
match x {
    1..=5 => println!("one through five"),
    _ => println!("something else"),
}

//----------------------------------------------------------

let x = 'c';
match x {
    'a'..='j' => println!("early ASCII letter"),
    'k'..='z' => println!("late ASCII letter"),
    _ => println!("something else"),
}
```

### 7 精分

```rust 

let x = Some(5);
match x {
    Some(7) => println!("Got 7"),
    Some(y) => println!("Matched, y = {y}"),
    _ => println!("Default case, x = {:?}", x),
}

//----------------------------------------------------------

let p = Point { x: 0, y: 7 };
match p {
    Point { x, y: 0 } => println!("On the x axis at {}", x),
    Point { x: 0, y } => println!("On the y axis at {}", y),
    Point { x, y } => println!("On neither axis: ({}, {})", x, y),
}

```

### _ 剩余全部 类似于default

```rust 

let x = 1;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    _ => println!("anything"),
}

```

### _ 省略

```rust 

fn foo(_: i32, y: i32) {
    println!("This code only uses the y parameter: {}", y);
}

//----------------------------------------------------------

let mut setting_value = Some(5);
let new_setting_value = Some(10);
match (setting_value, new_setting_value) {
    (Some(_), Some(_)) => {
        println!("Can't overwrite an existing customized value");
    }
    _ => {
        setting_value = new_setting_value;
    }
}

//----------------------------------------------------------

let numbers = (2, 4, 8, 16, 32);
match numbers {
    (first, _, third, _, fifth) => {
        println!("Some numbers: {first}, {third}, {fifth}")
    }
}

```

扩展：
通过以 _ 开头的名称来忽略未使用的变量

```rust 

let _x = 5;

```

使用 _ 和 _x 之间存在细微差别, 语法 _x 仍然将值绑定到变量，而 _ 则根本不绑定，也就不会持有值。
其实就是声明了一个不去使用的变量，除了消除未使用的lint，其他就跟正常变量一样。
```rust 

let s = Some(String::from("Hello!"));

// Error
if let Some(_s) = s {
    println!("found a string");
}
println!("{:?}", s);

// Yes
if let Some(_) = s {
    println!("found a string");
}
println!("{:?}", s);

```

### .. 省略多个

```rust 

struct Point {
    x: i32,
    y: i32,
    z: i32,
}
let origin = Point { x: 0, y: 0, z: 0 };
match origin {
    Point { x, .. } => println!("x is {}", x),
}

//----------------------------------------------------------

let numbers = (2, 4, 8, 16, 32);
match numbers {
    (first, .., last) => {
        println!("Some numbers: {first}, {last}");
    }
}

// 想想看也知道只能出现一个，多个的话鬼知道按那种方式去匹配呢
(.., second, ..) // Error

```

### if 细分

```rust 

let num = Some(4);
match num {
    Some(x) if x % 2 == 0 => println!("The number {} is even", x),
    Some(x) => println!("The number {} is odd", x),
    None => (),
}

//----------------------------------------------------------

let x = 4;
let y = false;
match x {
    4 | 5 | 6 if y => println!("yes"),
    _ => println!("no"),
}

```

### @ 绑定

这个有点怪，在模式匹配的同时，并给变量绑定值。

```rust 

enum Message {
    Hello { id: i32 },
}

let msg = Message::Hello { id: 5 };

match msg {
    Message::Hello {
        id: id_variable @ 3..=7,
    } => println!("Found an id in range: {}", id_variable),
    Message::Hello { id: 10..=12 } => {
        println!("Found an id in another range")
    }
    Message::Hello { id } => println!("Found some other id: {}", id),
}

```