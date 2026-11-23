# deploy26 — 2026-11-23（node26 undici 修復 ×3 + Mermaid 渲染修復 ×2 + Prod Spec schema 調整）

來源：tPAAW dev `deba0839a`（orphan commit，只含 `packages/**`，路徑 = tPAAW 相對路徑）

## 這一包是什麼（今天修的）

1. **🔴 LLM 全掛修復（最重要）**：homebrew node 升 26 後，global fetch（內建 undici v7）收到 npm undici(6.x) 的 Agent dispatcher 直接 `UND_ERR_INVALID_ARG` → 所有 LLM 呼叫秒死 "fetch failed"（spec-fill / DFO / EM 全掛）。修：dispatcher 與 fetch 統一用同一份 npm undici（llm-utils / cu-background-watcher / coding.mjs 背景自呼 / auto-dispatch-manager A2A 四處）。**公司 node 24 不受影響也不需要動** — 修法版本無關
2. **spec 誤判失敗修復**：spec-fill 跑 8 節 >5 分鐘，外層 localhost fetch 被 undici 預設 300s headersTimeout 掐死（內層其實成功、PROJECT.md 已寫完卻顯示錯誤）。修：外層 fetch 關 timeout
3. **resp.json() 掛死修復**：headers 回來後 timeout 已停 + bodyTimeout:0 → LLM body 停滯時永遠 pending（release-info ai-write 卡 20 分鐘無反應）。修：body 讀取納入 timeout 窗（readBodyJson 模式），逾時照常 retry
4. **Mermaid 渲染失敗 ×2**：
   - participant 撞保留字（如 `Loop`）→ mermaid 當 `loop` keyword → parse error。修：render 失敗時品修版自動改名重試（只動 actor 位置，訊息文字不動）
   - 同頁某張圖失敗後錯誤清理把全頁 mermaid 暫存 div 掃掉 → 隔壁正常圖跟著掛（getAttribute null）。修：清理只掃自己那塊
5. **Prod Spec schema 調整**：
   - FR 條目順序改 **Requirement > Business Rule > Acceptance Criteria**（BR 有才寫、沒有整塊省略不硬造）
   - 第 5-8 節（架構/技術/測試/維運）大幅簡化 — 只寫策略方向幾條短句，不展開細節；**第 2 節 FR 是重點**

## 覆蓋方式

1. 依路徑覆蓋 `packages/**`（本次 **442 檔**，全量包；只想蓋差異見下方清單）
2. **無依賴變更**（package.json / package-lock 沒動）— 不必 `npm install`

## 差異清單（vs deploy25，7 檔全改）

```
M  packages/server/src/lib/auto-dispatch-manager.mjs
M  packages/server/src/lib/cu-background-watcher.mjs
M  packages/server/src/lib/llm-utils.mjs
M  packages/server/src/lib/project-md.mjs
M  packages/server/src/lib/release-docs.mjs
M  packages/server/src/routes/coding.mjs
M  packages/ui/src/components/MermaidBlock.tsx
```

## 公司更新步驟

1. 覆蓋上列 7 檔（或整包 packages/ 蓋掉）
2. `npm run build`
3. 重啟 server（`node src/paaw-server.mjs`）

## 測試證據

- 94 test files / 1808 tests 全綠（dev 樹 + fresh clone `npm ci` 雙邊驗證，fresh clone 1802 pass + 6 skipped 環境差異）
- fresh clone `npm run build` ✓ + 開機 boot 200 ✓
- 實測：learning-space RU 三份文件（Prod Spec 8 節 0 佔位 / DFO / Release Info）重建全 done、verify ok:true；mermaid 保留字改名後 headless Chrome 實測 PARSE OK
