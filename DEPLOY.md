# deploy17 — 來源 tPAAW dev `6200beed`（2026-11-09 21:10）

> 全量 packages/ 430 檔（tree 驗證 = tPAAW origin/dev 完全一致）。
> 內容 = deploy16 tip（`ece1899`）+ 5 commits：派工精靈關窗回 EM chat、CU 視窗真正做完才關（訂閱板）、UI 去 v2 字眼、CU 步驟改名「API Map 前置處理（API-first）」、死碼清理。
> **無依賴變更（不用 npm install）。**

## 本次主題

1. **派工精靈（🚀 DispatchWizard）**：按下派工 → 立刻關精靈、回到 EM chat；執行進度與結果直接顯示在 EM chat 訊息流（含 review 委員會審查進度）。
2. **CU 視窗不再提早關**：同專案已有 CU 在跑時，新請求改「訂閱」既有 run 的即時進度，主 run 真正完成才回報 complete。
3. **不再出現 Feature Map 字眼**：CU 步驟一律叫「🗺️ API Map 前置處理（API-first）」。
4. **死碼清理**：刪 5 個無人 import 的組件 + `FeatureMap.tsx` 正名 `ApiMapView.tsx`。
5. **TS Guide 拿掉 draft? 狀態**：AI 產的條目直接是成品，badge 只剩人工條目 ✍️（Fleming：AI 產的總比人產的好）。

## 修改檔（12 改 + 1 改名，已裝 deploy16 者蓋這 13 檔）

| 檔案 | 變更 |
|---|---|
| `packages/server/src/routes/coding.mjs` | ai-initial 同 root 併發訂閱板（_cuBulkRuns）；CU 步驟名「API Map 前置處理（API-first）」；step summary 修 [object Object] |
| `packages/server/src/routes/ai-settings.mjs` | Coding 分類描述/prompt 標籤同步去 Feature Map |
| `packages/server/src/routes/coding-trouble.mjs` | TS Guide 條目 status 拿掉 draft（ai-draft→ai） |
| `packages/ui/src/components/TroubleshootingPanel.tsx` | badge 只剩人工 ✍️；刪 confirmTsg 死碼 |
| `packages/ui/src/i18n/locales/{zh,en,ja,zh-mix}.json` | 清 tsg.draft/confirm keys |
| `packages/ui/src/components/DispatchWizard.tsx` | onLaunch — 按派工即關窗，執行交父層 |
| `packages/ui/src/components/EMDashboard.tsx` | launchDispatchJob — 派工進度/結果寫進 EM chat；CU_STEPS 改名 |
| `packages/ui/src/components/CrewManager.tsx` | Project Info tool 描述「專案資訊、API Map」 |
| `packages/ui/src/pages/CodingIDE.tsx` | CU 步驟清單補齊 server 8 步；import ApiMapView新路徑 |
| `packages/ui/src/pages/coding-ide-helpers.tsx` | import ApiMapView 新路徑 |
| `packages/ui/src/components/ApiMapView.tsx` | ⭐ 新檔（= 舊 FeatureMap.tsx 改名，內容 100% 相同） |

## ⚠️ 手動刪除（公司端要刪，不然留死檔）

```
packages/ui/src/components/AgentBuilder.tsx
packages/ui/src/components/CrewAvatar.tsx
packages/ui/src/components/FeatureCockpit.tsx
packages/ui/src/components/ReleaseRequests.tsx
packages/ui/src/components/RuQaSection.tsx
packages/ui/src/components/FeatureMap.tsx   ← 已改名為 ApiMapView.tsx
```

## 公司更新 SOP

1. 蓋上表 13 檔 + 刪除下面 6 個舊檔（或全量清空 packages/ui/src/components 後覆蓋本 branch 的 packages/）
2. `npm run build`
3. 重啟 server

## 驗證紀錄（Mac mini，2026-11-09）

- tsc 0 error；vite build 過；全套 93 檔 1795 tests 全綠
- paaw-server 重啟後 UI 200 / API 200
- 兩個併發 ai-initial → 第二個正確訂閱第一個（replay + 即時轉發），無雙跑
- 真實全套 CU 跑完：48/48 items、cu-status summary「128 features」乾淨
