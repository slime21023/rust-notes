# 基本型別與語法：Rust 的入門基礎

Rust 是一門靜態型別語言，擁有清晰且強大的語法結構，旨在幫助開發者編寫安全高效的代碼。

對於初學者來說，掌握 Rust 的基本型別與語法是快速上手的關鍵。

---

## Rust 的基本語法結構

### 變數與不可變性

在 Rust 中，變數預設是不可變的（immutable），必須使用 `mut` 關鍵字才能使其可變：

```rust
fn main() {
    // 不可變變數
    let x = 5; 
    println!("x = {}", x);

    // 以下行會導致編譯錯誤
    // x = 10;
    

    // 可變變數
    let mut y = 10; 
    // 正確，可以修改
    y = 20; 
    println!("y = {}", y);
}
```

這種設計鼓勵開發者明確表達變數是否需要修改，提升代碼的可讀性與安全性。

### 函數定義

Rust 使用 `fn` 關鍵字定義函數，函數參數需要明確指定型別，返回值型別使用 `->` 符號指定：

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b // 無需分號，表示返回值
}

fn main() {
    let result = add(3, 5);
    println!("3 + 5 = {}", result);
}
```

Rust 的函數表達式不需要 `return` 關鍵字，最後一行的值會自動作為返回值。

### 控制流程

Rust 支援常見的控制流程結構，如 `if`、`while` 和 `for`：

```rust
fn main() {
    let number = 7;

    // if 條件不需要括號
    if number > 5 {
        println!("number is greater than 5");
    } else {
        println!("number is 5 or less");
    }

    // for 迴圈遍歷範圍
    for i in 1..4 {
        println!("i = {}", i);
    }
}
```

這些結構與其他語言類似，但 Rust 確保條件表達式必須是布林型別，防止隱式轉換錯誤。

---

## Rust 的基本型別

### 整數型別

Rust 支援有符號和無符號整數，根據大小分為不同型別：

- 有符號：`i8`, `i16`, `i32`, `i64`, `i128`（範圍從負數到正數）
- 無符號：`u8`, `u16`, `u32`, `u64`, `u128`（範圍從 0 到正數）
- 平台相關：`isize`, `usize`（根據系統架構為 32 位或 64 位）

示例：

```rust
fn main() {
    let a: i32 = -42; // 有符號 32 位整數
    let b: u8 = 255;  // 無符號 8 位整數
    println!("a = {}, b = {}", a, b);
}
```

### 浮點型別

Rust 支援兩種浮點型別：

- `f32`：單精度浮點數
- `f64`：雙精度浮點數（預設）

示例：

```rust
fn main() {
    let x: f64 = 3.14;
    println!("x = {}", x);
}
```

### 布林型別

布林型別為 `bool`，值為 `true` 或 `false`：

```rust
fn main() {
    let is_active: bool = true;
    println!("is_active = {}", is_active);
}
```

### 字元與字串

- 字元 (`char`)：表示單個 Unicode 字元，使用單引號。
- 字串 (`String` 和 `&str`)：`String` 是可變的堆分配字串，`&str` 是不可變的字串切片。

示例：

```rust
fn main() {
    let c: char = 'A';
    let s: &str = "Hello";
    let mut s2: String = String::from("World");
    s2.push_str("!");
    println!("c = {}, s = {}, s2 = {}", c, s, s2);
}
```

---

## 學習建議

- **練習基本語法**：編寫簡單的 Rust 程式，使用變數、函數和控制流程，熟悉語法結構。
- **理解型別系統**：嘗試使用不同的基本型別，觀察型別錯誤如何被編譯器捕獲。
- **使用 Rust Playground**：在線上環境中快速測試代碼片段，熟悉語法與行為。