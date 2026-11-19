# deploy23 — 2026-11-19（coding app side chat 全站化 + Mermaid 修補 + Prod Spec 維護提示 + 派工報告綁 RU）

**來源：** tPAAW dev `f9a513e4`（orphan branch，路徑 = tPAAW 相對路徑）
**前一版：** deploy22（來源 dev `288e21f6`）
**內容：** packages/ 全量 433 檔（= deploy22 433 檔 + 13 改 / 0 新 / 0 刪）

## 本次主題（9 commits，全部 UI/server 原始碼）

1. **💬 side chat 顯示/隱藏開關全站化**（跟 Prod Spec 同款）：RM / Handover / Troubleshooting / Code Intel / Tests / API Tester（Developer AI，按鈕在 request tab bar 尾端）；CSS 隱藏不卸載 → 收起再開保留捲動/對話狀態，不會滾到底
2. **Mermaid 渲染自動修補**：parse error 時自動把未加引號的 `|label|` / `[text]` 加引號重試（AI 產的 spec 圖含 `{app_name}` 等特殊字元不再掛掉）
3. **Prod Spec 維護提示**：架構師（林曉薇）/ Developer rolePrompt 加「動到程式主動提醒用 spec_write 同步 Prod Spec」
4. **side chat 預設寬統一 520px**：Tests（原 340）、Prod Spec（原 460）對齊其他頁
5. **API tester request tab bar 捲軸移除**（水平+垂直）
6. **派工報告跟著 release unit 走**：EM 報告 header 標明 Release Unit；`/start` route 吃 body.cwd fallback（沒 ?path 不再靜靜落 PAAW root）

## 變更檔案（13 改 — 已裝 deploy22 者蓋這 13 檔 + npm run build 即可）

```
M packages/modules/coding/crews/coding.architect.json
M packages/modules/coding/crews/coding.developer.json
M packages/server/src/lib/auto-dispatch-manager.mjs
M packages/server/src/routes/coding-auto-dispatch.mjs
M packages/ui/src/components/ApiRequestTabSheet.tsx
M packages/ui/src/components/CodeIntelPage.tsx
M packages/ui/src/components/HandoverPanel.tsx
M packages/ui/src/components/MermaidBlock.tsx
M packages/ui/src/components/ProjectMdPage.tsx
M packages/ui/src/components/ReleaseManagerPanel.tsx
M packages/ui/src/components/TestsPage.tsx
M packages/ui/src/components/TroubleshootingPanel.tsx
M packages/ui/src/pages/CodingIDE.tsx
```

## 依賴變更

無（不用 npm install）。

## SOP（沒裝過的人）

抓本 branch 全部 packages/ 檔案照路徑蓋 → `npm install` → `npm run build` → 重啟 server。
（crew json 屬 packages/modules，蓋完重啟即生效）

## 測試

tPAAW dev `f9a513e4`：94 test files / 1805 tests 全綠（含 security-kernel-matrix）；`npm run build` ✓；paaw-server 本機重啟冒煙 ✓。
