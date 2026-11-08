# deploy15 — 2026-11-08（v13 · 第一版 release）

**來源：** tPAAW dev `fb8f5b43`（packages/ 全量 435 檔，路徑 = tPAAW 相對路徑）
**上一版：** deploy15 v12（dev `dcf5fe17`）

## 依賴

**無依賴變更（不用 npm install）** — 直接蓋檔 → `npm run build` → 重啟 server。

## Delta（v12 → v13：僅 1 檔）

```
M  packages/ui/src/components/EMDashboard.tsx
```

**內容：** EM 大總管 header 的「查看注入 Context & Prompts」按鈕 🧠 跟 Code Understanding 撞 emoji → 改 👁️。
（已裝 v12 者只需蓋這 1 檔 + rebuild。）

## 公司更新 SOP

1. 蓋 `packages/ui/src/components/EMDashboard.tsx`（或整個 packages/ 覆蓋 = 435 檔）
2. `npm run build`
3. 重啟 server
4. 驗證：EM 頁 header = 🚀 派工 / 🧠 CU / ⚙️ 設定 / 📋 歷史 / 👁️ Context / 📄 報告（emoji 各唯一）

## 排除（照舊政策）

`.paaw/`（本機 runtime + agent override）、`data/semgrep-rules/golang/`、個人 runtime data
