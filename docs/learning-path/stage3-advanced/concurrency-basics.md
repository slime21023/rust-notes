# 並發基礎：Rust 的安全多線程

並發 (Concurrency) 是現代程式設計中的重要主題，允許程式同時處理多個任務，提升性能與響應性。

Rust 提供了獨特的並發機制，確保多線程環境下的記憶體安全。

對於有一定 Rust 基礎的學習者來說，掌握並發基礎是編寫高效程式的重要一步。

---

## 什麼是並發？

並發是指程式能夠同時執行多個任務，這些任務可能在不同線程或同一線程中交錯執行。

在系統編程中，並發常用於提升性能，例如處理多個用戶請求或並行計算。

Rust 並發的核心理念是：**通過所有權與借用規則，在編譯時確保並發安全，避免數據競賽。**

---

## 為什麼需要安全並發？

傳統語言中的並發可能導致問題，例如：

- **數據競賽 (Data Race)**：多個線程同時修改共享數據，導致未定義行為。
- **死鎖 (Deadlock)**：多個線程互相等待資源，導致程式卡住。
- **記憶體不安全**：不當的共享訪問可能導致懸垂指針或使用後釋放錯誤。

Rust 通過其所有權系統與型別檢查，在編譯時防止數據競賽，確保並發安全。

---

## 並發的基本工具

### 線程 (Threads)：基本並發單位

Rust 使用 `std::thread` 模組提供線程支援，允許您創建獨立執行的任務：

```rust
use std::thread;
use std::time::Duration;

fn main() {
    // 創建一個新線程
    let handle = thread::spawn(|| {
        for i in 1..5 {
            println!("Thread: {}", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    // 主線程執行
    for i in 1..3 {
        println!("Main: {}", i);
        thread::sleep(Duration::from_millis(1));
    }

    // 等待新線程完成
    handle.join().unwrap();
}
```

線程允許任務並行執行，但需要注意共享數據的安全性。

### 共享數據：Send 和 Sync 特徵

Rust 使用 `Send` 和 `Sync` 特徵確保並發安全：

- `Send`：表示型別可以安全地跨線程轉移所有權。
- `Sync`：表示型別可以安全地跨線程共享借用。

大多數 Rust 型別預設支援這些特徵，但某些型別（如 `Rc`）不支援跨線程操作。

### 互斥鎖 (Mutex)：安全共享可變數據

`Mutex` 用於在多線程間安全共享可變數據，確保同一時間只有一個線程能修改數據：

```rust
use std::sync::Mutex;
use std::thread;

fn main() {
    let counter = Mutex::new(0);
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Mutex::lock(&counter).unwrap();
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();
            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Final count: {}", *counter.lock().unwrap());
}
```

`Mutex` 通過鎖定機制防止數據競賽，但需要注意避免死鎖。

---

## 學習建議

- **練習線程創建**：編寫簡單多線程程式，觀察線程執行順序與交互行為。
- **理解 Send 和 Sync**：研究不同型別的並發特性，理解為什麼某些型別不能跨線程使用。
- **探索互斥鎖應用**：使用 `Mutex` 共享數據，理解鎖定與解鎖的時機與影響。