# DEPLOY11 — 2026-11-02 (v4, 最新 ✅)

- 來源：tPAAW dev `2502fbe7`
- 內容：packages/ 全量快照（路徑 = tPAAW 相對路徑）

## v4 關鍵修復（vs v3）— CU bulk 全綠
- 🔴 `_extractJsonArray` / `backfillApiTests` 原本宣告在 ai-initial-step 單步 route 的 block 內（block-scoped）→ bulk ai-initial 路徑 ReferenceError「X is not defined」（api-map/ui-map/test-intelligence 全炸）。已搬 module top-level
- ui-map 空結果判定剝 code fence（LLM 回 ```json\n[]\n``` 也正確標 done）
- 驗證：guardrail-hub 全量 CU force rerun，8 steps 全 done

## 先前修復（v1-v3）
- coding-db migrate 容忍舊 db 既有表、cu-status done 清殘留 error、const 重賦值 4 處

## Delta vs deploy10：18 新增 + 70 修改，0 刪除
完整清單：https://github.com/LoveFleming/tPAAW/compare/b7fdbdaa...2502fbe7

## 相依性
無依賴變更 → 不用 npm install。UI 有改 → npm run build。蓋完**一定要重啟 server**。

## 更新方式
已裝 deploy10：蓋 delta 檔 → 重啟 → npm run build。
全新：git clone -b deploy11 → npm install → npm run build → node packages/server/src/paaw-server.mjs
