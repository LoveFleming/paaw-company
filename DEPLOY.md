# deploy24 — 2026-11-21（Release Manager / DFO / Prod Spec 文件體系大改）

來源：tPAAW dev `a35fdba56`（orphan commit，只含 `packages/**`，路徑 = tPAAW 相對路徑）

## 這一包是什麼

1. **Release Manager v5–v7**：單一 Release 卡；features 數量由程式依 API Map 決定、AI 只負責命名與 1–2 句白話說明；Release Info 寫進 SQLite；Evidence Matrix 併入 Release Info 卡並隨版本切換；文件狀態表格化
2. **DFO 改版**：五節 → 四節（移除「回滾與降級」）；版本代號對準 Release Request（首次 `initial`、之後 RR 單號；同代號只留一版，RR 關閉即凍結不可改）；C4 model 與 error codes 全文餵入並要求寫進文件（錯誤碼 byFeature 全列）
3. **Prod Spec 版本化**：`.paaw/spec/versions/{代號}.json` 快照，Prod Spec 頁與 Release Info 都有版本下拉（預設最新，沒有就顯示沒有）
4. **文件自動保鮮（新）**：CU 背景巡檢每輪比對 git HEAD 與文件記錄的 `codeCommit` — code 有動就自動重生 DFO；spec 預設 7 天一次（環境變數可調）；人不用介入
5. **AI 寫文件參考 task/ADR**：新增 `doc-evidence.mjs`，三個生成器（Spec / DFO / Release Info）都吃 TASKS、ADR/技術決策、CHANGELOG、KNOWN ISSUES、人的備註（全文不切字數）
6. **side chat 人機協作**：Greta（DFO）與曉薇（spec）rolePrompt 要求先讀 tasks/ADR/changelog；人講的現場事實優先並用 `dfo_write` / `spec_write` 寫入；架構決策補 ADR；主動指出過時章節
7. **Mermaid 字型統一**：圖內文字改用正文 system-ui 字族、字級 inherit（循序圖含 actor/message/note）

## 覆蓋方式

1. 依路徑覆蓋 `packages/**`（本次 **441 檔**，全量包；只想蓋差異見下方清單）
2. **無依賴變更**（package.json / package-lock 沒動）— 不必 `npm install`
3. `npm run build`
4. 重啟 server

## 相對 deploy23 的差異（只蓋這些也可以）

- 新增 12：`packages/server/src/lib/{dfo.mjs,doc-evidence.mjs,doc-autorefresh.mjs,cu-background-watcher.mjs,release-docs.mjs,release-info-builder.mjs,evidence-by-api.mjs,scan-skeleton.mjs,coding-db/release-info.repo.mjs}`、`packages/server/src/routes/coding-dfo.mjs`、`packages/server/src/prompts/code-understanding/gen-dfo.md`、`packages/ui/src/components/DfoPanel.tsx`
- 修改 30（Release Manager / Prod Spec / CodingIDE / 4 個 locale / coding.ops / coding.architect / coding-releases route / mermaid 等）
- 刪除 1：`packages/ui/src/components/TroubleshootingPanel.tsx`（頁面已移除）

完整清單：GitHub 上本 branch 對 deploy23 的 compare。

## 驗收重點（明天公司測）

- [ ] Release Manager：建 RR → 🚀 製作 Release Info → 文件狀態表格 → features 表格（15 個功能、1–2 句說明）→ Evidence Matrix 同版號
- [ ] Release Info / Prod Spec 版本下拉：預設最新；切舊版唯讀；沒有版本時顯示「沒有」不報錯
- [ ] DFO：四節、版本代號 `initial`、同一代號不會出現 v2/v3 多版；RR 關閉後 🔒 凍結、AI 更新 disabled
- [ ] DFO 內含 C4 容器/對外連線表 + 錯誤碼對照（byFeature 全列）
- [ ] side chat：問 Greta 某功能在幹嘛 → 她會實際開檔引用檔案/函式；人補充經驗 → `dfo_write` 寫入
- [ ] 自動保鮮：改一個 code commit 後，4 小時內（或重啟後首輪）DFO 自動更新；console 找 `[doc-auto]` / `[cu-watch]` log
- [ ] 環境變數（可選）：`DOC_AUTO_WATCH=0` 關自動保鮮、`SPEC_AUTO_DAYS=0` 關 spec 自動、`DOC_AUTO_MAX_PER_SWEEP=2` 每輪上限、`CU_WATCH_INTERVAL_MS` 巡檢間隔

## 注意

- 自動保鮮會叫 LLM（DFO 每次約 30–60 秒、spec 8 節約 3 分鐘），公司若有限流可先設 `DOC_AUTO_WATCH=0`
- `.paaw` 一律不帶（本包已排除 `packages/server/.paaw/**`）
