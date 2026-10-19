# DEPLOY7 — 10-16 晚起 → 10-19 更新包（paaw-company 版）

- **來源**：tPAAW dev `3080307d`（2026-10-19，已 push）
- **範圍**：`6728dc4b`（10-16 20:54）起的全部改動，含 10-16 晚 RM/scheduler、10-17~18 retrofit 兩天、10-19 execution-plan 修復
- **檔案路徑 = tPAAW 相對路徑**，逐檔下載覆蓋即可
- ⚠️ 10-16 20:54 以前（含 10-15 skills 報表等）不在本包

## 內容（128 檔 + 本 DEPLOY.md）

### packages/（71）— 本包主力
- **10-16 晚**：RM Evidence Matrix（Human 欄可點擊/嚴重度分流/CTA 跳 EM 預填）、error-codes JSON 修復（error-code-scan.mjs）、cron-jobs 空 body 400、QaRecords/ReleaseManagerPanel/CodingIDE
- **10-17~18**：auto-dispatch-manager（殭屍誤判 ×2 + 停止鏈 + status action）、coding-review-runner、paaw-agent-loop（dispatch 逾時 30min）、a2a（-32600 修正 + a2a-constants + SSRF/CORS）、security 審批接線（TASK-164）、engine IDOR guard、EMDashboard、SettingsPage tab 修復、GitPanel crash 修復、i18n 四 locale
- **10-19**：execution-plan.mjs（createPlan 必炸修復 — 沒這個，UI 建 plan 會 500）

### tests/（31）— 可不蓋，不影響運行
### data/（17）— sre.* crews ×9（新）+ nextjs skill 模板 ×8（不想動 skill 可略過）
### installed-apps/（7）— sre ×5 + secret ×2（TASK-169 harden）
### 根目錄（2）— package.json（+@types/node、@types/sql.js）、vitest.config.ts

## 排除清單（不要覆蓋公司端）
- `.paaw/**` — Mac mini 本機 runtime，紀律一律排除
- `data/app-data/pocket.json`、`data/config/user.json`、`data/config/backup.json`、`data/notes/notebooks.json` — Mac mini 個人狀態，蓋了會污染公司設定
- `installed-apps/learning/**` — 小元寶學習 app，公司不需要

## 更新步驟
1. 逐檔下載覆蓋（tests/ 可跳過）
2. `npm install`（package.json 新增 2 個 devDependencies）
3. `npm run build`
4. 重啟 server
