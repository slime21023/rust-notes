# Rust Cheat Sheet：錯誤處理與模式匹配

本速查表涵蓋 Rust 中的錯誤處理與模式匹配機制，適合快速參考如何使用 `Result`、`Option` 與 `match`、`if let` 等工具處理錯誤與結構化數據。

Rust 的設計強調顯式錯誤處理，確保程式健壯性與可靠性。

---

## 錯誤處理

- **Option<T>**：用於處理可能為空的值，包含 `Some(T)` 或 `None`。
  ```rust
  let value: Option<i32> = Some(42);
  if let Some(num) = value {
      println!("值為: {}", num); // 輸出: 42
  } else {
      println!("值為 None");
  }
  // 使用 unwrap_or 提供預設值
  let fallback = value.unwrap_or(0);
  println!("值或預設: {}", fallback); // 輸出: 42
  ```
- **Result<T, E>**：用於處理可能失敗的操作，包含 `Ok(T)` 或 `Err(E)`。
  ```rust
  fn divide(a: i32, b: i32) -> Result<i32, &'static str> {
      if b == 0 {
          Err("除以零錯誤")
      } else {
          Ok(a / b)
      }
  }
  match divide(10, 2) {
      Ok(result) => println!("結果: {}", result), // 輸出: 5
      Err(e) => println!("錯誤: {}", e),
  }
  // 使用 unwrap_or_else 處理錯誤
  let result = divide(10, 0).unwrap_or_else(|e| {
      println!("錯誤: {}", e);
      0
  });
  println!("結果或預設: {}", result); // 輸出: 0
  ```
- **? 運算子**：簡化錯誤傳播，僅在函數返回 `Result` 或 `Option` 時使用。
  ```rust
  fn might_fail() -> Result<i32, &'static str> {
      let value = divide(10, 2)?; // 如果 Err，提前返回
      Ok(value * 2)
  }
  println!("結果: {:?}", might_fail()); // 輸出: Ok(10)
  ```
- **panic! 宏**：用於不可恢復的錯誤，導致程式崩潰。
  ```rust
  fn critical_error() {
      panic!("發生致命錯誤，程式終止");
  }
  // 程式執行到此將崩潰
  ```

---

## 模式匹配

- **match 表達式**：用於結構化數據的分支處理，必須窮盡所有可能。
  ```rust
  let number = 3;
  match number {
      1 => println!("一"),
      2 => println!("二"),
      3 => println!("三"),
      _ => println!("其他"), // _ 通配符匹配剩餘情況
  }
  // 匹配 Option
  let opt: Option<i32> = Some(5);
  match opt {
      Some(value) => println!("值: {}", value), // 輸出: 5
      None => println!("無值"),
  }
  ```
- **if let 表達式**：簡化單一模式匹配，適合簡單場景。
  ```rust
  let opt: Option<i32> = Some(7);
  if let Some(value) = opt {
      println!("值: {}", value); // 輸出: 7
  } else {
      println!("無值");
  }
  ```
- **while let 表達式**：用於循環中匹配模式。
  ```rust
  let mut opt = Some(3);
  while let Some(value) = opt {
      println!("值: {}", value);
      opt = if value > 1 { Some(value - 1) } else { None };
  }
  // 輸出: 3, 2
  ```
- **let 解構**：直接在變數宣告時匹配模式。
  ```rust
  let (x, y) = (1, 2); // 解構元組
  println!("x: {}, y: {}", x, y); // 輸出: 1, 2
  let point = (3, 4);
  if let (x, 0) = point {
      println!("在 x 軸上");
  } else {
      println!("不在 x 軸上"); // 輸出: 不在 x 軸上
  }
  ```

---

## 常見模式與技巧

- **匹配範圍**：使用 `..=` 匹配數字範圍。
  ```rust
  let score = 75;
  match score {
      90..=100 => println!("優秀"),
      60..=89 => println!("合格"), // 輸出: 合格
      _ => println!("不合格"),
  }
  ```
- **匹配結構體與枚舉**：解構複雜類型。
  ```rust
  enum Color {
      RGB(u8, u8, u8),
      Gray(u8),
  }
  let color = Color::RGB(255, 128, 0);
  match color {
      Color::RGB(r, g, b) => println!("RGB({}, {}, {})", r, g, b), // 輸出: RGB(255, 128, 0)
      Color::Gray(value) => println!("Gray({})", value),
  }
  ```
- **忽略部分值**：使用 `_` 忽略不需要的值。
  ```rust
  let tuple = (1, 2, 3);
  match tuple {
      (first, _, third) => println!("第一: {}, 第三: {}", first, third), // 輸出: 1, 3
  }
  ```


