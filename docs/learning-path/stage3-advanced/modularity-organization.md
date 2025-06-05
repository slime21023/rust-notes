# 模組化與代碼組織：Rust 的結構化設計

模組化與代碼組織是 Rust 語言中重要的中級概念，旨在幫助開發者管理大型專案的複雜性，提升代碼的可讀性與可維護性。

對於有一定 Rust 基礎的學習者來說，掌握模組化與代碼組織是編寫結構化程式的重要一步。

---

## 什麼是模組化與代碼組織？

模組化是指將代碼分為獨立且可重用的單元（模組），每個模組負責特定的功能。

代碼組織則涉及如何結構化這些模組與文件，使專案易於理解與擴展。

Rust 模組化的核心理念是：**通過模組與可見性規則，構建清晰的代碼結構，控制功能的訪問範圍。**

---

## 為什麼需要模組化與代碼組織？

在開發大型專案時，缺乏結構化設計可能導致問題，例如：

- **代碼混亂**：所有功能集中在單一文件，難以查找與修改。
- **命名衝突**：缺乏命名空間時，函數或型別可能互相干擾。
- **難以維護**：缺乏清晰結構時，新增功能或除錯變得困難。

Rust 的模組系統與 `Cargo` 工具提供了強大的組織能力，確保代碼結構清晰且易於管理。

---

## 模組化與代碼組織的基本工具

### 模組 (Modules)：邏輯分組

Rust 使用 `mod` 關鍵字定義模組，將相關功能分組在一起：

```rust
// 在 main.rs 中定義模組
mod utils {
    pub fn format_greeting(name: &str) -> String {
        format!("Hello, {}!", name)
    }
}

fn main() {
    let greeting = utils::format_greeting("Alice");
    println!("{}", greeting);
}
```

模組可以嵌套，也可以通過文件分離

### 文件與目錄結構：模組分離

Rust 支援將模組放在不同文件中，使用目錄結構組織代碼。例如：

- `src/main.rs`：
```rust
 // 引用 utils 模組，Rust 會自動查找 src/utils.rs 或 src/utils/mod.rs
mod utils;

fn main() {
    let greeting = utils::format_greeting("Alice");
    println!("{}", greeting);
}
```

- `src/utils.rs`：
```rust
pub fn format_greeting(name: &str) -> String {
    format!("Hello, {}!", name)
}
```

這種結構使大型專案的代碼組織更清晰。

### 可見性 (Visibility)：控制訪問

Rust 使用 `pub` 關鍵字控制模組與功能的訪問範圍：

- `pub`：公開，外部代碼可訪問。
- 無 `pub`：私有，僅模組內部可訪問。

```rust
mod inner {
    pub fn public_function() {
        println!("This is public");
    }
    fn private_function() {
        println!("This is private");
    }
}

fn main() {
    inner::public_function(); // 正確
    // inner::private_function(); // 錯誤，私有函數不可訪問
}
```

### Cargo 與 Crates：專案管理

Rust 使用 `Cargo` 作為專案管理工具，支援多個模組與依賴：

- **Crate**：Rust 的專案或庫單位，可以包含多個模組。
- **Cargo.toml**：定義專案元數據與依賴。

示例 `Cargo.toml`：

```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"

[dependencies]
rand = "0.8.5"
```

使用 `Cargo` 管理依賴與建置，簡化大型專案的開發。

---

## 學習建議

- **練習模組分離**：將代碼分為多個模組與文件，理解模組引用與文件結構。
- **掌握可見性規則**：使用 `pub` 控制功能訪問，設計清晰的模組介面。
- **探索 Cargo 功能**：使用 `Cargo` 建立多模組專案，添加外部依賴，熟悉建置與測試流程。

