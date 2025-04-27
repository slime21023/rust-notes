# 標準函數庫的使用：基礎模組介紹 (進階)

Rust 的標準函數庫 (Standard Library) 是語言的核心支柱，提供了從基本資料結構到系統級操作的豐富功能。

---

## 為什麼深入學習基礎模組？

基礎模組是 Rust 開發的基石，深入理解它們有以下好處：

- **精確控制**：了解模組的底層行為，讓您能精確控制程式行為與效能。
- **自定義擴展**：掌握模組實現原理，為自定義實現或擴展功能奠定基礎。
- **診斷問題**：深入知識有助於調試複雜問題，特別是在涉及記憶體與效能時。

本章將超越基本用法，聚焦於設計理念與進階應用。

---

## 核心基礎模組的進階解析

### 1. 基本資料結構 (`std::collections`)

`std::collections` 模組提供了高效資料結構，其設計與 Rust 的記憶體安全與零成本抽象理念緊密結合：

- **`Vec<T>`**：動態陣列，內部使用指數增長策略管理容量。
  
    - **實現細節**：`Vec<T>` 通過三個指針 (指向數據、容量、長度) 管理記憶體，當容量不足時以 2 倍增長重新分配。
    - **進階用法**：使用 `with_capacity` 預分配容量避免頻繁重新分配。
      ```rust
      let mut vec = Vec::with_capacity(1000);
      for i in 0..1000 {
          vec.push(i);
      }
      println!("Capacity: {}", vec.capacity()); // 輸出: 1000 (避免重新分配)
      ```
    - **效能考量**：注意 `shrink_to_fit` 可能導致記憶體碎片，謹慎使用。
  
- **`HashMap<K, V>`**：基於 Robin Hood 哈希的鍵值對存儲，優化衝突處理。
    - **實現細節**：使用開放定址法與反向索引偏移來減少衝突影響，負載因子約為 0.9 時擴容。
    - **進階用法**：自定義 `Hasher` 來優化特定鍵型別的哈希效能。
      ```rust
      use std::collections::HashMap;
      use std::hash::{Hash, Hasher};
      use std::collections::hash_map::DefaultHasher;
      let mut map: HashMap<&str, i32> = HashMap::new();
      map.insert("key", 42);
      ```
    - **效能考量**：避免頻繁插入/刪除操作導致負載因子波動，影響效能。

- **`HashSet<T>`**：基於 `HashMap` 實現，僅存儲鍵。
    - **實現細節**：內部復用 `HashMap` 的鍵值結構，值部分為空結構。
    - **進階用法**：使用集合運算 (`union`, `intersection`) 進行高效數據處理。

**建議**：深入閱讀 `std::collections` 源碼，理解其記憶體布局與成長策略，特別是對大規模數據處理時的影響。

### 2. 字串處理 (`std::str` 和 `std::string`)

Rust 的字串處理設計考慮了 UTF-8 編碼與記憶體安全：

- **設計理念**：`String` 作為擁有所有權的 UTF-8 緩衝區，`&str` 作為不可變借用切片，保證編碼正確性。
- **實現細節**：`String` 內部基於 `Vec<u8>`，確保 UTF-8 邊界檢查，切片操作需處理多位元組字符。
- **進階用法**：處理大規模文本時，使用 `Cow<str>` 避免不必要複製。
  ```rust
  use std::borrow::Cow;
  fn process_text(text: &str) -> Cow<str> {
      if text.contains("old") {
          Cow::Owned(text.replace("old", "new"))
      } else {
          Cow::Borrowed(text)
      }
  }
  let result = process_text("This is old text");
  println!("{}", result); // 輸出: This is new text
  ```
- **效能考量**：避免頻繁拼接 `String` (導致多次分配)，考慮使用 `String::with_capacity` 或 `format!` 宏。

**建議**：理解 UTF-8 邊界對字串操作的影響，特別是在處理非 ASCII 文本時，避免無效切片操作。

### 3. 錯誤處理 (`std::result` 和 `std::option`)

`Result` 和 `Option` 是 Rust 錯誤處理的核心，體現了顯式錯誤處理的設計理念：

- **設計理念**：強制開發者處理錯誤，避免隱藏異常，增強程式可靠性。
- **實現細節**：`Result` 和 `Option` 是枚舉類型，編譯器會優化其記憶體布局，接近於零成本。
- **進階用法**：使用 `and_then`、`or_else` 進行鏈式錯誤處理，減少巢狀代碼。
  ```rust
  fn parse_and_double(input: &str) -> Result<i32, &str> {
      input.parse::<i32>()
           .map_err(|_| "Parse error")
           .and_then(|n| Ok(n * 2))
  }
  println!("{:?}", parse_and_double("5")); // 輸出: Ok(10)
  println!("{:?}", parse_and_double("abc")); // 輸出: Err("Parse error")
  ```
- **效能考量**：避免過度使用 `unwrap_or_else` 與動態分配，優先使用靜態錯誤值。

**建議**：自定義錯誤類型並實現 `std::error::Error` 特徵，構建複雜應用時的錯誤處理層次。

### 4. 時間與日期 (`std::time`)

`std::time` 模組提供了時間相關功能，設計考慮了跨平台一致性：

- **設計理念**：區分系統時間 (`SystemTime`) 與單調時間 (`Instant`)，滿足不同精度需求。
- **實現細節**：`Instant` 保證單調遞增，適合效能測量；`SystemTime` 可能受系統時鐘調整影響。
- **進階用法**：使用 `Instant` 進行精確的效能基準測試。
  ```rust
  use std::time::Instant;
  let start = Instant::now();
  // 模擬計算任務
  let mut sum = 0;
  for i in 0..1_000_000 {
      sum += i;
  }
  let duration = start.elapsed();
  println!("Time taken: {:?}", duration);
  println!("Sum: {}", sum);
  ```
- **效能考量**：避免頻繁調用 `SystemTime::now()`，因其可能涉及系統調用，影響效能。

**建議**：根據應用場景選擇合適的時間類型，特別是在高精度計時或跨平台應用中。

---

## 技術挑戰與解決方案

- **記憶體安全限制**：標準模組的使用必須遵守所有權與借用規則，可能導致 API 使用複雜性；解決方案是善用借用檢查器提示，設計合理的所有權結構。
- **跨版本兼容性**：標準函數庫隨 Rust 版本更新，某些 API 可能棄用；建議鎖定 Rust 版本或使用條件編譯。
- **進階調試**：當模組行為不符合預期時，檢查 Rust 源碼與使用 `unsafe` 代碼進行低層調試。

---

## 學習建議

- **源碼閱讀**：下載 Rust 標準函數庫源碼，深入研究 `Vec`、`HashMap` 等結構的實現細節。
- **效能分析**：使用 `cargo bench` 對模組操作進行基準測試，理解其效能特性。
- **社群資源**：參與 Rust 社群討論，學習標準函數庫的最佳實踐與常見陷阱。

**相關資源**：

- Rust 標準函數庫源碼 (`https://github.com/rust-lang/rust/tree/master/library/std`)，提供底層實現細節。
- Rust API 指南 (`https://doc.rust-lang.org/std/`)，詳細說明每個模組的用法。
