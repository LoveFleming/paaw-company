# 公司更新包 — 2026-09-24 v3（API Tester Collections + 左欄 Tab Sheet，8 檔）

> 相對路徑 = tPAAW repo 相對路徑。對應 tPAAW dev commit：`d2837733`
> ⚠️ 接在 v2（chat.mjs + ChatView.tsx）之後，建議按順序蓋。

## 新功能（Fleming 2026-09-24 需求）

1. **agent 的 `api_test` tool 支援 collection**
   - `listCollections: true` → 列出所有 collection + payloads
   - `collection` + `name`（免 url）→ 直接跑 collection 裡存的 payload
   - `save: true` + `collection` + `name` + `method` + `url`（+ headers/body）→ 存進 collection（同名覆蓋）
   - 原本直接帶 url 打 API 的行為不變
2. **使用者可以請 AI 新增 api test payload by collection** — 跟 agent 說「把這個 API 存到 collection xxx」即可
3. **API Tester 左欄改 tab sheet**
   - 🗂 Feature（原本的 API 地圖，by feature）
   - 📁 Collection（AI/人存的 payload，by collection，可搜尋/刪除）
   - 📜 History（執行歷史，agent 打的帶 🤖、從 collection 跑的帶 📁）
4. **標題列的 📜 history button 移除**（History 已是 tab）；新增 **💾+** 按鈕可手動把目前 request 存入 collection

## 蓋的檔案（8）

| 檔案 | 動作 |
|---|---|
| `packages/server/src/routes/api-tester.mjs` | M（collections CRUD API）|
| `packages/server/src/lib/paaw-agent-loop.mjs` | M（api_test tool collection 模式）|
| `packages/ui/src/pages/CodingIDE.tsx` | M（左欄 tab sheet + 💾+ + 移除 📜）|
| `packages/ui/src/components/ApiTesterTabs.tsx` | **A（新檔）** |
| `packages/ui/src/i18n/locales/{zh,zh-mix,en,ja}.json` | M（apiTester.* 18 keys ×4）|

## 更新步驟

1. 8 檔照相對路徑蓋到公司 tPAAW
2. `npm run build`（有 UI + 新檔）
3. 重啟 server

## 驗證

1. Coding app → API Tester：左欄三個 tab（Feature / Collection / History），標題列沒有 📜 按鈕
2. 測試表單填好一個 request → 💾+ → 輸入 collection/payload 名 → 存檔 → Collection tab 看得到
3. 跟 coding agent 說「列出 api test collections」→ agent 用 listCollections 回報
4. 跟 agent 說「把 GET http://localhost:PORT/api/xxx 存到 collection 測試集」→ Collection tab 出現
5. History tab：agent 打的請求帶 🤖 標記

## 資料檔

`data/api-tester-collections.json` 自動建立（第一次存 payload 時），不需手動建。
