# deploy15 — 2026-11-08（v12 · 第一版 release 候選）

**來源：** tPAAW dev `dcf5fe17`（packages/ 全量 435 檔，路徑 = tPAAW 相對路徑）
**上一版：** deploy15 v11（dev `3ba6d39f`，2026-11-07）

## 依賴

**無依賴變更（不用 npm install）** — 直接蓋檔 → `npm run build` → 重啟 server。

## 主題（v11 → v12，34 commits，30 檔變更 = 8 新增 + 22 修改）

**EM 派工大改 + 三大盤點修正 + Handover 交接文件 v2：**

1. **派工 Job Catalog**（新增 `dispatch-catalog.mjs` + `DispatchWizard.tsx`）：派工 popup 改「工作分類」6 張卡（open-tasks / review-board / security-loop / test-gen / release-prep / direct）；參數精簡（open-tasks/security 零參數、委員會=天數、direct=agent+task）；maxTickets 不再卡完成度；新增 `coding-crew-routes.mjs` 獨立 route 模組（review-board-run SSE、dispatch、QA 記錄 API）
2. **Page 盤點**（新增 `page-inventory.mjs`）：路由 + tab fallback（tab-view 掃描）+ API 接線；kind 標籤修正
3. **E2E 盤點修正**（`e2e-inventory.mjs`）：誤抓修正（.md/semgrep 規則不再算測試檔）→ 檔案數真實化；孤兒（有路徑無 API）vs 黑盒（UI 驅動）分家徽章；零訊號/系統路徑過濾
4. **CU 過期自動保鮮**（`cu-source-scan.mjs`）：stale 自動重跑（背景、不卡 UI）
5. **Handover 交接文件 v2**（`coding-handover.mjs` 大改 + `HandoverPanel.tsx` 重寫）：
   - 移除 Git 演進史/進行中工作
   - 新 9 面向 AI 採證據生成：專案定位/技術棧/Storage/外部服務與金鑰/認證/Cache/高風險區/**AI 開發方式（SA 開 task→EM 派工→review→release）**/怎麼跑起來（含接手第一步 3 步起手式）
   - 疑難排解 = helpdesk agent FAQ
   - **一鍵更新**（POST /api/coding-handover/doc；進頁零 token）
   - Handover side chat 注入交接文件 context
6. **修 6 個 route 模組載入失敗**（`coding-helpers.mjs` 拆分後 callProjectLLM import 斷 → handover/features/trouble/skill-suggest/error-codes/c4 靜默 404）
7. **PROJECT.md User Remarks 標題滾雪球修正**（`project-md.mjs` — 每輪重生成疊一層）
8. coding.mjs 拆分（`coding-helpers.mjs` / `paaw-agent-{loop,tools,exec}.mjs` / `coding-ide-helpers.tsx`）

## Delta 檔案清單（v11 → v12，直接覆蓋這 30 檔即可）

```
A  packages/server/src/lib/dispatch-catalog.mjs
A  packages/server/src/lib/page-inventory.mjs
A  packages/server/src/lib/paaw-agent-exec.mjs
A  packages/server/src/lib/paaw-agent-loop.mjs
A  packages/server/src/lib/paaw-agent-tools.mjs
A  packages/server/src/routes/coding-crew-routes.mjs
A  packages/server/src/routes/coding-helpers.mjs
A  packages/ui/src/components/DispatchWizard.tsx
A  packages/ui/src/pages/coding-ide-helpers.tsx
M  packages/server/src/lib/cu-source-scan.mjs
M  packages/server/src/lib/e2e-inventory.mjs
M  packages/server/src/lib/em-job-entrypoints.mjs
M  packages/server/src/lib/project-md.mjs
M  packages/server/src/routes/coding-c4-model.mjs
M  packages/server/src/routes/coding-error-codes.mjs
M  packages/server/src/routes/coding-features.mjs
M  packages/server/src/routes/coding-handover.mjs
M  packages/server/src/routes/coding-health.mjs
M  packages/server/src/routes/coding-releases.mjs
M  packages/server/src/routes/coding-skill-suggest.mjs
M  packages/server/src/routes/coding-trouble.mjs
M  packages/server/src/routes/coding.mjs
M  packages/ui/src/components/EMDashboard.tsx
M  packages/ui/src/components/HandoverPanel.tsx
M  packages/ui/src/components/ReleaseManagerPanel.tsx
M  packages/ui/src/i18n/locales/en.json
M  packages/ui/src/i18n/locales/ja.json
M  packages/ui/src/i18n/locales/zh-mix.json
M  packages/ui/src/i18n/locales/zh.json
M  packages/ui/src/pages/CodingIDE.tsx
```

## 公司更新 SOP

1. 逐一開檔下載覆蓋（路徑即 tPAAW 相對路徑；懶得挑就整個 `packages/` 目錄覆蓋 = 435 檔）
2. `npm run build`
3. 重啟 server
4. 驗證：EM 頁 🚀 派工 → 6 張卡；RM → Page 盤點；Handover → 按「🔄 重新產生」→ 9 面向文件

## 排除（照舊政策）

`.paaw/`（本機 runtime + agent override）、`data/semgrep-rules/golang/`、個人 runtime data
