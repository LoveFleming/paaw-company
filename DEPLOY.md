# deploy8 — packages/ 整包下載測試（2026-10-22 v3）

## 這包是什麼

**tPAAW `packages/` 全量快照**，共 **393 檔**（server 232 / ui 116 / modules 13 / db 10 / context 8 / engine 8 / shared 6）。

- 來源：tPAAW dev `ee80170d`（2026-10-22 v3，已 push GitHub）
- 目的：在公司試「整包下載覆蓋」流程
- 路徑 = tPAAW 相對路徑，直接對應公司 tPAAW 安裝目錄

## 下載方式（二選一）

1. **整包 ZIP**：GitHub → repo → branch `deploy8` → Code → Download ZIP（含全部 393 檔）
2. **逐一抓檔**：照下方路徑清單從 GitHub web 一檔一檔下載

## 覆蓋步驟

1. 整個 `packages/` 目錄覆蓋公司 tPAAW 的 `packages/`（直接蓋掉）
2. `npm install`（package.json 自 deploy7 後沒變，可不重跑）
3. `npm run build`
4. 重啟 server

## 自 deploy7 起實際變更（27 檔，供比對）

```
M packages/modules/coding/crews/coding.architect.json
M packages/modules/coding/crews/coding.handover.json
M packages/modules/coding/crews/coding.helpdesk.json
M packages/modules/coding/crews/coding.ops.json
M packages/modules/coding/crews/coding.qa.json
M packages/modules/coding/crews/coding.rm.json
M packages/server/src/lib/audit-log.mjs
M packages/server/src/lib/code-intelligence.mjs
M packages/server/src/lib/coding-review-runner.mjs
M packages/server/src/lib/context-truncation.mjs
M packages/server/src/lib/paaw-agent-loop.mjs
M packages/server/src/lib/release-evidence.mjs
M packages/server/src/lib/release-unit/model.mjs
M packages/server/src/lib/test-report-reader.mjs
M packages/server/src/lib/test-runner.mjs
M packages/server/src/routes/coding-releases.mjs
M packages/server/src/routes/coding.mjs
M packages/ui/src/components/AgentSideChat.tsx
M packages/ui/src/components/ApiTesterTabs.tsx
M packages/ui/src/components/QaRecords.tsx
M packages/ui/src/components/ReleaseManagerPanel.tsx
M packages/ui/src/components/TestsPage.tsx
M packages/ui/src/i18n/locales/en.json
M packages/ui/src/i18n/locales/ja.json
M packages/ui/src/i18n/locales/zh-mix.json
M packages/ui/src/i18n/locales/zh.json
M packages/ui/src/pages/CodingIDE.tsx
```

## 重點更新內容（10-20 ~ 10-22）

- **讀檔截斷放大（`ee80170d`，本次新加）**：context 寬鬆時不再截斷 read_file；截斷上限 36K→100K / 12K→40K / 2K→8K；單檔 100K→150K — 林雨晴/所有 agent 讀檔內容不再無故消失
- **Coverage 混合 RU 治本（`03fcc223`）**：深掃各語言 report 位置 + 多份 coverage 行數加權合併（前後端混合 RU 顯示合併值）
- **api_test 開放全 agent（`e6d5b1d6`）**：EM/QA/RM/helpdesk 都能寫 API Tester collection（之前看不到 tool → 幻覺「已寫入」）；Collections tab stale rootPath 修復
- **Agent tool 掛載治本（`f75a1282`）**：13 斷點歸零（rm ru_verify、qa test_run/record_decision、ops/helpdesk bash 診斷、handover/architect prompt 修正）
- 測試可視化：RM/Tests 頁顯示 UT + E2E + 真實行覆蓋率（`68106452`）
- Coding chat 歷史切換治本 v2（`67bb2de6`）
- Release unit 流程治本：test→code 對映、node --test 覆蓋率、semgrep 路徑（`767089f2`）

## 不包含

- `data/`、`.paaw/`、`tests/`、root 設定檔 —— 均不在本包（packages/ 以外無變更需求）
