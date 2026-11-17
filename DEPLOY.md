# deploy21 — API Tester UI 清理 + TS Guide AI 挖掘修復

來源：tPAAW dev `711174ec`（2026-11-17）· orphan branch · packages/ 全量 433 檔
基準：deploy20（`ba3d144`，來源 a45c5f58）· delta = 7 檔（4改 + 3語系改）· **無依賴變更（不用 npm install）**

## 主題

1. **API Tester 移除 AI payload UI**（commit 588f40a1）
   - 移除右上「🧪 AI」按鈕（載入 AI 產出 payload 的 handler 一併移除）
   - 移除 Collection tab 的「🤖 AI 新增 payload」卡片（payload 名稱/collection 名/送出給 AI）
   - 💾+ 存入 Collection 保留不變
   - i18n 清掉 8 個 `apiTester.ai*` key（4 個 locale）
2. **TS Guide「AI 挖掘失敗」修復**（commit 711174ec）
   - 根因：thinking 燒光 max_tokens（finish=length、content 空）
   - 修法：mineEntries 補 `thinking:{type:'disabled'}`

## 檔案清單（M = 覆蓋即可）

```
M  packages/server/src/routes/coding-trouble.mjs
M  packages/ui/src/components/ApiRequestTabSheet.tsx
M  packages/ui/src/components/ApiTesterTabs.tsx
M  packages/ui/src/i18n/locales/zh.json
M  packages/ui/src/i18n/locales/en.json
M  packages/ui/src/i18n/locales/ja.json
M  packages/ui/src/i18n/locales/zh-mix.json
```

## 更新步驟（已裝 deploy20 者）

1. 依上面清單蓋 7 檔（或全量蓋 packages/）
2. `npm run build`
3. 重啟 server（coding-trouble.mjs 是 server 端 — 一定要重啟）

> 附註：dev 樹另有 3 個 `packages/server/.paaw/` runtime 產物（changelog/decisions/project md），公司不需要，未收入本包。
