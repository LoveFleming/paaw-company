# deploy16 — 來源 tPAAW dev `b5ac6dee`（2026-11-08 21:55）

> 全量 packages/ 435 檔（tree 驗證 = tPAAW origin/dev 完全一致）。
> 內容 = deploy15 v15（`7fb673ff`）+ 1 commit：`b5ac6dee`（dispatch agentMap 補 em）。
> **已裝 deploy15 v15 者只需蓋 1 檔；從 v14 升級蓋 12 檔。無依賴變更（不用 npm install）。**

## 全員實測紀錄（2026-11-08 晚，learning-space）

7/7 PASS，全部用 project_info 查 API Map / UI Map 回答，零猜測：
- developer ✓ api_detail — 還抓出測試題給錯的 endpoint 並用證據糾正
- tester ✓ api_detail validation → error case 清單
- helpdesk ✓ api_detail + process flow 排查回報
- architect ✓ ui_map + api_map 前後端對應
- qa ✓ ui_detail UI flow
- doc-writer ✓ api_map 概述
- EM ✓ ui_map + api_map（並主動標記 UI/API 路徑不一致）

## Delta vs deploy15 v15（1 改）

| 檔案 | 變更 |
|---|---|
| `packages/server/src/routes/coding-crew-routes.mjs` | dispatch agentMap 補 `em`（過去直接 dispatch / EM 自派 404） |

## 公司更新 SOP

1. 蓋上表 1 檔
2. `npm run build`
3. 重啟 server
