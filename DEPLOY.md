# Rollback 包 — 回到 9/23 晚上 EM 三連改之前（7 檔）

> 基準：tPAAW dev `e0f34d11`（2026-09-22 14:47，b007f0f9 的 parent）
> 這會完整還原 9/23 晚三個 commit（b007f0f9 / bc657c2e / 8df627fb）
> 和 9/24 兩輪修復（748cec51 / 93d1aea7）動到的所有檔案。

## 檔案（7）

| 檔案 | 說明 |
|---|---|
| `packages/server/src/lib/llm-utils.mjs` | 還原（拿掉跨 provider 自動路由）|
| `packages/server/src/lib/auto-dispatch-manager.mjs` | 還原（拿掉閉環多輪 + fallbacks 傳遞）|
| `packages/server/src/lib/em-config.mjs` | 還原（拿掉 closedLoop 區塊）|
| `packages/server/src/lib/paaw-agent-loop.mjs` | 還原（400/401/403/404 不走 fallback）|
| `packages/server/src/routes/a2a.mjs` | 還原（不收 metadata.fallbacks）|
| `packages/server/src/routes/chat.mjs` | 還原（過期 provider 回 400 舊行為）|
| `packages/ui/src/pages/ChatView.tsx` | 還原（不驗證 localStorage 偏好）|

## 步驟

1. 7 檔照相對路徑蓋回公司 tPAAW
2. `npm run build`（有 UI 檔）
3. 重啟 server

## ⚠️ 老實說：這包大概率治不了「尚未設定 AI Model」

那個跳頁髮生在**瀏覽器端、訊息送出之前**：UI 問 `/api/paaw/providers`
「active provider 有沒有 key」，沒有就跳設定頁。這 7 檔沒有任何一支
影響那個檢查 — **如果 rollback 後還跳，就能 100% 確定是公司
`data/config/providers.json` 的 active provider 沒 key（或檔案壞了）**，
那是設定問題不是 code 問題，rollback 治不了，要把 key 補回/active 切回。

## 先花 30 秲確認（強烈建議 rollback 前先做）

公司瀏覽器開：`http://localhost:4097/api/paaw/providers`

看回傳 JSON：
- `"active": ""` 或 `"providers": {}` → providers.json 壞了/讀不到 → 從備份還原
- `active` 指向的 provider 其 `apiKey` 是 `""` 或 `"na"` → 去 **設定 → AI Provider** 補 key 或把 active 切回有 key 的 provider → 聊天立刻復活（不用 rollback）
