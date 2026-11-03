# DEPLOY12 — 2026-11-03 (v6, 最新 ✅)

- 來源：tPAAW dev `c588d505`
- 內容：packages/ 全量 425 檔

## v6 變更（vs v5）— 公司端「api-map 綠燈後卡死」完整修復
- 🔴 **真兇**：純後端專案 ui-map 標 done 後寫了 `return` → 直接結束整個 bulk CU run，code-intelligence / test-intelligence / error-codes / c4-model 全部不跑 → 改 `continue`
- tree-sitter 掃描每 100 檔讓出 event loop — 無上限掃描大 repo 會凍死 server（UI 所有輪詢 timeout = console 一堆 a2a architect 錯誤的來源）
- code-intelligence 掃描進度即時回報 UI（Tree-sitter 分析中 x/x 檔）
- ui-map 解析失敗但回應 <300 字 → 一律判定 backend-only 標 done（不再誤報紅燈）
- api-map/ui-map 失敗不再落入通用 step_done（修 error+done 雙事件）

## 更新方式（公司，port 6200）
1. packages/ 重新命名覆蓋
2. **重啟 server**
3. `npm run build` + Ctrl+Shift+R
4. RU 重新 import（無 .paaw）→ CU 全量跑

## 相依性
無依賴變更 → 不用 npm install

## 只蓋 v6 三個檔案也行（已裝 v5 者）
- packages/server/src/routes/coding.mjs
- packages/server/src/lib/tree-sitter-parser.mjs
- packages/server/src/lib/code-intelligence.mjs
