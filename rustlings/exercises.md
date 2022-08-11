
# Exercises

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