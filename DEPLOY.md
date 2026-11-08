# deploy15 v14 — 來源 tPAAW dev `12590200`（2026-11-08 21:00）

> 全量 packages/ 435 檔（tree 驗證 = tPAAW origin/dev packages/ 完全一致）。
> v13 = dev `fb8f5b43`。v14 = v13 + 2 commits：`8c10e0ed`（Troubleshooting）+ `12590200`（CU 卡死修復）。
> **已裝 v13 者只需蓋 7 檔（全部 M），無新增/刪除，無依賴變更（不用 npm install）。**

## Delta vs v13（7 改）

| 檔案 | 變更 |
|---|---|
| `packages/server/src/routes/coding-trouble.mjs` | TS Guide 生成提示詞重寫（SRE runbook 等級：症狀引用原始錯誤字串、fixSteps 含驗證/回滾、無證據→gaps）+ facts 加 project meta |
| `packages/server/src/routes/coding-crew-routes.mjs` | 修讀錯檔名 guide.json → TSGUIDE.json |
| `packages/server/src/routes/a2a.mjs` | A2A message/stream：ops/helpdesk agent 注入 TS Guide 條目庫（side chat 吃得到） |
| `packages/ui/src/components/TroubleshootingPanel.tsx` | 移除 confirmed/drift 計數與「來源: git fix ×N」統計（零人工介入） |
| `packages/ui/src/pages/CodingIDE.tsx` | **CU 卡死修復**：UI 步驟清單落後 server（8步 vs 6步，feature-map/ui-map 無卡）→ SSE 動態補卡；CU model race（ModelSelector 未載入 → body.model 空 → 用 providers defaultModel）→ 補讀 sideChat.em |
| `packages/ui/src/components/EMDashboard.tsx` | CU_STEPS 補 ui-map |
| `packages/server/src/routes/coding.mjs` | ai-initial 開頭 log 本次 model（未指定 → providers.json defaultModel） |

## 公司更新 SOP

1. 依上表 7 檔從 GitHub 逐一開檔下載，按路徑覆蓋 tPAAW
2. `npm run build`
3. 重啟 server（`node src/paaw-server.mjs`）
4. 驗證：coding app 開新專案跑 CU → 應看到 8 張步驟卡依序跑（🗺️ feature-map、🖥️ ui-map 不再隱形）；server log 開頭有 `[cu] [bulk] model = ...`
