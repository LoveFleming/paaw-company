# deploy22 — Prod Spec 8 節 schema + 貼內容一鍵填整份

來源：tPAAW dev `288e21f6`（2026-11-18 22:50）· orphan branch · packages/ 全量 430 檔
基準：deploy21（`a205e315`，來源 711174ec）· delta = 11 檔 · **無依賴變更（不用 npm install）**

## 主題（2026-11-18 Fleming 定調）

**Prod Spec 重做** — 每個 project 通用 8 章節，各節有各自固定 schema（GHS spec 風格，不過度設計）：

1. **8 節各自 schema**：Overview 五塊（定位/目標/範圍內/範圍外/使用情境）；FR = `#### FR-XX — 標題` + **Requirement** / **Acceptance Criteria** / **Business Rule** 條目；NFR、API Contract、架構（mermaid）、技術設計、測試、維運各有模板
2. **貼內容一次填整份**：右側林曉薇 side chat 貼上 project 規格內容 → Prod Spec 頁按「✨ AI 填整份」→ 逐節生成 8 篇寫入（失敗節保留原值；全失敗不寫入）
3. **spec_write tool 整份寫入**：新用法 `sections={1..8: markdown}`，AI 可一次寫整份（單節用法不變）；chat 第一顆 suggestion = 「貼內容填整份」
4. 新 API：`POST /api/coding-project/project-md/spec-fill`

## Delta vs deploy21（11 檔）

**修改（11）**
- `packages/server/src/lib/project-md.mjs`（8 節 schema + generateFullSpec）
- `packages/server/src/lib/paaw-agent-tools.mjs`（spec_write sections 參數）
- `packages/server/src/lib/paaw-agent-exec.mjs`（spec_write 整份寫入執行）
- `packages/server/src/routes/coding.mjs`（spec-fill endpoint）
- `packages/ui/src/components/ProjectMdPage.tsx`（✨ AI 填整份按鈕）
- `packages/ui/src/i18n/locales/zh.json` / `zh-mix.json` / `en.json` / `ja.json`

**手動刪除（3 — deploy17 誤入的 runtime 檔，公司本來就不該有）**
- `packages/server/.paaw/changelog/CHANGELOG.md`
- `packages/server/.paaw/decisions/DECISIONS.md`
- `packages/server/.paaw/project/PROJECT.md`

## 公司更新 SOP

1. 蓋上列 11 檔
2. 刪除上列 3 檔（若有）
3. `npm run build`
4. 重啟 server

## 驗證（Mac mini dev）

- 全套測試 1805 passed（94 files）· 新增 tests/unit/project-md-spec.test.mjs 10 條
- `npm run build` 過（tsc + vite）
