# deploy15 v15 — 來源 tPAAW dev `be4a4bea`（2026-11-08 21:40）

> 全量 packages/ 435 檔（tree 驗證 = tPAAW origin/dev 完全一致）。
> v14 = dev `12590200`。v15 = v14 + 3 commits：`802bba57`（背景 CU model）+ `37b06947`（agent API-first）+ `be4a4bea`（agent UI Map）。
> **已裝 v14 者只需蓋 11 檔（全部 M），無新增/刪除，無依賴變更（不用 npm install）。**

## Delta vs v14（11 改）

| 檔案 | 變更 |
|---|---|
| `packages/server/src/routes/coding.mjs` | 背景 CU 自動增量重跑沿用該 RU 最後指定的 model（記 .paaw/cu-model.json，不再退 providers defaultModel） |
| `packages/server/src/lib/paaw-agent-tools.mjs` | project_info schema 補 api_map/api_detail/ui_map/ui_detail + entry/route 參數 |
| `packages/server/src/lib/paaw-agent-exec.mjs` | agent executor 補 api_map/api_detail/ui_map/ui_detail handler（與 chat 側同源） |
| `packages/server/src/tools/index.mjs` | chat 側 project_info 同步補 ui_map/ui_detail |
| `packages/modules/coding/crews/*.json`（7 檔） | prompts 改 API-first：先查 API Map 再動手（api_map→api_detail→features）；tester/developer 移除死掉的 api-samples.json 教學；動前端先查 UI Map（route-first） |

## 公司更新 SOP

1. 依上表 11 檔逐一開檔下載覆蓋 tPAAW
2. `npm run build`
3. 重啟 server
4. 驗證：coding app 派 developer 問「這專案有 XXX 的 API 嗎」（用 project_info 查）→ 應看到它呼叫 project_info(api_map)；問前端 → 呼叫 ui_map
