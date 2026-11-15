# deploy19 — 來源 tPAAW dev `2a4f161b`（2026-11-15 20:55）

> 全量 packages/ 434 檔（tree 驗證 = tPAAW origin/dev 完全一致）。
> 內容 = deploy17（`6200beed`，2026-11-09）+ 6 commits。
> **無依賴變更（不用 npm install）。**

## 本次主題

1. **📄 Product Spec（AI-Native Product Development Specification）**：8-section 規格（Overview / Functional / NFR / API Contract / Architecture / Technical / Testing / Operations）。
   - CU 新增 `product-spec` step：既有專案第一次跑只為「空 section」從 code 反向生成初稿（evidence 優先：api-map / feature-map / test-intelligence）；既有內容一律不碰、不自動同步；新專案（<5 source 檔）不跑。
   - Coding IDE 新分頁「📄 Product Spec」：左 section 清單（狀態 ⚪空→📝draft→👀review→✅final）/ 中 markdown 編輯 / 右架構師 side chat（agent `spec` = Alex Werner），共識後 `spec_write` 受控工具寫入 `.paaw/spec/`（自動備份 .bak）。
2. **CU modal 改版**：方形卡片、左機械層 / 右智慧層分欄、無捲軸，一目了然；skill 綁定縮成 🧩 徽章。
3. **Model 偏好頁完整化**：顯示 `user.json` 實際存在的**所有** model 偏好（原本只硬編碼 4 格 — sideChat.* / notes-ai / autoDispatchFallback 等全看不到，model key 失效時無從發現）；偏好指向已不存在的 provider/model → ⚠️ 已失效警示，選「（使用預設）」清除。**401 排查就用這頁**。
4. **mermaid parse-error 汙染修復**：mermaid 12.2.0 渲染失敗時錯誤 div 殘留 document.body → coding app 頁面最外層多捲軸、拉到底見「Syntax error…parse error in line N」怪文字。修：`suppressErrorRendering` + 渲染前後掃除殘留。
5. **API tester 切 tab 保狀態**：常駐掛載、切走只 hidden 不 unmount。
6. 測試同步：93 檔 / 1805 tests 全綠。

## 修改檔（13 改 + 4 新，已裝 deploy17 者蓋這 17 檔即可）

### 新增（4）

| 檔案 | 內容 |
|---|---|
| `packages/modules/coding/crews/coding.spec.json` | Spec 架構師 agent（Alex Werner） |
| `packages/server/src/lib/product-spec.mjs` | 8-section spec 讀寫 / CU 初稿生成 lib |
| `packages/server/src/prompts/code-understanding/gen-product-spec.md` | section 初稿 prompt（evidence 優先） |
| `packages/ui/src/components/SpecPanel.tsx` | Spec 分頁 UI（三欄） |

### 修改（13）

| 檔案 | 內容 |
|---|---|
| `packages/server/src/routes/coding.mjs` | CU `product-spec` step（單步+bulk）；API `GET/POST /api/coding-project/spec*` |
| `packages/server/src/lib/domain-agent-registry.mjs` | 註冊 `spec` agent |
| `packages/server/src/lib/paaw-agent-tools.mjs` | `spec_write` 工具定義 |
| `packages/server/src/lib/paaw-agent-exec.mjs` | `spec_write` executor + group map |
| `packages/ui/src/pages/CodingIDE.tsx` | Spec 分頁接線 + API tester keep-mounted |
| `packages/ui/src/pages/coding-ide-helpers.tsx` | MainTabType + spec |
| `packages/ui/src/components/EMDashboard.tsx` | CU modal 方形卡片左右分欄；CU_STEPS + product-spec |
| `packages/ui/src/components/MermaidBlock.tsx` | mermaid 殘留 div 修復 |
| `packages/ui/src/pages/SettingsPage.tsx` | Model 偏好頁顯示全部偏好 + 失效警示 |
| `packages/ui/src/i18n/locales/zh.json` / `en.json` / `ja.json` / `zh-mix.json` | `spec.*` keys |

## 安裝（已裝 deploy17 者）

1. 蓋上 17 檔（packages/ 相對路徑）
2. `npm run build`
3. 重啟 server

## 全新安裝

clone / 全量覆蓋 packages/ 434 檔 → `npm install` → `npm run build` → 重啟。出廠 data 資產另見 `deploy18`。

## 另注意（data/ 不隨包走，公司自行檢查）

- Model 401 排查：`⚙️ Settings → 📌 Model 偏好`（新 UI）+ `data/config/providers.json`（active / defaultModel / fallbacks / visionModel 四處）
