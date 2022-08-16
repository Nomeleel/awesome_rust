
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