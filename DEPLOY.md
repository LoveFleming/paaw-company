# deploy15 — 2026-11-07（v7）

**來源：** tPAAW dev `15c29b8a`（orphan branch，路徑 = tPAAW 相對路徑，packages/ 全量 426 檔）
**上一版：** deploy14（dev `1436ee0d`，2026-11-05）

## 主題

Release Manager / Code Review 委員會 / QA 記錄 / API Tester 大改（**API-first 證據審查**）＋ CU 多語言：

1. **Evidence Matrix 改 API-first**：審 API 不審 feature；按 ⚖️ 按鈕才載入；選 baseline commit → 只列之後有變更的 API；選 HEAD → 全部 API（不 by 時間）
2. **Evidence Matrix 7 欄**：API · 名稱 · Unit Test · E2E · SG · Code Review · QA（全 by API+時間）
3. **QA 記錄 API-first**：schema 加 `apis[]`、簽核可標 API、QA 頁 API 過濾
4. **API 現況 5 欄** + 自動 AI 摘要
5. **Review Report** 按鈕在 matrix 塊內（`POST /api/coding-releases/review-report`）
6. **Code Review 會議記錄 by API first**（apiIndex）
7. **API Tester 改版**（Postman 式 tab + AI payload）
8. Header 精簡
9. 修正：壞 baseline sha → 400
10. **CU 多語言**（gin / actix-web / spring-petclinic 真 OSS repo 實測）：
    - 測試檔判定含 Go/Python/Rust/Java 慣例（`_test.go`、`test_*.py`、`tests/` 目錄、`XxxTests.java`）
    - api-map 不再吃測試檔假 routes
    - **manifest 多語言偵測**（區段感知 Cargo 解析）：package.json / go.mod / Cargo.toml / pom.xml / build.gradle / requirements.txt / pyproject.toml
    - gen-api-map prompt 補 language-agnostic 說明

## Delta vs deploy14（18 檔）

```
M  packages/server/src/lib/cu-source-scan.mjs
M  packages/server/src/lib/qa-results.mjs
M  packages/server/src/lib/tree-sitter-parser.mjs
M  packages/server/src/prompts/code-understanding/gen-api-map.md
M  packages/server/src/routes/coding-releases.mjs
M  packages/server/src/routes/coding-review-board.mjs
M  packages/server/src/routes/coding.mjs
M  packages/ui/src/components/ApiMapSidebar.tsx
A  packages/ui/src/components/ApiRequestTabSheet.tsx
M  packages/ui/src/components/ApiTesterTabs.tsx
M  packages/ui/src/components/CodeReviewBoardPanel.tsx
M  packages/ui/src/components/QaRecords.tsx
M  packages/ui/src/components/ReleaseManagerPanel.tsx
M  packages/ui/src/i18n/locales/{en,ja,zh-mix,zh}.json
M  packages/ui/src/pages/CodingIDE.tsx
```

## 依賴變更

- root `package-lock.json` 有變（undici 6.29.1 — 3 個 high）。請在公司端跑一次 **`npm install`**。

## 更新步驟（已裝 deploy14 者）

1. 蓋上面檔案（或整包 packages/ 覆蓋）
2. `npm install`
3. `npm run build`
4. 重啟 server
