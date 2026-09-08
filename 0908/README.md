# 0908 補包 — Provider 設定 UI 缺檔（8/23 升級沒進 9/4-9/6 大包）

**問題：** 設定 → Provider 沒辦法換預設 model。
**原因：** 8/23 的 Provider UI 大升級（tPAAW `c71ca7b0` + `6c000c49`）在 9/4-9/6 大包範圍**之前**就 commit 完了，大包只收 9/4-9/6 的變更 → 這幾個檔案一直沒進 paaw-company，公司端還是舊版 UI。

**要覆蓋的 3 檔（全部從 Mac mini dev 最新版，與 9/4-9/6 大包+0907 相容）：**

```
packages/ui/src/pages/SettingsPage.tsx      ← ⭐ 主要：Provider tab 完整版
packages/server/src/routes/assistant.mjs    ← ⭐ 必須跟 UI 一起換（見下）
packages/ui/src/pages/OnboardingPage.tsx    ← 順帶補齊（首次設定表單 8/23 版，可不換）
```

⚠️ **SettingsPage.tsx 和 assistant.mjs 一定要成對覆蓋**：新版 UI 存檔時靠新版 server 的 apiKey 保護邏輯（GET 回傳截斷 key `7573827...`，PUT 不會把截斷版寫回）。如果只換 UI 不換 server，舊 PUT 會把截斷的 key 覆蓋掉真 key → **API key 損毀**。

## 覆蓋後你就有了

- **設定 → 系統設定 → Provider tab：**
  - 「預設 Model」下拉選單 — 全域預設 model 直接換
  - 每個 model 旁的 📌 按鈕 — 一鍵設為預設
  - Model 卡片完整欄位：contextWindow / maxTokens / 輸入輸出單價
  - Fallback chain 編輯（加/刪/上下移）
  - 記得按最下面「儲存 Provider 設定」
- 換完即時生效，不用重啟 server（對話中的 session 下一次呼叫生效）

## 步驟

1. 三檔下載放到公司 PAAW 相對路徑覆蓋（UI 兩檔 + server 一檔）
2. **重啟 server**（assistant.mjs 是 server 端）
3. 瀏覽器 hard refresh
4. 設定 → 系統設定 → Provider → 「預設 Model」下拉換你要的 → 儲存
