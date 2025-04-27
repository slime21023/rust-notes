# Rust Cheat Sheet：標準函數庫速查

本速查表涵蓋 Rust 標準函數庫 (`std`) 的常用模組與功能，適合快速參考資料結構、檔案操作、並發工具等核心組件。

Rust 的標準函數庫提供了豐富的功能，支援高效與安全的程式開發。

---

## 常用資料結構 (std::collections)

- **Vec<T>**：動態陣列，支援可變大小。
  ```rust
  let mut vec = vec![1, 2, 3];
  vec.push(4);
  vec.pop(); // 移除並返回最後元素
  println!("Vec: {:?}", vec); // 輸出: [1, 2, 3]
  ```
- **HashMap<K, V>**：雜湊表，鍵值對存儲。
  ```rust
  use std::collections::HashMap;
  let mut map = HashMap::new();
  map.insert("key", 42);
  if let Some(value) = map.get("key") {
      println!("Value: {}", value); // 輸出: 42
  }
  ```
- **HashSet<T>**：無序唯一集合。
  ```rust
  use std::collections::HashSet;
  let mut set = HashSet::new();
  set.insert(1);
  set.insert(2);
  println!("Set contains 2: {}", set.contains(&2)); // 輸出: true
  ```
- **BTreeMap<K, V>** 和 **BTreeSet<T>**：有序映射與集合。
  ```rust
  use std::collections::BTreeMap;
  let mut map = BTreeMap::new();
  map.insert(2, "two");
  map.insert(1, "one");
  for (key, value) in map {
      println!("{}: {}", key, value); // 按鍵有序輸出
  }
  ```
- **VecDeque<T>**：雙端佇列。
  ```rust
  use std::collections::VecDeque;
  let mut deque = VecDeque::new();
  deque.push_back(1);
  deque.push_front(0);
  println!("Deque: {:?}", deque); // 輸出: [0, 1]
  ```
- **BinaryHeap<T>**：二進位堆，預設為最大堆。
  ```rust
  use std::collections::BinaryHeap;
  let mut heap = BinaryHeap::new();
  heap.push(3);
  heap.push(1);
  println!("Top: {:?}", heap.pop()); // 輸出: Some(3)
  ```

---

## 字串與文本處理 (std::str, std::string)

- **String**：擁有所有權的可變字串。
  ```rust
  let mut s = String::from("Hello");
  s.push_str(", World");
  println!("String: {}", s); // 輸出: Hello, World
  ```
- **&str**：字串切片，不可變引用。
  ```rust
  let s: &str = "Hello";
  let slice = &s[0..3];
  println!("Slice: {}", slice); // 輸出: Hel
  ```

---

## 檔案與 I/O (std::fs, std::io)

- **讀取檔案**：使用 `fs::File` 和 `io::BufReader`。
  ```rust
  use std::fs::File;
  use std::io::{self, BufRead, BufReader};
  let file = File::open("file.txt")?;
  let reader = BufReader::new(file);
  for line in reader.lines() {
      println!("{}", line?);
  }
  ```
- **寫入檔案**：使用 `fs::File` 和 `io::Write`。
  ```rust
  use std::fs::File;
  use std::io::Write;
  let mut file = File::create("output.txt")?;
  file.write_all(b"Hello, Rust!")?;
  ```

---

## 並發與同步 (std::thread, std::sync)

- **執行緒**：使用 `thread::spawn` 創建新執行緒。
  ```rust
  use std::thread;
  let handle = thread::spawn(|| {
      println!("來自新執行緒!");
  });
  handle.join().unwrap();
  ```
- **Mutex**：用於互斥鎖，保護共享數據。
  ```rust
  use std::sync::Mutex;
  let data = Mutex::new(0);
  let mut guard = data.lock().unwrap();
  *guard += 1;
  println!("Value: {}", *guard); // 輸出: 1
  ```
- **Arc**：原子引用計數，支援多執行緒共享。
  ```rust
  use std::sync::Arc;
  let data = Arc::new(42);
  let clone = Arc::clone(&data);
  println!("Cloned value: {}", clone); // 輸出: 42
  ```

---

## 時間與日期 (std::time)

- **系統時間**：獲取當前時間。
  ```rust
  use std::time::SystemTime;
  let now = SystemTime::now();
  println!("當前時間: {:?}", now);
  ```
- **持續時間**：計算時間間隔。
  ```rust
  use std::time::{Duration, SystemTime};
  let start = SystemTime::now();
  thread::sleep(Duration::from_millis(100));
  let elapsed = start.elapsed().unwrap();
  println!("經過時間: {:?}", elapsed);
  ```

---

## 環境與命令列 (std::env)

- **命令列參數**：獲取程式執行時的參數。
  ```rust
  use std::env;
  let args: Vec<String> = env::args().collect();
  println!("參數: {:?}", args);
  ```
- **環境變數**：讀取系統環境變數。
  ```rust
  use std::env;
  if let Ok(value) = env::var("PATH") {
      println!("PATH: {}", value);
  }
  ```
