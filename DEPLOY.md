# DEPLOY14 — 2026-11-06

- 來源：tPAAW dev `1436ee0d`（orphan branch，無歷史）
- 內容：packages/ 全量 **425 檔**（本 branch 只含 packages/，路徑 = tPAAW 相對路徑）
- **無依賴變更 — 不用 npm install**，覆蓋後 `npm run build` + 重啟 server 即可

## Delta vs deploy13（cfb25a1，來源 152ee7d5）— 3 改 / 0 新 / 0 刪

| 檔案 | 主題 |
|---|---|
| `packages/ui/src/components/ReleaseManagerPanel.tsx` | 移除現況報告卡（READY/stats/Tests/風險）+ 品質債現況區塊；Review Report / API 現況按鈕搬 header（cbdd6bdc, 05af6486） |
| `packages/ui/src/components/EMDashboard.tsx` | CU_STEPS 加 feature-map step（1436ee0d） |
| `packages/server/src/routes/coding.mjs` | scan.json 剝 LLM code fence（4dcf9cc1）+ CU pipeline feature-map v2 復活（1436ee0d） |

## 已裝 deploy13 者
蓋上面 3 檔 → `npm run build` → 重啟 server，即可。
