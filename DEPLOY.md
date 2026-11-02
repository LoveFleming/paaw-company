# DEPLOY12 — 2026-11-03 (v4, 最新 ✅)

- 來源：tPAAW dev `1195fbdf`
- 內容：packages/ 全量 425 檔（CU prompt 內建在 `packages/server/src/prompts/code-understanding/`，不需要碰 data/）

## v4 變更（vs v3）
- 🎯 **audit-log 時區修正**：`_tpeTs()` 原公式在非 UTC 機器上把 timestamp 寫成「UTC 時間掛 +08:00 標籤」
  → 修正為台北牆上時間 = 瞬間 +8h，與機器時區無關
  - 影響：audit JSONL 的 `@timestamp` 與檔名日期在 16:00–24:00（跨日時段）會不一致；公司機器若非 UTC 時區全部中獎
- 全套測試 1768 全綠（含原本的定時炸彈測試）

## 更新方式（公司，port 6200）
1. packages/ 重新命名覆蓋（或全量蓋）
2. **重啟 server**（一定要 — 蓋檔不重啟 = 跑舊碼）
3. `npm run build` + 瀏覽器 Ctrl+Shift+R

## 相依性
無依賴變更 → 不用 npm install

## 驗收
- CU 全量重跑 → log 應出現 `step=api-map Running Tree-sitter...`
- audit log（log/logs/audit/audit-YYYY-MM-DD.jsonl）timestamp 與檔名日期一致（+08:00 正確台北時間）
