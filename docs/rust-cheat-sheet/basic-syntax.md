# Rust Cheat Sheet：基本語法速查

本速查表涵蓋 Rust 的基本語法，適合快速參考變數宣告、控制流、函數定義等核心概念。

Rust 是一門注重安全與效能的系統程式語言，其語法設計簡潔且嚴謹。

---

## 變數與資料類型

- **變數宣告**：使用 `let` 宣告變數，預設不可變；使用 `mut` 允許可變。
  ```rust
  let x = 5; // 不可變變數
  let mut y = 10; // 可變變數
  y = 15; // 正確
  // x = 20; // 錯誤：不可變變數無法重新賦值
  ```
- **資料類型**：Rust 是靜態類型語言，支援類型推斷。
  ```rust
  let integer: i32 = 42; // 整數
  let float: f64 = 3.14; // 浮點數
  let boolean: bool = true; // 布林值
  let character: char = 'A'; // 字元 (UTF-8)
  let string_slice: &str = "Hello"; // 字串切片
  let string: String = String::from("World"); // 擁有所有權的字串
  ```
- **常量**：使用 `const` 宣告，必須明確類型且值在編譯時確定。
  ```rust
  const MAX_VALUE: i32 = 100;
  ```

---

## 控制流

- **條件語句**：使用 `if`、`else if` 和 `else`。
  ```rust
  let number = 7;
  if number < 5 {
      println!("小於 5");
  } else if number == 5 {
      println!("等於 5");
  } else {
      println!("大於 5");
  }
  ```
- **條件表達式**：`if` 可以作為表達式返回結果。
  ```rust
  let result = if number > 0 { "正數" } else { "非正數" };
  println!("結果: {}", result);
  ```
- **循環**：支援 `loop`（無限循環）、`while` 和 `for`。
  ```rust
  // 無限循環，直到 break
  let mut counter = 0;
  loop {
      counter += 1;
      if counter == 3 { break; }
      println!("計數: {}", counter);
  }
  // while 循環
  while counter < 5 {
      counter += 1;
      println!("while 計數: {}", counter);
  }
  // for 循環 (範圍)
  for i in 1..4 { // 1 到 3
      println!("for 計數: {}", i);
  }
  ```

---

## 函數

- **函數定義**：使用 `fn` 關鍵字，參數需明確類型，返回值可選。
  ```rust
  fn add(a: i32, b: i32) -> i32 {
      a + b // 無分號表示返回值
  }
  fn main() {
      let sum = add(3, 5);
      println!("總和: {}", sum); // 輸出: 8
  }
  ```
- **無返回值**：省略 `->` 部分，預設返回 `()`。
  ```rust
  fn print_hello() {
      println!("Hello, Rust!");
  }
  ```

---

## 陣列與切片

- **陣列**：固定大小，類型與長度在編譯時確定。
  ```rust
  let arr: [i32; 3] = [1, 2, 3];
  println!("第一個元素: {}", arr[0]);
  ```
- **切片**：陣列或 `Vec` 的引用，長度動態。
  ```rust
  let slice: &[i32] = &arr[1..3]; // 包含索引 1 和 2
  println!("切片: {:?}", slice); // 輸出: [2, 3]
  ```

---

## 向量 (Vec)

- **向量**：動態陣列，支援增長與縮減。
  ```rust
  let mut vec: Vec<i32> = Vec::new();
  vec.push(1);
  vec.push(2);
  println!("向量: {:?}", vec); // 輸出: [1, 2]
  let first = vec.get(0); // 安全訪問
  println!("第一個元素: {:?}", first); // 輸出: Some(1)
  vec.pop(); // 移除最後元素
  println!("移除後: {:?}", vec); // 輸出: [1]
  ```

---

## 所有權與借用簡介

- **所有權**：每個值只有一個擁有者，移動後原變數不可用。
  ```rust
  let s1 = String::from("Hello");
  let s2 = s1; // 所有權移動
  // println!("s1: {}", s1); // 錯誤：s1 已移動
  println!("s2: {}", s2);
  ```
- **借用**：使用 `&` 借用不可變引用，`&mut` 借用可變引用。
  ```rust
  let mut s = String::from("Hello");
  let r1 = &s; // 不可變借用
  let r2 = &mut s; // 可變借用
  // println!("r1: {}", r1); // 錯誤：不可變借用與可變借用衝突
  r2.push_str(", World");
  println!("r2: {}", r2);
  ```