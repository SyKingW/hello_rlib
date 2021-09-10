# Rust 引用 .rlib 示例

# 前言

电脑环境  

```
Windows 10
rustup 1.24.3 (ce5817a94 2021-05-31)
rustc 1.55.0 (c8dfcfe04 2021-09-06)
msvc 2019
```

# 代码编写

我这里是一步步来的，从创建一个新项目开始，如果嫌啰嗦，可直接跳到最下面，下载demo源码看

先创建一个 hello_rlib 项目

```
cargo new hello_rlib
```

编写 src/main.rs

```rust
// 导入 test_rlib
// 不过当前会报错，这个没事，后面走完流程就没事了
extern crate test_rlib;

fn main() {
    println!("Hello, world!");
    // 调用 test 函数
    test_rlib::test();
}
```

Cargo.toml package 模块加上 build.rs, 并且增加 test_rlib 工作成员

```toml
[package]
name = "hello_rust"
version = "0.1.0"
edition = "2018"
build = "build.rs"

[dependencies]

[workspace]
members = [
    "test_rlib"
]
```

项目根目录下创建 build.rs 文件(编译过C这些的同学应该不陌生)，并编写以下代码

```rust
fn main() {
	// .rlib 路径
    println!("cargo:rustc-link-search=./target/debug/");
}
```

在项目下，创建 lib 库

```
cargo new test_rlib --lib
```

编写 test_rlib/src/lib.rs 代码

```rust
pub fn test() {
    println!("hello test");
}
```

编译 test_rlib  
这里是让 target/debug/ 目录下生成 libtest_rlib.rlib 文件  

```
cargo build --package test_rlib
```

最后 run 项目

```
cargo run
```

输出

```
Hello, world!
hello test
```

至此整个流程就结束了

# 坑

## VS Code  extern crate xxx 报错
显示 extern crate test_rlib; 错误，应该是插件 rust-analyzer(版本v0.2.735) 的问题，这个问题或许要等待 rust-analyzer 的更新了。  
当然，如果有发现其他解决方法，我也会第一时间更新该文章


## VS Code 无法进行库的代码联想

暂无发现解决方法

# 总结

其实就两个点

1. 项目使用 ```extern crate 库名;```导入库
2. build.rs ```println!("cargo:rustc-link-search=./target/debug/");```关联.rlib 库路径

# 演示项目
[github demo](https://github.com/SyKingW/hello_rlib)


