# Rust 學習筆記 (Rust Learning Notes)

歡迎來到 Rust 學習筆記！本專案旨在提供一個基於第一性原理與設計理念的系統化 Rust 學習路徑與資源集合。

Rust 是一門以**性能**、**安全性**和**並發性**為核心設計理念的現代系統程式語言。這些筆記將幫助您從基礎到進階，逐步掌握 Rust 的核心概念與技術細節。

## 專案結構

本專案使用 [MkDocs](https://www.mkdocs.org/) 搭配 [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) 主題來組織和呈現學習筆記。主要內容位於 `docs` 目錄下，並按照以下結構組織：

- **`docs/index.md`**: 專案首頁，介紹 Rust 的核心理念與學習路徑概述。
- **`docs/learning-path/`**: 詳細的五階段學習路徑，涵蓋從入門到專家級的各個方面。
- **`docs/learning-tips-resources/`**: 學習 Rust 的技巧、推薦資源和學習方向。
- **`docs/standard-library-usage/`**: Rust 標準函式庫的常用模組介紹與實用範例。
- **`docs/advanced-topics/`**: 深入探討 Rust 的進階主題，如異步編程、`unsafe` Rust 等。
- **`docs/ecosystem-projects/`**: 介紹 Rust 生態中的重要工具、框架和實際應用案例。

## 如何使用

### 本地預覽

1.  **安裝 MkDocs 和 Material 主題**:
    ```bash
    pip install mkdocs mkdocs-material
    ```
2.  **啟動本地伺服器**:
    在專案根目錄下執行以下命令：
    ```bash
    mkdocs serve
    ```
3.  **瀏覽筆記**:
    打開瀏覽器並訪問 `http://127.0.0.1:8000`。

