# DEPLOY10 — 公司更新包（2026-10-29 v2）

**來源：** tPAAW dev `b7fdbdaa`（2026-10-29）
**內容：** packages/ 全量 392 檔（tPAAW 相對路徑，直接覆蓋）
**Delta vs deploy9**（`1c3607a9`, 2026-10-26）：**14 修改 / 0 新增 / 0 刪除 — 無依賴變更（不用 npm install）**

## 本次主題

### A. 🔴 審批 gate 修正（b7fdbdaa，重要）

**症狀：** 林雨晴 chat 用任何寫入型工具（bookmark_add、所有 `*_add/_update/_delete/_set`、env_exec）→ 回「等待用戶批准」永遠不執行

**根因：** 10-17 TASK-164 把 approval_gate 接上 ToolEngine（非唯讀工具一律要求批准），production mode='always' → pending；但審批系統沒有任何 API/UI 可以批准 → 2 分鐘 silently 過期 → 全掛

**修法：** chat.mjs / a2a.mjs approval mode 改 `'auto'`；approval.mjs 預設也從 'always' 改 'auto'。硬攔截（exec_security / fs_security / rate limit）照常生效

### B. coding app 證據保鮮 + 覆蓋率系列（8 commits）

1. `6780981e` task commit 成功後自動重跑測試 — 證據保鮮（10-27）
2. `4ecaaf38` jest 專案也自動量行覆蓋率 — 免裝套件（10-27）
3. `8e9aab1b` go test 也自動量覆蓋率 — `-coverprofile=coverage.out`（10-27）
4. `3d3f8c1c` coverage 未量測必附原因（10-27）
5. `725b3c9b` readiness stale 自癒 — 手動/bash commit 後證據矩陣自動保鮮（10-28）
6. `127febb9` 證據矩陣看現況不看 commit 線 — Unit gate 三態 + CU/model 自癒（10-28）
7. `f309c54c` CU 空掃防護 + paawRoot 層數修正（10-28）
8. `9537caf8` test-code-map 多重映射 — 一個測試可測多個生產檔（10-28）

## 變更檔案清單（14 M）

```
M  packages/server/src/lib/code-intelligence.mjs
M  packages/server/src/lib/release-evidence.mjs
M  packages/server/src/lib/release-unit/model.mjs
M  packages/server/src/lib/test-runner.mjs
M  packages/server/src/routes/coding-releases.mjs
M  packages/server/src/routes/coding-tasks.mjs
M  packages/server/src/lib/security/approval.mjs
M  packages/server/src/routes/a2a.mjs
M  packages/server/src/routes/chat.mjs
M  packages/ui/src/components/ReleaseManagerPanel.tsx
M  packages/ui/src/i18n/locales/en.json
M  packages/ui/src/i18n/locales/ja.json
M  packages/ui/src/i18n/locales/zh-mix.json
M  packages/ui/src/i18n/locales/zh.json
```

## SOP

1. 依上方路徑覆蓋 14 檔（**公司已裝 deploy9 的話只需這 14 檔**；全新安裝才需要整包 392 檔）
2. `npm run build`
3. 重啟 server

本次無新增依賴，**不需 npm install**。
