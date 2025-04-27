# 資料結構的應用：基礎資料結構

Rust 的標準函數庫提供了多種基礎資料結構，這些結構是構建高效應用程式的基石。

---

## 為什麼學習基礎資料結構？

基礎資料結構是程式設計的核心，學習它們有以下好處：

- **高效數據管理**：選擇合適的資料結構能顯著提升程式效能。
- **代碼可讀性**：熟悉標準資料結構有助於編寫清晰且一致的代碼。
- **問題解決能力**：理解資料結構的特性有助於解決各種算法問題。

本章將聚焦於 Rust 標準函數庫中的基礎資料結構及其應用。

---

## 基礎資料結構概覽

### 1. 向量 (`Vec<T>`)

`Vec<T>` 是 Rust 中最常用的動態陣列，支援可變大小的連續存儲：

- **底層機制**：`Vec` 使用堆分配的連續記憶體，當容量不足時會重新分配並移動數據。
- **基本用法**：支援推入、彈出、索引訪問等操作。
  ```rust
  let mut vec: Vec<i32> = Vec::new();
  vec.push(1);
  vec.push(2);
  println!("Vector: {:?}", vec); // 輸出: [1, 2]
  let first = vec[0]; // 索引訪問
  vec.pop(); // 移除最後元素
  println!("After pop: {:?}", vec); // 輸出: [1]
  ```
- **應用場景**：適用於需要動態增長的有序數據列表，如任務佇列或結果收集。

**建議**：若事先知道大致大小，使用 `Vec::with_capacity()` 預分配容量，避免頻繁重新分配。

### 2. 字串 (`String`)

`String` 是 Rust 中用於處理 UTF-8 編碼文本的可變字串類型：

- **底層機制**：`String` 內部是一個 `Vec<u8>`，保證 UTF-8 有效性。
- **基本用法**：支援拼接、切片與轉換操作。
  ```rust
  let mut s = String::from("Hello");
  s.push_str(", World!");
  println!("String: {}", s); // 輸出: Hello, World!
  let slice = &s[0..5]; // 切片，需注意 UTF-8 邊界
  println!("Slice: {}", slice); // 輸出: Hello
  ```
- **應用場景**：適用於需要動態構建文本的場景，如日誌訊息或用戶輸入處理。

**建議**：對於靜態文本，使用 `&str` 切片以減少記憶體分配；操作大量字串時，考慮使用 `String::with_capacity()`。

### 3. 雜湊表 (`HashMap<K, V>`)

`HashMap<K, V>` 是基於雜湊表的鍵值對存儲結構，支援快速查找與插入：

- **底層機制**：使用雜湊函數將鍵映射到內部陣列位置，解決衝突通常使用鏈結法或開放定址法。
- **基本用法**：支援插入、查找與迭代。
  ```rust
  use std::collections::HashMap;
  let mut map = HashMap::new();
  map.insert("key1", 10);
  map.insert("key2", 20);
  if let Some(value) = map.get("key1") {
      println!("Value: {}", value); // 輸出: 10
  }
  for (key, value) in &map {
      println!("{}: {}", key, value);
  }
  ```
- **應用場景**：適用於需要快速查找的場景，如緩存系統或配置存儲。

**建議**：若鍵的順序重要，考慮使用 `BTreeMap`；若效能敏感，可自定義雜湊函數或使用第三方庫。

### 4. 集合 (`HashSet<T>`)

`HashSet<T>` 是無序的唯一元素集合，基於雜湊表實現：

- **底層機制**：類似 `HashMap`，但僅存儲鍵，無關聯值。
- **基本用法**：支援插入、檢查存在與集合操作。
  ```rust
  use std::collections::HashSet;
  let mut set = HashSet::new();
  set.insert(1);
  set.insert(2);
  set.insert(1); // 重複元素不會插入
  println!("Set: {:?}", set); // 輸出: {1, 2} (順序不定)
  if set.contains(&2) {
      println!("Contains 2");
  }
  ```
- **應用場景**：適用於去重或快速成員檢查，如標籤系統或訪問控制列表。

**建議**：若需要有序集合，考慮使用 `BTreeSet`；對於小規模數據，可直接使用 `Vec` 與線性搜索。

---

## 技術挑戰與解決方案

- **記憶體分配**：`Vec` 和 `String` 的重新分配可能導致效能問題；解決方案是預分配容量。
- **UTF-8 處理**：`String` 切片操作需注意字元邊界；使用 `.chars()` 或 `.bytes()` 進行安全操作。
- **雜湊衝突**：`HashMap` 和 `HashSet` 在衝突嚴重時效能下降；可自定義雜湊函數或調整負載因子。

---

## 學習建議

- **實踐練習**：使用 `Vec` 和 `HashMap` 實現簡單的數據處理任務，如字頻統計。
- **源碼閱讀**：研究 `std::vec` 和 `std::collections` 模組源碼，理解其實現細節。
- **效能測試**：使用 `criterion` 比較不同資料結構在特定場景下的效能。

**相關資源**：

- Rust 標準函數庫文檔 (`https://doc.rust-lang.org/std/`)，提供詳細 API 說明。
- Rust 程式設計書籍 (`https://doc.rust-lang.org/book/`)，包含資料結構基礎教學。

---

## 總結

Rust 的基礎資料結構如 `Vec`、`String`、`HashMap` 和 `HashSet` 提供了高效且安全的數據管理能力。

通過理解其底層機制與應用場景，您可以根據需求選擇合適的結構，並通過預分配與正確操作避免常見效能問題。

這些基礎工具是進階資料結構與複雜應用程式的起點，掌握它們將為後續學習奠定堅實基礎。
