# DEPLOY13 — 2026-11-05

- 來源：tPAAW dev `152ee7d5`（orphan commit，無 dev 歷史）
- 內容：packages/ 全量 425 檔
- 對比 deploy12 v8（680c568）：**5 改 / 0 新 / 0 刪，無依賴變更（不用 npm install）**

## 主題（2 commits）
1. `cdfef5ce` — 🔴 Release Manager 恆降級根因：RU model.mjs `featuresData` 未定義（2026-11-01 rename 漏改）
2. `152ee7d5` — 🔴 no-undef 靜態掃描再抓 5 處未定義變數（SQLite v2 大改範圍 53 檔稽查）：
   - auto-dispatch-manager.mjs：`_startTime` 未定義（EM session 炸）
   - paaw-agent-loop.mjs：`externalReadOnlyDirs` 跨 scope — **MXC 沙箱靜默失效（最嚴重）**
   - coding-features.mjs：migration-report `searchParams`→`q`
   - coding.mjs：`paawPort`×2 → shared `PORT`
   - （doom_loop `messages` 修在同 commit，非 packages 檔按清單覆蓋）

## 已裝 deploy12 v8 者：只需蓋 5 檔
```
M packages/server/src/lib/auto-dispatch-manager.mjs
M packages/server/src/lib/paaw-agent-loop.mjs
M packages/server/src/lib/release-unit/model.mjs
M packages/server/src/routes/coding-features.mjs
M packages/server/src/routes/coding.mjs
```
→ 重啟 server → `npm run build`（UI 無變更，build 快速確認即可）

## 新裝（全量）
抓整個 packages/ 蓋 tPAAW → `npm install`（如換機）→ `npm run build` → 重啟 server
