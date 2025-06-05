# 錯誤處理基礎：Rust 的可靠設計

錯誤處理是 Rust 語言中一個重要的基礎概念，旨在幫助開發者明確處理程式中可能出現的異常情況，避免隱藏錯誤或未定義行為。

對於有基本 Rust 知識的學習者來說，掌握錯誤處理是編寫可靠程式的重要一步。

---

## 什麼是錯誤處理？

在程式設計中，錯誤是不可避免的，例如文件讀取失敗、網絡連接中斷或無效輸入。

Rust 的錯誤處理機制要求開發者顯式處理這些潛在失敗，而不是忽略或依賴例外 (exception) 機制，以確保程式行為可預測且可靠。

Rust 錯誤處理的核心理念是：**錯誤是值，必須被顯式處理。**

---

## 為什麼需要顯式錯誤處理？

傳統語言中的錯誤處理可能導致問題，例如：

- **隱藏錯誤**：未檢查的錯誤被忽略，導致程式在後續階段崩潰。
- **複雜例外流程**：例外機制可能使代碼控制流程難以追踪。
- **運行時開銷**：例外處理可能引入不可預測的性能成本。

Rust 通過 `Result` 和 `Option` 型別，將錯誤作為普通值處理，強制開發者在編寫代碼時考慮失敗情況。

---

## 錯誤處理的基本工具

### Option 型別：處理可能不存在的值

`Option` 型別用於表示值可能存在或不存在，類似於其他語言中的 `null`，但更安全：

```rust
fn find_first_space(s: &str) -> Option<usize> {
    for (i, c) in s.chars().enumerate() {
        if c == ' ' {
            return Some(i);
        }
    }
    None
}

fn main() {
    let text = "Hello World";
    match find_first_space(text) {
        Some(index) => println!("First space at index: {}", index),
        None => println!("No space found"),
    }
}
```

`Option` 有兩種變體：`Some(T)` 表示值存在，`None` 表示值不存在。

Rust 強制您處理這兩種情況，避免忽略 `None`。

### Result 型別：處理可能失敗的操作

`Result` 型別用於表示操作可能成功或失敗：

```rust
use std::fs::File;

fn open_file(path: &str) -> Result<File, std::io::Error> {
    File::open(path)
}

fn main() {
    match open_file("example.txt") {
        Ok(file) => println!("File opened successfully: {:?}", file),
        Err(e) => println!("Failed to open file: {}", e),
    }
}
```

`Result` 有兩種變體：`Ok(T)` 表示成功，`Err(E)` 表示失敗。

Rust 強制您處理錯誤分支，確保失敗不會被忽略。

### 簡化錯誤處理：? 操作符

`?` 操作符用於簡化錯誤傳播，特別是在函數返回 `Result` 或 `Option` 時：

```rust
use std::fs::File;
use std::io::Read;

fn read_file(path: &str) -> Result<String, std::io::Error> {
    let mut file = File::open(path)?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}

fn main() {
    match read_file("example.txt") {
        Ok(contents) => println!("File contents: {}", contents),
        Err(e) => println!("Error reading file: {}", e),
    }
}
```

`?` 操作符會在操作失敗時提前返回錯誤，簡化代碼結構。

---

## 學習建議

- **練習使用 Option 和 Result**：編寫簡單程式，處理可能不存在的值或可能失敗的操作，熟悉模式匹配。
- **避免 unwrap() 的濫用**：雖然 `unwrap()` 可以快速獲取值，但可能導致程式 panic，建議僅在測試或確定安全時使用。
- **探索錯誤傳播**：使用 `?` 操作符簡化錯誤處理，理解其在函數中的行為。