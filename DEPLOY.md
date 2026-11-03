# DEPLOY12 — 2026-11-03 (v7, 最新 ✅)

- 來源：tPAAW dev `2d6ec6a1`
- 內容：packages/ 全量 425 檔

## v7 變更（vs v6）
- 🔴 **File Mounts 多掛載修復**：reference_read 原本只讀得到第一個掛載目錄（Fleming 回報）
  - `workspace list` → 列出**所有**掛載（依目錄名）
  - read/list 可帶 `<掛載目錄名>/` 前綴指定某個掛載
  - 沒帶前綴 → 每個掛載逐一找（第一個存在的命中）
  - search → 掃**全部**掛載

## 更新方式（公司，port 6200）
1. packages/ 重新命名覆蓋（或只蓋下面 1 檔）
2. **重啟 server**
3. `npm run build` + Ctrl+Shift+R

## 相依性
無依賴變更 → 不用 npm install

## 只蓋 v7 一個檔案也行（已裝 v6 者）
- packages/server/src/lib/paaw-agent-loop.mjs
