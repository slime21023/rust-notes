# 函數式編程特性：Rust 的抽象與表達力

函數式編程 (Functional Programming) 是一種程式設計範式，強調不可變性、純函數與高階函數。

Rust 雖然主要是一門系統編程語言，但融入了許多函數式編程特性，提升代碼的抽象性與表達力。

對於有較深 Rust 基礎的學習者來說，掌握函數式編程特性是編寫優雅且高效代碼的重要一步。

---

## 什麼是函數式編程特性？

函數式編程特性包括不可變數據、純函數（無副作用）、閉包 (Closures)、模式匹配與高階函數等。

Rust 將這些特性與其所有權系統結合，確保安全與性能。

Rust 函數式編程的核心理念是：**通過抽象與不可變性，提升代碼的可預測性與可組合性。**

---

## 為什麼需要函數式編程特性？

在複雜程式設計中，命令式編程可能導致問題，例如：

- **副作用難追踪**：可變狀態與副作用使代碼行為難以預測。
- **代碼難重用**：缺乏抽象機制時，功能難以組合與重用。
- **並發不安全**：可變共享狀態可能導致數據競賽。

Rust 的函數式特性通過不可變性與高階函數，減少副作用，提升代碼的可讀性與安全性。

---

## 函數式編程的主要特性

### 不可變性與純函數

Rust 預設變數不可變，鼓勵使用純函數（不依賴或修改外部狀態）：

```rust
fn double(x: i32) -> i32 {
    // 純函數，無副作用
    x * 2 
}

fn main() {
    let x = 5;
    let result = double(x);
    println!("Double of {} is {}", x, result);
}
```

不可變性與純函數使代碼行為更可預測，特別是在並發環境中。

### 閉包 (Closures)：捕獲環境

閉包是 Rust 中的匿名函數，可以捕獲其定義環境中的變數：

```rust
fn main() {
    let base = 10;

    // 閉包捕獲 base
    let add_base = |x: i32| x + base; 
    println!("Result: {}", add_base(5)); // 輸出 15
}
```

閉包結合所有權規則，確保捕獲變數的安全訪問。

### 高階函數與迭代器

Rust 支援高階函數（接受函數作為參數或返回函數），並提供強大的迭代器 API：

```rust
fn main() {
    let numbers = vec![1, 2, 3, 4, 5];
    let doubled: Vec<i32> = numbers.into_iter().map(|x| x * 2).collect();
    println!("Doubled: {:?}", doubled); // 輸出 [2, 4, 6, 8, 10]
}
```

迭代器方法如 `map`、`filter` 和 `fold` 提供函數式風格的操作，簡化數據處理。

### 模式匹配與 Result/Option 組合

模式匹配與 `Result`/`Option` 的函數式處理方式，使錯誤處理更具表達力：

```rust
fn process_data(data: Option<i32>) -> Option<i32> {
    data.and_then(|x| if x > 0 { Some(x * 2) } else { None })
}

fn main() {
    let value = Some(3);
    println!("Processed: {:?}", process_data(value)); // 輸出 Some(6)
    let none = Some(-1);
    println!("Processed: {:?}", process_data(none)); // 輸出 None
}
```

這種組合方式避免了巢狀條件，提升代碼清晰度。

---

## 學習建議

- **練習閉包應用**：使用閉包處理數據，理解其與環境變數的交互。
- **探索迭代器 API**：使用 `map`、`filter` 等方法處理集合，熟悉函數式風格。
- **設計純函數**：嘗試將功能分解為無副作用的函數，提升代碼可預測性。
