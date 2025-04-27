# Rust Cheat Sheet：進階特性速查

本速查表涵蓋 Rust 的進階特性，適合快速參考泛型、特徵、生命週期、宏與不安全代碼等概念。

Rust 提供了強大的工具與機制，支援複雜系統的設計與高效實現。

---

## 泛型 (Generics)

- **泛型函數**：允許函數處理多種類型，需指定類型參數。
  ```rust
  fn max<T: PartialOrd>(a: T, b: T) -> T {
      if a > b { a } else { b }
  }
  let int_max = max(5, 10); // 輸出: 10
  let float_max = max(3.14, 2.71); // 輸出: 3.14
  println!("整數最大值: {}, 浮點數最大值: {}", int_max, float_max);
  ```
- **泛型結構體**：結構體字段使用泛型類型。
  ```rust
  struct Pair<T> {
      first: T,
      second: T,
  }
  let pair = Pair { first: 1, second: 2 };
  println!("Pair: {}, {}", pair.first, pair.second);
  ```
- **泛型約束**：使用特徵限制泛型類型。
  ```rust
  fn print_debug<T: std::fmt::Debug>(item: T) {
      println!("{:?}", item);
  }
  print_debug(42); // 輸出: 42
  ```

---

## 特徵 (Traits)

- **定義特徵**：類似接口，定義行為規範。
  ```rust
  trait Printable {
      fn print(&self);
  }
  struct MyStruct(i32);
  impl Printable for MyStruct {
      fn print(&self) {
          println!("值: {}", self.0);
      }
  }
  let item = MyStruct(100);
  item.print(); // 輸出: 值: 100
  ```
- **特徵作為參數**：限制函數參數類型。
  ```rust
  fn display<T: Printable>(item: &T) {
      item.print();
  }
  display(&MyStruct(200)); // 輸出: 值: 200
  ```
- **特徵物件**：動態分發，使用 `dyn` 關鍵字。
  ```rust
  let item: Box<dyn Printable> = Box::new(MyStruct(300));
  item.print(); // 輸出: 值: 300
  ```

---

## 生命週期 (Lifetimes)

- **生命週期標記**：確保引用有效，使用 `'a` 之類的標記。
  ```rust
  fn longest<'a>(s1: &'a str, s2: &'a str) -> &'a str {
      if s1.len() > s2.len() { s1 } else { s2 }
  }
  let str1 = String::from("short");
  let str2 = String::from("longer string");
  let result = longest(&str1, &str2);
  println!("最長字串: {}", result); // 輸出: longer string
  ```
- **結構體中的生命週期**：引用字段需標記生命週期。
  ```rust
  struct Holder<'a> {
      data: &'a str,
  }
  let text = String::from("Hello");
  let holder = Holder { data: &text };
  println!("持有數據: {}", holder.data);
  ```

---

## 宏 (Macros)

- **聲明式宏**：使用 `macro_rules!` 定義簡單宏。
  ```rust
  macro_rules! say_hello {
      () => {
          println!("Hello, Macro!");
      };
  }
  say_hello!(); // 輸出: Hello, Macro!
  ```
- **帶參數的宏**：支援參數化代碼生成。
  ```rust
  macro_rules! repeat {
      ($msg:expr, $times:expr) => {
          for _ in 0..$times {
              println!("{}", $msg);
          }
      };
  }
  repeat!("重複", 3); // 輸出: 重複 (3 次)
  ```
- **過程式宏**：更強大的宏，需外部 crate，如 `proc-macro2`。
  - 參考 `derive` 宏，如 `#[derive(Debug)]`。

---

## 不安全代碼 (Unsafe)

- **unsafe 塊**：允許繞過 Rust 安全檢查，需謹慎使用。
  ```rust
  let mut num = 5;
  let ptr = &mut num as *mut i32;
  unsafe {
      *ptr = 10; // 直接修改記憶體
  }
  println!("修改後: {}", num); // 輸出: 10
  ```
- **常見用途**：調用 C 函數、操作裸指針。
  ```rust
  extern "C" {
      fn abs(input: i32) -> i32;
  }
  let result = unsafe { abs(-5) };
  println!("絕對值: {}", result); // 輸出: 5
  ```
- **注意事項**：不安全代碼需明確標記，避免記憶體洩漏或未定義行為。

---

## 並發進階 (Advanced Concurrency)

- **Send 和 Sync 特徵**：確保並發安全。
    - `Send`：類型可安全跨執行緒移動。
    - `Sync`：類型可安全跨執行緒共享。
    ```rust
    use std::sync::Arc;
    let data = Arc::new(42); // Arc 實現 Send 和 Sync
    let clone = Arc::clone(&data);
    std::thread::spawn(move || {
        println!("在新執行緒中: {}", clone);
    });
    ```
- **RwLock**：支援多讀單寫的鎖。
  ```rust
  use std::sync::RwLock;
  let lock = RwLock::new(5);
  let read_guard = lock.read().unwrap();
  println!("讀取值: {}", *read_guard); // 輸出: 5
  drop(read_guard);
  let mut write_guard = lock.write().unwrap();
  *write_guard = 10;
  println!("修改後: {}", *write_guard); // 輸出: 10
  ```
