# deploy27 — 2026-11-27（Release cancelled 修復 + 維運節環境變數清單）

來源：tPAAW dev `3b8a07bdc`（orphan commit，只含 `packages/**`，路徑 = tPAAW 相對路徑）

## 這一包是什麼

1. **fix(release) cancelled 非終態**（dev `bc6afd0ee`）：release request 的 cancelled 狀態不再是終態 — 未 close 的 RR 一律可重做
2. **feat(spec) 維運節環境變數**（dev `3b8a07bdc`，單檔 +36/-2，全在 `project-md.mjs`）：
   - 第 8 節 schema 加環境變數格式 — 維運節固定列 VAR_NAME 用途 / 預設值 / 在哪設（.env / export / 啟動參數），各一行
   - collectFacts 掃 code 拿證據 — 走 walkSourceFiles 掃 `process.env.X` / `import.meta.env.X`，帶使用行（含預設值）+ 檔案位置進 facts
   - 共用 prompt 加規則 — 環境變數是維運硬事實，照 facts.envVars 逐一列，不得發明證據裡沒有的
   - spec-fill / spec_write / 曉薇「AI 填整份」全部吃到

## Delta vs deploy26（3 改 / 0 新 / 0 刪）

- `M packages/server/src/lib/dfo.mjs`
- `M packages/server/src/lib/project-md.mjs`
- `M packages/server/src/lib/release-requests.mjs`

## 覆蓋方式

1. 依路徑覆蓋 `packages/**`（本次 **442 檔** 全量包；只想蓋差異見上方清單）
2. **無依賴變更**（package.json / package-lock 沒動）— 不必 `npm install`
3. 蓋完 → `npm run build` → 重啟 server

## 已裝 deploy26 者

只需蓋上面 3 個檔案 + `npm run build` + 重啟即可。
