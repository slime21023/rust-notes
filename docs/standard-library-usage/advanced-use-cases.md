# 標準函數庫的使用：進階應用案例

Rust 的標準函數庫提供了強大的工具，支援從基礎資料處理到複雜系統設計的各種場景。

---

## 為什麼學習進階應用案例？

進階應用案例將標準函數庫的各個部分整合到實際專案中，學習它們有以下好處：

- **綜合應用**：理解如何將多個模組結合使用，解決複雜問題。
- **效能優化**：通過真實場景學習如何分析與優化程式效能。
- **設計思維**：培養系統設計與架構能力，構建可靠且高效的應用程式。

本章將通過具體案例，深入探索標準函數庫的進階應用。

---

## 進階應用案例

### 1. 大規模資料處理管道

**場景**：處理一個包含百萬筆記錄的 CSV 檔案，進行過濾、轉換與匯總，生成報告。

**實現**：

- 使用 `std::fs` 和 `std::io` 進行高效檔案讀取。
- 使用 `std::collections` 和迭代器進行資料處理。
- 使用 `std::thread` 與 `std::sync::mpsc` 實現並行處理。
  ```rust
  use std::fs::File;
  use std::io::{self, BufRead, BufReader};
  use std::sync::mpsc;
  use std::thread;
  use std::collections::HashMap;
  
  fn process_csv(path: &str) -> io::Result<()> {
      let file = File::open(path)?;
      let reader = BufReader::new(file);
      let (tx, rx) = mpsc::channel();
      let mut handles = vec![];
      let chunk_size = 10000;
      let mut current_chunk = vec![];
      
      // 讀取並分塊
      for line in reader.lines() {
          current_chunk.push(line?);
          if current_chunk.len() >= chunk_size {
              let tx = tx.clone();
              let chunk = std::mem::take(&mut current_chunk);
              handles.push(thread::spawn(move || {
                  let mut counts = HashMap::new();
                  for line in chunk {
                      if let Some(field) = line.split(',').next() {
                          *counts.entry(field.to_string()).or_insert(0) += 1;
                      }
                  }
                  tx.send(counts).unwrap();
              }));
          }
      }
      
      // 處理剩餘數據
      if !current_chunk.is_empty() {
          let tx = tx.clone();
          handles.push(thread::spawn(move || {
              let mut counts = HashMap::new();
              for line in current_chunk {
                  if let Some(field) = line.split(',').next() {
                      *counts.entry(field.to_string()).or_insert(0) += 1;
                  }
              }
              tx.send(counts).unwrap();
          }));
      }
      
      drop(tx); // 關閉發送端
      let mut final_counts = HashMap::new();
      for received in rx {
          for (key, value) in received {
              *final_counts.entry(key).or_insert(0) += value;
          }
      }
      
      println!("Final counts: {:?}", final_counts);
      for handle in handles {
          handle.join().unwrap();
      }
      Ok(())
  }
  
  fn main() {
      process_csv("data.csv").unwrap();
  }
  ```

**優化策略**：

- 使用 `BufReader` 減少系統調用，提升讀取效率。
- 將數據分塊並行處理，充分利用多核 CPU。
- 使用通道收集結果，避免鎖爭用。

**建議**：在處理超大檔案時，考慮使用流式處理框架或外部庫如 `csv` 來簡化解析，並結合 `rayon` 進一步優化並行處理。

### 2. 高性能日誌系統

**場景**：構建一個高性能日誌系統，支援多執行緒寫入日誌到檔案，保證低延遲與資料完整性。

**實現**：

- 使用 `std::sync` 進行同步控制。
- 使用 `std::fs` 和 `std::io` 進行檔案操作。
- 使用 `std::thread` 實現後台寫入執行緒。
  ```rust
  use std::fs::{File, OpenOptions};
  use std::io::{self, Write};
  use std::sync::{Arc, Mutex};
  use std::thread;
  use std::time::SystemTime;
  
  struct Logger {
      file: Arc<Mutex<File>>,
  }
  
  impl Logger {
      fn new(path: &str) -> io::Result<Self> {
          let file = OpenOptions::new()
              .write(true)
              .create(true)
              .append(true)
              .open(path)?;
          Ok(Logger { file: Arc::new(Mutex::new(file)) })
      }
      
      fn log(&self, message: &str) {
          let mut file = self.file.lock().unwrap();
          let timestamp = SystemTime::now()
              .duration_since(std::time::UNIX_EPOCH)
              .unwrap()
              .as_secs();
          let log_line = format!("[{}] {}\n", timestamp, message);
          file.write_all(log_line.as_bytes()).unwrap();
          file.flush().unwrap(); // 保證立即寫入
      }
  }
  
  fn main() {
      let logger = Arc::new(Logger::new("app.log").unwrap());
      let mut handles = vec![];
      
      for i in 0..5 {
          let logger = Arc::clone(&logger);
          handles.push(thread::spawn(move || {
              for j in 0..100 {
                  logger.log(&format!("Thread {} - Log {}", i, j));
              }
          }));
      }
      
      for handle in handles {
          handle.join().unwrap();
      }
      println!("Logging completed");
  }
  ```

**優化策略**：

- 使用 `Arc<Mutex<>>` 保證多執行緒安全寫入。
- 考慮使用緩衝區減少 `flush()` 頻率，提升效能。
- 可進一步使用後台執行緒與通道，將日誌寫入與應用邏輯分離。

**建議**：在高吞吐量場景中，考慮使用 `log` 與 `env_logger` 庫，並結合旋轉日誌策略避免檔案過大。

### 3. 並發伺服器模擬

**場景**：模擬一個簡單的並發伺服器，處理多個客戶端請求，支援任務分發與結果收集。

**實現**：

- 使用 `std::thread` 模擬客戶端與工作者執行緒。
- 使用 `std::sync::mpsc` 實現請求分發與結果收集。
- 使用 `std::sync::Arc` 與 `Mutex` 共享狀態。
  ```rust
  use std::sync::{mpsc, Arc, Mutex};
  use std::thread;
  use std::time::Duration;
  
  fn main() {
      let (request_tx, request_rx) = mpsc::channel();
      let (result_tx, result_rx) = mpsc::channel();
      let active_workers = Arc::new(Mutex::new(0));
      let mut worker_handles = vec![];
      
      // 啟動工作者執行緒
      for id in 0..3 {
          let request_rx = request_rx.clone();
          let result_tx = result_tx.clone();
          let active_workers = Arc::clone(&active_workers);
          worker_handles.push(thread::spawn(move || {
              *active_workers.lock().unwrap() += 1;
              while let Ok(request) = request_rx.recv() {
                  println!("Worker {} processing request: {}", id, request);
                  thread::sleep(Duration::from_millis(100)); // 模擬處理時間
                  result_tx.send(format!("Processed {}", request)).unwrap();
              }
              *active_workers.lock().unwrap() -= 1;
          }));
      }
      
      // 模擬客戶端請求
      let mut client_handles = vec![];
      for i in 0..10 {
          let request_tx = request_tx.clone();
          client_handles.push(thread::spawn(move || {
              request_tx.send(format!("Request {}", i)).unwrap();
          }));
      }
      
      drop(request_tx); // 關閉所有發送端
      for handle in client_handles {
          handle.join().unwrap();
      }
      
      // 收集結果
      let results: Vec<String> = result_rx.iter().collect();
      println!("Results: {:?}", results);
      
      // 等待所有工作者完成
      for handle in worker_handles {
          handle.join().unwrap();
      }
      println!("Active workers at end: {}", *active_workers.lock().unwrap());
  }
  ```

**優化策略**：

- 使用有界通道控制背壓，避免過多待處理請求。
- 使用 `Arc<Mutex<>>` 追蹤活躍工作者數量，動態調整資源。
- 可進一步使用條件變數 (`std::sync::Condvar`) 實現更精細的同步。

**建議**：在真實伺服器應用中，考慮使用 `tokio` 或 `async-std` 實現異步 I/O，提升並發處理能力。

### 4. 高效緩存系統

**場景**：構建一個簡單的緩存系統，支援快速鍵值對存取與過期策略，適用於高頻查詢場景。

**實現**：

- 使用 `std::collections::HashMap` 作為緩存存儲。
- 使用 `std::sync` 保證多執行緒安全。
- 使用 `std::time` 實現緩存過期機制。
  ```rust
  use std::collections::HashMap;
  use std::sync::{Arc, Mutex};
  use std::thread;
  use std::time::{Duration, SystemTime};
  
  struct CacheEntry {
      value: String,
      expires_at: Option<u64>, // 過期時間戳 (秒)
  }
  
  struct Cache {
      store: Arc<Mutex<HashMap<String, CacheEntry>>>,
  }
  
  impl Cache {
      fn new() -> Self {
          Cache { store: Arc::new(Mutex::new(HashMap::new())) }
      }
      
      fn set(&self, key: &str, value: &str, ttl_seconds: Option<u64>) {
          let mut store = self.store.lock().unwrap();
          let expires_at = ttl_seconds.map(|ttl| {
              SystemTime::now()
                  .duration_since(std::time::UNIX_EPOCH)
                  .unwrap()
                  .as_secs() + ttl
          });
          store.insert(key.to_string(), CacheEntry {
              value: value.to_string(),
              expires_at,
          });
      }
      
      fn get(&self, key: &str) -> Option<String> {
          let mut store = self.store.lock().unwrap();
          if let Some(entry) = store.get(key) {
              if let Some(expiry) = entry.expires_at {
                  let now = SystemTime::now()
                      .duration_since(std::time::UNIX_EPOCH)
                      .unwrap()
                      .as_secs();
                  if now > expiry {
                      store.remove(key);
                      return None;
                  }
              }
              return Some(entry.value.clone());
          }
          None
      }
  }
  
  fn main() {
      let cache = Arc::new(Cache::new());
      let cache_clone = Arc::clone(&cache);
      
      // 模擬多執行緒訪問
      let mut handles = vec![];
      cache.set("key1", "value1", Some(2)); // 設置 2 秒過期
      cache.set("key2", "value2", None);    // 永不過期
      
      for i in 0..3 {
          let cache = Arc::clone(&cache);
          handles.push(thread::spawn(move || {
              println!("Thread {} - key1: {:?}", i, cache.get("key1"));
              println!("Thread {} - key2: {:?}", i, cache.get("key2"));
          }));
      }
      
      thread::sleep(Duration::from_secs(3)); // 等待 key1 過期
      println!("After expiry - key1: {:?}", cache_clone.get("key1")); // 應為 None
      println!("After expiry - key2: {:?}", cache_clone.get("key2")); // 應有值
      
      for handle in handles {
          handle.join().unwrap();
      }
  }
  ```

**優化策略**：

- 使用 `Arc<Mutex<>>` 保證多執行緒安全存取。
- 實現惰性清理機制，避免頻繁檢查過期項目。
- 可進一步使用 `BTreeMap` 按過期時間排序，實現主動清理。

**建議**：在真實應用中，考慮使用 `dashmap` 或 `lru-cache` 庫，提升緩存效能與功能性。

---

## 技術挑戰與解決方案

- **資源爭用**：多執行緒應用可能導致鎖爭用與效能下降；解決方案是細化鎖粒度或使用無鎖資料結構。
- **記憶體管理**：大規模資料處理可能導致記憶體過度使用；建議使用流式處理與預分配策略。
- **跨平台兼容性**：檔案與 I/O 操作在不同平台行為不一致；可使用條件編譯與標準函數庫抽象解決。

---

## 學習建議

- **專案實踐**：選擇一個真實問題，綜合運用標準函數庫實現解決方案，如命令列工具或小型伺服器。
- **效能分析**：使用 `criterion` 或 `perf` 對案例進行基準測試，識別瓶頸與優化空間。
- **社群參與**：參與 Rust 社群，學習真實專案中的標準函數庫應用模式與最佳實踐。

**相關資源**：

- Rust 標準函數庫文檔 (`https://doc.rust-lang.org/std/`)，提供詳細 API 說明。
- Rust 專案範例 (`https://github.com/rust-lang/rust-by-example`)，包含多個實用案例。
- `crates.io` 上的相關庫，如 `rayon`、`tokio`，用於擴展標準函數庫功能。
