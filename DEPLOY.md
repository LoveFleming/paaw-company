# deploy15 — 2026-11-07（v5）

**來源：** tPAAW dev `51faae0f`（orphan branch，路徑 = tPAAW 相對路徑，packages/ 全量 426 檔）
**上一版：** deploy14（dev `1436ee0d`，2026-11-05）

## 主題

Release Manager / Code Review 委員會 / QA 記錄 / API Tester 大改（**API-first 證據審查**）＋ CU 多語言修正：

1. **Evidence Matrix 改 API-first**：審 API 不審 feature；按 ⚖️ 按鈕才載入；下拉選 baseline commit → 只列該 commit 之後有變更的 API，選 HEAD → 全部 API（不 by 時間，release record 概念移除）
2. **Evidence Matrix 欄位（7 欄）**：API · 名稱 · Unit Test · E2E · SG · Code Review · **QA**（都是 by API + 時間）
3. **QA 記錄 API-first 關聯**：qa-results schema 加 `apis[]`；簽核表單可直接標 API；QA 頁加 API 過濾
4. **API 現況改 5 欄** + 自動 AI 摘要
5. **Review Report** 按鈕在 Evidence Matrix 塊內（新端點 `POST /api/coding-releases/review-report`）
6. **Code Review 會議記錄頁 by API first**（apiIndex，可切回依會議）
7. **API Tester 改版**：Postman 式多 request tab + AI 新增 payload
8. Header 精簡（移除 ↻ / 副標 / releases badge）
9. 修正：壞 baseline sha → 400；**CU 多語言**（gin/actix-web/spring-petclinic 三個真 OSS repo 實測）：
   - 測試檔判定加 Go/Python/Rust/Java 慣例（`_test.go`、`test_*.py`、`tests/` 目錄、`XxxTests.java`）→ e2e corpus 不再漏
   - api-map 不再吃測試檔的假 routes（gin 測試 `GET /login`、actix `tests/routes.rs` 這種幻覺訊號全排除）

## Delta vs deploy14（17 檔）

```
M  packages/server/src/lib/cu-source-scan.mjs
M  packages/server/src/lib/qa-results.mjs
M  packages/server/src/lib/tree-sitter-parser.mjs
M  packages/server/src/routes/coding-releases.mjs
M  packages/server/src/routes/coding-review-board.mjs
M  packages/ui/src/components/ApiMapSidebar.tsx
A  packages/ui/src/components/ApiRequestTabSheet.tsx
M  packages/ui/src/components/ApiTesterTabs.tsx
M  packages/ui/src/components/CodeReviewBoardPanel.tsx
M  packages/ui/src/components/QaRecords.tsx
M  packages/ui/src/components/ReleaseManagerPanel.tsx
M  packages/ui/src/i18n/locales/en.json / ja.json / zh-mix.json / zh.json
M  packages/ui/src/pages/CodingIDE.tsx
```

## 依賴變更

- root `package-lock.json` 有變（undici 6.29.1 — 3 個 high）。請在公司端跑一次 **`npm install`**。

## 更新步驟（已裝 deploy14 者）

1. 蓋上面檔案（或整包 packages/ 覆蓋）
2. `npm install`
3. `npm run build`
4. 重啟 server
