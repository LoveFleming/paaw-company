# deploy15 — 2026-11-07（v3）

**來源：** tPAAW dev `5c5e4534`（orphan branch，路徑 = tPAAW 相對路徑，packages/ 全量 426 檔）
**上一版：** deploy14（dev `1436ee0d`，2026-11-05）

## 主題

Release Manager / Code Review 委員會 / QA 記錄 / API Tester 大改（**API-first 證據審查**）：

1. **Evidence Matrix 改 API-first**：審 API 不審 feature；按 ⚖️ 按鈕才載入；下拉選 baseline commit → 只列該 commit 之後有變更的 API，選 HEAD → 全部 API（不 by 時間，release record 概念移除）
2. **Evidence Matrix 欄位（7 欄）**：API · 名稱 · Unit Test · E2E · SG · Code Review · **QA**（都是 by API + 時間）
3. **QA 記錄 API-first 關聯**：qa-results schema 加 `apis[]`；簽核表單可直接標 API；QA 頁加 API 過濾（舊記錄 fallback 文字比對 target/摘要）
4. **API 現況改 5 欄**：API · 名稱 · Unit Test · E2E · SG（補 E2E/SG 判定 + 自動 AI 摘要）
5. **產生 Review Report 搬進 Evidence Matrix 塊內**：按鈕 → 報告顯示在表格下方（新端點 `POST /api/coding-releases/review-report`）
6. **Code Review 會議記錄頁 by API first**：API 分組 → 該 API 歷次會議；可切回依會議
7. **API Tester 改版**：Postman 式多 request tab + APIs 平面列表 + AI 新增 payload
8. Header 精簡：移除 ↻ 重新整理按鈕、副標、`.paaw/releases/` badge
9. 修正：壞 baseline sha 明確報 400（原本靜默回空矩陣，誤導成無變更）

## Delta vs deploy14（14 檔，全部覆蓋即可）

```
M  packages/server/src/lib/qa-results.mjs
M  packages/server/src/routes/coding-releases.mjs
M  packages/server/src/routes/coding-review-board.mjs
M  packages/ui/src/components/ApiMapSidebar.tsx
A  packages/ui/src/components/ApiRequestTabSheet.tsx
M  packages/ui/src/components/ApiTesterTabs.tsx
M  packages/ui/src/components/CodeReviewBoardPanel.tsx
M  packages/ui/src/components/QaRecords.tsx
M  packages/ui/src/components/ReleaseManagerPanel.tsx
M  packages/ui/src/i18n/locales/en.json
M  packages/ui/src/i18n/locales/ja.json
M  packages/ui/src/i18n/locales/zh-mix.json
M  packages/ui/src/i18n/locales/zh.json
M  packages/ui/src/pages/CodingIDE.tsx
```

## 依賴變更

- **root `package-lock.json` 有變**（undici 升 6.29.1 — tPAAW commit `d68e76c2`，修 3 個 high：TLS 驗證繞過 + DoS）。lock 不在本 branch，請在公司端跑一次 **`npm install`** 讓 lock 更新。

## 更新步驟（已裝 deploy14 者）

1. 蓋上面 14 檔（或整個 packages/ 覆蓋）
2. `npm install`（此次需要 — undici 安全更新）
3. `npm run build`
4. 重啟 server（server route 有改：coding-releases.mjs / coding-review-board.mjs / qa-results.mjs）
