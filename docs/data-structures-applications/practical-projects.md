# 資料結構的應用：實踐專案案例

Rust 的資料結構提供了強大的工具，通過實踐專案可以將理論知識轉化為實際能力。

---

## 為什麼學習實踐專案案例？

實踐專案案例將資料結構應用於具體問題，學習它們有以下好處：

- **理論結合實踐**：將抽象概念轉化為可運行程式。
- **問題解決能力**：通過專案學習分析與解決真實問題。
- **系統設計思維**：培養從需求到實現的完整開發能力。

本章將通過具體專案案例，展示 Rust 資料結構的應用。

---

## 實踐專案案例

### 1. 字頻統計工具

**場景**：構建一個命令列工具，讀取文本檔案並統計每個單詞的出現頻率，輸出結果到螢幕或檔案。

**實現**：

- 使用 `std::fs` 和 `std::io` 讀取檔案。
- 使用 `HashMap` 存儲單詞與頻率。
- 使用 `Vec` 排序結果。
  ```rust
  use std::collections::HashMap;
  use std::env;
  use std::fs::File;
  use std::io::{self, BufRead, BufReader};
  
  fn word_frequency(path: &str) -> io::Result<()> {
      let file = File::open(path)?;
      let reader = BufReader::new(file);
      let mut freq_map = HashMap::new();
      
      for line in reader.lines() {
          let line = line?;
          for word in line.split_whitespace() {
              *freq_map.entry(word.to_lowercase()).or_insert(0) += 1;
          }
      }
      
      let mut freq_vec: Vec<(&String, &i32)> = freq_map.iter().collect();
      freq_vec.sort_by(|a, b| b.1.cmp(a.1)); // 按頻率降序排序
      
      for (word, count) in freq_vec.iter().take(10) {
          println!("{}: {}", word, count);
      }
      Ok(())
  }
  
  fn main() {
      let args: Vec<String> = env::args().collect();
      if args.len() < 2 {
          eprintln!("用法: {} <檔案路徑>", args[0]);
          std::process::exit(1);
      }
      if let Err(e) = word_frequency(&args[1]) {
          eprintln!("錯誤: {}", e);
          std::process::exit(1);
      }
  }
  ```

**優化策略**：

- 使用 `BufReader` 提升檔案讀取效率，減少系統調用。
- 將單詞轉為小寫以統一統計，避免大小寫差異。
- 使用 `Vec` 進行排序，僅輸出前 10 個結果以提升可讀性。

**建議**：對於大規模文本，考慮使用流式處理避免一次性讀取全部內容；可進一步使用 `clap` 庫增強命令列參數解析。

### 2. 任務調度系統

**場景**：設計一個簡單的任務調度系統，支援優先級任務的插入與執行，模擬背景工作者處理。

**實現**：

- 使用 `BinaryHeap` 作為優先佇列，管理任務。
- 使用 `Vec` 儲存處理結果。
- 使用 `std::cmp::Reverse` 實現最小堆效果。
  ```rust
  use std::cmp::Reverse;
  use std::collections::BinaryHeap;
  
  #[derive(Debug)]
  struct Task {
      id: usize,
      priority: i32, // 較小值表示更高優先級
      description: String,
  }
  
  impl Ord for Task {
      fn cmp(&self, other: &Self) -> std::cmp::Ordering {
          self.priority.cmp(&other.priority)
      }
  }
  
  impl PartialOrd for Task {
      fn partial_cmp(&self, other: &Self) -> Option<std::cmp::Ordering> {
          Some(self.cmp(other))
      }
  }
  
  impl PartialEq for Task {
      fn eq(&self, other: &Self) -> bool {
          self.priority == other.priority
      }
  }
  
  impl Eq for Task {}
  
  fn schedule_tasks(tasks: Vec<Task>) -> Vec<String> {
      let mut priority_queue: BinaryHeap<Reverse<Task>> = BinaryHeap::new();
      let mut results = Vec::new();
      
      // 插入任務
      for task in tasks {
          priority_queue.push(Reverse(task));
      }
      
      // 處理任務
      while let Some(Reverse(task)) = priority_queue.pop() {
          results.push(format!("處理任務 {} (優先級: {}): {}", task.id, task.priority, task.description));
      }
      results
  }
  
  fn main() {
      let tasks = vec![
          Task { id: 1, priority: 3, description: "低優先級任務".to_string() },
          Task { id: 2, priority: 1, description: "高優先級任務".to_string() },
          Task { id: 3, priority: 2, description: "中優先級任務".to_string() },
      ];
      let results = schedule_tasks(tasks);
      for result in results {
          println!("{}", result);
      }
  }
  ```

**優化策略**：

- 使用 `BinaryHeap` 結合 `Reverse` 實現最小堆，保證最高優先級任務最先處理。
- 避免不必要的複製，任務描述使用 `String` 擁有所有權。
- 結果使用 `Vec` 收集，支援後續處理或輸出。

**建議**：在真實系統中，可結合 `std::thread` 或 `tokio` 實現並發任務處理；考慮任務超時或取消機制。

### 3. 簡易緩存系統

**場景**：實現一個簡單的緩存系統，支援鍵值對存取與 LRU (Least Recently Used) 淘汰策略。

**實現**：

- 使用 `HashMap` 存儲鍵值對。
- 使用 `VecDeque` 追蹤訪問順序，實現 LRU 策略。
- 使用 `std::sync` 支援多執行緒訪問。
  ```rust
  use std::collections::{HashMap, VecDeque};
  use std::sync::{Arc, Mutex};
  
  struct LRUCache {
      capacity: usize,
      cache: HashMap<String, String>,
      order: VecDeque<String>,
  }
  
  impl LRUCache {
      fn new(capacity: usize) -> Self {
          LRUCache {
              capacity,
              cache: HashMap::new(),
              order: VecDeque::new(),
          }
      }
      
      fn get(&mut self, key: &str) -> Option<&String> {
          if let Some(value) = self.cache.get(key) {
              // 更新訪問順序
              self.order.retain(|k| k != key);
              self.order.push_back(key.to_string());
              return Some(value);
          }
          None
      }
      
      fn put(&mut self, key: String, value: String) {
          if self.cache.contains_key(&key) {
              self.order.retain(|k| k != &key);
          } else if self.cache.len() >= self.capacity {
              // 移除最舊的項目
              if let Some(oldest) = self.order.pop_front() {
                  self.cache.remove(&oldest);
              }
          }
          self.cache.insert(key.clone(), value);
          self.order.push_back(key);
      }
  }
  
  fn main() {
      let cache = Arc::new(Mutex::new(LRUCache::new(3)));
      let mut cache_lock = cache.lock().unwrap();
      cache_lock.put("key1".to_string(), "value1".to_string());
      cache_lock.put("key2".to_string(), "value2".to_string());
      cache_lock.put("key3".to_string(), "value3".to_string());
      println!("Get key1: {:?}", cache_lock.get("key1")); // 輸出: Some("value1")
      cache_lock.put("key4".to_string(), "value4".to_string()); // 淘汰 key2
      println!("Get key2: {:?}", cache_lock.get("key2")); // 輸出: None
  }
  ```

**優化策略**：

- 使用 `HashMap` 提供 O(1) 存取效率。
- 使用 `VecDeque` 高效管理訪問順序，支援 LRU 策略。
- 使用 `Arc<Mutex<_>>` 支援多執行緒安全訪問。

**建議**：在高併發場景中，考慮使用 `dashmap` 替代 `HashMap` 加鎖；可增加緩存過期時間策略。

### 4. 檔案目錄樹結構

**場景**：構建一個工具，遞迴掃描檔案目錄並以樹狀結構顯示，使用資料結構表示目錄層次。

**實現**：

- 使用自定義結構與 `Box` 管理樹狀關係。
- 使用 `std::fs` 讀取目錄內容。
- 使用 `HashMap` 或 `Vec` 存儲子目錄與檔案。
  ```rust
  use std::fs;
  use std::path::Path;
  
  #[derive(Debug)]
  struct DirNode {
      name: String,
      children: Vec<DirNode>,
      files: Vec<String>,
  }
  
  impl DirNode {
      fn new(name: String) -> Self {
          DirNode {
              name,
              children: Vec::new(),
              files: Vec::new(),
          }
      }
      
      fn add_child(&mut self, child: DirNode) {
          self.children.push(child);
      }
      
      fn add_file(&mut self, file: String) {
          self.files.push(file);
      }
      
      fn display(&self, level: usize) {
          let indent = "  ".repeat(level);
          println!("{}{}/", indent, self.name);
          for file in &self.files {
              println!("{}  - {}", indent, file);
          }
          for child in &self.children {
              child.display(level + 1);
          }
      }
  }
  
  fn build_tree<P: AsRef<Path>>(path: P) -> DirNode {
      let path = path.as_ref();
      let mut root = DirNode::new(path.file_name().unwrap().to_string_lossy().to_string());
      if let Ok(entries) = fs::read_dir(path) {
          for entry in entries {
              if let Ok(entry) = entry {
                  let entry_path = entry.path();
                  if entry_path.is_dir() {
                      let child = build_tree(&entry_path);
                      root.add_child(child);
                  } else {
                      root.add_file(entry_path.file_name().unwrap().to_string_lossy().to_string());
                  }
              }
          }
      }
      root
  }
  
  fn main() {
      let tree = build_tree(".");
      tree.display(0);
  }
  ```

**優化策略**：

- 使用遞迴結構表示目錄樹，`Vec` 存儲子目錄與檔案。
- 避免不必要的路徑複製，使用 `to_string_lossy()` 處理非 UTF-8 路徑。
- 支援樹狀顯示，方便用戶閱讀。

**建議**：對於大規模目錄，考慮使用流式處理避免記憶體過載；可增加過濾條件以忽略特定檔案或目錄。

---

## 技術挑戰與解決方案

- **資源限制**：大規模數據處理可能導致記憶體或 CPU 過載；解決方案是使用流式處理或分塊處理。
- **並發安全**：多執行緒訪問資料結構可能引入資料競爭；使用同步原語或無鎖設計解決。
- **錯誤處理**：真實應用中錯誤頻繁發生；需完善錯誤處理與日誌記錄。

---

## 學習建議

- **專案實踐**：選擇一個感興趣的問題，綜合運用多種資料結構實現解決方案。
- **代碼重構**：在完成初步實現後，分析效能瓶頸並重構代碼。
- **社群參與**：將專案分享到 Rust 社群，獲取反饋與改進建議。

**相關資源**：

- Rust 專案範例 (`https://github.com/rust-lang/rust-by-example`)，包含多個實用案例。
- `crates.io` 上的相關庫，如 `clap`、`tokio`，用於增強專案功能。

---

## 總結

通過字頻統計、任務調度、緩存系統與目錄樹結構等實踐專案案例，您可以深入理解 Rust 資料結構在真實場景中的應用。

每個案例都展示了如何綜合運用多種資料結構解決具體問題，並通過優化策略提升效能。

持續實踐與分析這些案例，將幫助您構建高效、安全且可靠的 Rust 應用程式。
