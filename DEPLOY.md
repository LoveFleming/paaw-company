# DEPLOY12 — 2026-11-03 (v5, 最新 ✅)

- 來源：tPAAW dev `a6ff6217`
- 內容：packages/ 全量 425 檔

## v5 變更（vs v4）
- 🎯 **api-map/ui-map 防卡死三件套**：
  ① Tree-sitter 掃描加上限 5000 檔 + 每 500 檔進度回報（大 repo 無上限掃描 + 零回饋 = 看起來卡死）
  ② LLM 生成前發 step_progress 事件（最長 10 分鐘不再無聲轉圈，UI 會顯示「LLM 生成中…」）
  ③ map 步驟 maxRetries 降為 1（避免最壞 40 分鐘無回饋）
- v4：audit-log 時區修正（timestamp 與檔名日期一致）

## 更新方式（公司，port 6200）
1. packages/ 重新命名覆蓋
2. **重啟 server**
3. `npm run build` + Ctrl+Shift+R

## 相依性
無依賴變更 → 不用 npm install

## 只蓋 v5 兩個檔案也行（已裝 v4 者）
- packages/server/src/routes/coding.mjs
- packages/server/src/lib/tree-sitter-parser.mjs
1. packages/ 重新命名覆蓋（或全量蓋）
2. **重啟 server**（一定要 — 蓋檔不重啟 = 跑舊碼）
3. `npm run build` + 瀏覽器 Ctrl+Shift+R

## 相依性
無依賴變更 → 不用 npm install

## 驗收
- CU 全量重跑 → log 應出現 `step=api-map Running Tree-sitter...`
- audit log（log/logs/audit/audit-YYYY-MM-DD.jsonl）timestamp 與檔名日期一致（+08:00 正確台北時間）
