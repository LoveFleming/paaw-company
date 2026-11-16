# deploy20 — 來源 tPAAW dev `a45c5f58`（2026-11-16 23:20）

> 全量 packages/ 433 檔。**無依賴變更（不用 npm install）。**
> 內容 = deploy19（`2a4f161b`）+ Prod Spec 大改（詳下）。
> 已裝 deploy19 者：**刪 4 檔 + 蓋 15 檔 + `npm run build` + 重啟** 即可。

## 本次主題 — 📄 Prod Spec 頁（大改方向：原 Product Spec 功能整個重做）

1. **舊 Product Spec 功能移除**：SpecPanel 三欄頁、spec agent（Alex Werner）、`product-spec.mjs`、`/api/coding-project/spec` 系列、CU `product-spec` step、`.paaw/spec/` 目錄 — 全部刪除。
2. **新「Prod Spec」頁（原 PROJECT.md tab 改名）**，跟 PAAW 的 project 概念解耦：
   - 上方 **8 個 section tab**（Overview / 功能需求 / 非功能需求 / API Contract / 架構設計 / 技術設計 / 測試策略 / 維運觀測；綠點 = 有內容）
   - **頁面唯讀**：render 好的 markdown + **mermaid 圖**（sequenceDiagram 循序圖 / flowchart 流程圖直接渲染）
   - 右側 **林曉薇 side chat（= coding.architect 架構師本體，a2a 直連，帶她的 crew 工具）**，ColResizer splitter（同 ReleaseManager 版面）
3. **寫入全靠 AI**：討論收斂後林曉薇用新受控工具 **`spec_write`**（section 1-8 整節覆寫，只能寫 `.paaw/project/PROJECT.md` 的 spec 區，碰不到其他檔案）→ UI 按 🔄 看最新。
4. **PROJECT.md schema 變更**：只剩 Product Spec 一區（User Remarks、AI Overview 都移除）。
   - ⚠️ **舊檔的 User Remarks 內容會在第一次寫入後消失** — 公司 project 的 PROJECT.md 如有重要手寫內容，更新前先備份一份！
5. import 專案首次 CU 收尾：spec 空且專案有 code → 自動生 8 節初稿（新專案不生）。

## 測試（2026-11-16 晚，全綠）

- 全套 **93 檔 / 1795 tests 通過**（i18n 一致性測試抓到一個空值已修）
- lib 級 11 項：v2 舊檔遷移 / 單節寫入不影響他節 / placeholder 不誤判 / no-code 防護 / 壞 index 擋
- E2E 實測：clone open source 專案（anime.js）import → 起獨立 server（:4399）→ API 讀寫 → **真實 a2a agent loop 呼叫 spec_write 寫入 section 2**，其他節 + mermaid 完整保留

## 檔案清單（vs deploy19：15 改 + 4 刪）

### 修改（15）
| 檔案 | 內容 |
|---|---|
| `packages/modules/coding/crews/coding.architect.json` | 林曉薇 rolePrompt 加 Prod Spec side chat 模式（8 節 schema、圖優先、spec_write 流程） |
| `packages/server/src/lib/paaw-agent-exec.mjs` | `spec_write` 工具 handler（受控：只寫 PROJECT.md spec 單節） |
| `packages/server/src/lib/paaw-agent-tools.mjs` | `spec_write` 工具定義（project-edit group） |
| `packages/server/src/lib/project-md.mjs` | PROJECT.md 新 schema：只剩 spec 區；split/join 8 節；spec 初稿生成；單節寫入 |
| `packages/server/src/routes/coding.mjs` | project-md API（GET specSections / POST specSections / spec-draft / spec-section） |
| `packages/server/src/lib/domain-agent-registry.mjs` | 移除舊 spec agent 註冊 |
| `packages/ui/src/components/ProjectMdPage.tsx` | 全新：8-tab 唯讀渲染 + 林曉薇 side chat + splitter |
| `packages/ui/src/components/AgentSideChat.tsx` | paawChat 支援 systemPrompt 覆寫 |
| `packages/ui/src/components/EMDashboard.tsx` | 移除 product-spec CU 卡片 |
| `packages/ui/src/pages/CodingIDE.tsx` | tab 改名 Prod Spec |
| `packages/ui/src/pages/coding-ide-helpers.tsx` | 同步 |
| `packages/ui/src/i18n/locales/{zh,en,ja,zh-mix}.json` | Prod Spec keys（移除舊 spec.* / userRemarks / aiOverview 系列） |

### 刪除（4）
- `packages/modules/coding/crews/coding.spec.json`（Alex Werner agent）
- `packages/server/src/lib/product-spec.mjs`
- `packages/server/src/prompts/code-understanding/gen-product-spec.md`
- `packages/ui/src/components/SpecPanel.tsx`

## 公司更新步驟

1. 刪除上表 4 個刪除檔
2. 蓋上 15 個修改檔（packages/ 全量包的話直接整包覆蓋即可）
3. `npm run build` → 重啟 server
4. ⚠️ 先備份既有專案的 `.paaw/project/PROJECT.md`（舊 User Remarks 內容會被新 schema 淘汰）
