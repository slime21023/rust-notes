# Rust Cheat Sheet：常用工具與調試

本速查表涵蓋 Rust 開發中常用的工具與調試技巧，適合快速參考如何使用 Cargo、格式化工具、基準測試與調試方法。

Rust 生態系統提供了強大的工具支援高效開發與問題診斷。

---

## 核心工具：Cargo

- **建立新專案**：創建新 Rust 專案。
  ```bash
  cargo new my_project --bin # 可執行專案
  cargo new my_lib --lib # 函數庫專案
  ```
- **建置與執行**：編譯與運行程式。
  ```bash
  cargo build # 建置專案 (debug 模式)
  cargo build --release # 建置優化版本
  cargo run # 建置並執行 (適用於 bin 專案)
  ```
- **測試**：執行單元與整合測試。
  ```bash
  cargo test # 執行所有測試
  cargo test -- --show-output # 顯示測試輸出
  ```
- **依賴管理**：管理專案依賴。
  ```bash
  cargo add serde # 新增依賴到 Cargo.toml
  cargo update # 更新依賴版本
  ```
- **檢查與文檔**：檢查代碼與生成文檔。
  ```bash
  cargo check # 快速檢查語法錯誤，不編譯
  cargo doc --open # 生成並打開 API 文檔
  ```

---

## 代碼格式化與靜態分析

- **rustfmt**：自動格式化 Rust 代碼，確保一致性。
  ```bash
  cargo fmt # 格式化專案中所有代碼
  cargo fmt -- --check # 檢查格式是否符合規範
  ```
- **Clippy**：靜態分析工具，檢測潛在問題與壞味道。
  ```bash
  cargo clippy # 執行 Clippy 檢查
  cargo clippy --fix # 自動修復部分問題
  ```

---

## 調試工具與技巧

- **println! 調試**：最簡單的調試方法，輸出變數值。
  ```rust
  let x = 42;
  println!("x 的值是: {}", x);
  ```
- **dbg! 宏**：快速調試，輸出表達式值與檔案位置。
  ```rust
  let x = 42;
  dbg!(x); // 輸出: [src/main.rs:2] x = 42
  ```
- **Rust Analyzer**：強大的 IDE 支援工具，提供即時錯誤檢查與補全。
  - 安裝：通過 VS Code 擴充功能或直接下載。
  - 功能：語法高亮、即時錯誤、跳轉到定義。
- **GDB/LLDB**：傳統調試器，適合深入調試。
  ```bash
  rust-gdb target/debug/my_program # 使用 GDB 調試
  lldb target/debug/my_program # 使用 LLDB 調試
  ```

---

## 效能分析與基準測試

- **Criterion**：Rust 基準測試框架，用於微基準測試。
  ```rust
  use criterion::{black_box, criterion_group, criterion_main, Criterion};
  fn benchmark_example(c: &mut Criterion) {
      c.bench_function("example", |b| b.iter(|| black_box(1 + 1)));
  }
  criterion_group!(benches, benchmark_example);
  criterion_main!(benches);
  ```
  執行：`cargo bench`
- **perf**：Linux 效能分析工具，分析程式熱點。
  ```bash
  cargo build --release
  perf record ./target/release/my_program
  perf report
  ```
- **Flamegraph**：視覺化效能分析結果。
  ```bash
  cargo flamegraph # 需要安裝 cargo-flamegraph
  ```

---

## 其他實用工具

- **cargo-watch**：自動重新建置與執行，適合開發階段。
  ```bash
  cargo watch -x run # 檔案變更時重新執行
  ```
- **cargo-expand**：展開宏，檢查生成的代碼。
  ```bash
  cargo expand # 展開專案中的宏
  ```
- **rustup**：管理 Rust 工具鏈與版本。
  ```bash
  rustup update # 更新 Rust 版本
  rustup toolchain install nightly # 安裝 nightly 版本
  rustup component add rustfmt # 安裝 rustfmt
  ```
