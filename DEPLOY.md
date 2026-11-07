# deploy15 — 2026-11-07（v11）

**來源：** tPAAW dev `3ba6d39f`（orphan branch，路徑 = tPAAW 相對路徑；packages/ 全量 427 檔）
**上一版：** deploy14（dev `1436ee0d`，2026-11-05）

## 主題

Release Manager / Code Review 委員會 / QA 記錄 / API Tester 大改（**API-first 證據審查**）＋ CU 多語言 ＋ E2E 測試盤點引擎：

1. **Evidence Matrix 改 API-first**：審 API 不審 feature；按 ⚖️ 按鈕才載入；選 baseline commit → 只列之後有變更的 API；選 HEAD → 全部 API（不 by 時間）
2. **Evidence Matrix 7 欄**：API · 名稱 · Unit Test · E2E · SG · Code Review · QA（全 by API+時間）
3. **QA 記錄 API-first**：schema 加 `apis[]`、簽核可標 API、QA 頁 API 過濾
4. **API 現況 5 欄** + 自動 AI 摘要
5. **Review Report** 按鈕在 matrix 塊內
6. **Code Review 會議記錄 by API first**
7. **API Tester 改版**（Postman 式 tab + AI payload）
8. Header 精簡
9. 修正：壞 baseline sha → 400
10. **CU 多語言**（gin / actix-web / spring-petclinic / full-stack-fastapi 真 OSS repo 實測）：測試檔判定多語言慣例、api-map 不吃測試檔假 routes、manifest 多語言偵測、prompt language-agnostic
11. **Semgrep 支援 Rust**：runner 加 `.rs`（rust rule pack 只放 Mac mini，不隨包）
12. **E2E 覆蓋比對修假陰性**：`{param}` 字面 placeholder + 變數前綴後綴降級
13. **🧪 E2E 測試盤點引擎**：deterministic 抽測試名/URL ↔ API 雙向關聯、孤兒測試檔=黑盒缺口。新 lib `e2e-inventory.mjs` + `GET /api/coding-releases/e2e-inventory` + RM 🧪 按鈕表格
14. **API Tester 修正**：存入 collection 後左欄清單即時刷新（browser UI 實測 learning-space 發現：資料有存但 UI 不刷新，要 reload 才看得到）

## Delta vs deploy14（19 檔 packages）

```
M  packages/server/src/lib/cu-source-scan.mjs
A  packages/server/src/lib/e2e-inventory.mjs
M  packages/server/src/lib/qa-results.mjs
M  packages/server/src/lib/semgrep-runner.mjs
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
