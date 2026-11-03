# DEPLOY12 — 2026-11-03 (v8, 最新 ✅)

- 來源：tPAAW dev `0cf29dc8`
- 內容：packages/ 全量 425 檔

## v8 變更（vs v7）— Release Manager 簡化 + API 現況一鍵清點
- 🌐 新按鈕「**API 現況**」（現況報告右上）：一鍵清點前後端 API — 後端 N API（幾個有測試）、前端 K 頁面引用 J 個 API、baseline 後變更的 API 標黃；表格逐 API 顯示（名稱/前端引用/測試/變更）。按鈕才算，不自動燒
- 🤖 表格上方「**AI 摘要**」按鈕：deterministic 統計 → 繁中 5 句現狀（規模/覆蓋/變更/風險判斷）
- Evidence Matrix **移除 Human 欄**（人不逐 feature 簽核）
- 移除「老闆五問」快捷鈕（過度設計）

## 更新方式（公司，port 6200）
1. packages/ 重新命名覆蓋（或只蓋下面 7 檔）
2. **重啟 server**
3. `npm run build` + Ctrl+Shift+R

## 相依性
無依賴變更 → 不用 npm install

## 只蓋 v8 七個檔案也行（已裝 v7 者）
- packages/server/src/routes/coding-releases.mjs
- packages/ui/src/components/ReleaseManagerPanel.tsx
- packages/ui/src/i18n/locales/zh.json / en.json / ja.json / zh-mix.json
