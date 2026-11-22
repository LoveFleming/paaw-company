# deploy25 — 2026-11-22（Evidence 實跑數字 + CU 分流 + 差異摘要 + 多語言偵測）

來源：tPAAW dev `87876ad1f`（orphan commit，只含 `packages/**`，路徑 = tPAAW 相對路徑）

## 這一包是什麼

1. **Evidence Matrix 實跑測試數字**：🧪 badge 顯示最近真跑 pass/fail、📈 顯示覆蓋率（沒跑過顯示 `—`，不猜）；「製作 Release Info」流程第一步自動真跑測試
2. **Release Info 簡化**：拿掉版本下拉與 commit 顯示；每個 RR 只保留最新一份 info/matrix/diff 快照（SQLite 仍存全史供 AI 查）
3. **📊 與前一版的差異摘要（新）**：第 4 份文件 — 有前一版 release snapshot 時自動比對（矩陣數字 delta、API 新增/移除、git diff 統計）→ AI 條列摘要放在 Release Info 卡最前面；第一版自動 skip
4. **CU 修復與分流**：staleness 改認 coding.db v2 產出（不再永遠 stale）；全新 RU import 跳 CU 引導、有 `.paaw` 的交背景排程（首次 CU 每輪限量 1 個 RU）
5. **test runner 決定性偵測補全**：Rust（cargo test + 摘要解析）、Maven/Gradle 並存二選一（有 gradlew 優先）、自訂 `test:*` script（jest/vitest）、`test:ui` 等互動模式排除
6. **DFO JSON 解析自動 retry**（最多 3 次才 500）；confirm dialog 自訂化（不再用瀏覽器 window.confirm）

## 覆蓋方式

1. 依路徑覆蓋 `packages/**`（本次 **442 檔**，全量包；只想蓋差異見下方清單）
2. **無依賴變更**（package.json / package-lock 沒動）— 不必 `npm install`
3. `npm run build`
4. 重啟 server

## 相對 deploy24 的差異（只蓋這些也可以，16 檔）

- 新增 1：`packages/server/src/lib/release-diff.mjs`（差異摘要生成）
- 修改 15：ReleaseManagerPanel / RuOnboardingModal / CodingIDE、coding.mjs、coding-dfo.mjs、coding-releases.mjs、release-docs.mjs、release-info-builder.mjs、evidence-by-api.mjs、test-runner.mjs、cu-background-watcher.mjs、4 個 locale
- ⚠️ 另有 3 個 `packages/server/.paaw/` 檔（changelog/decisions/project）本包**刻意不含**；若公司端有殘留同名檔可手動刪除（本機 runtime 檔，不該出貨）

## 驗收重點

1. Release Manager → 製作 Release Info → 🧪 出現實跑數字、第一版沒有 📊 差異摘要區塊（正常）
2. 有兩個以上 RR 出過 release 的專案 → Release Info 卡最上方有「📊 與前一版的差異摘要」
3. portkey-gateway（g6）測試偵測 → 2 組 jest（src/ + plugins/）
4. Import 新 RU（無 `.paaw`）→ 跳 3 步 onboarding 含 CU；舊 RU → 2 步

完整清單：GitHub 上本 branch 對 deploy24 的 compare。

## 追加（同日晚間）

- 未 close 的 RR 顯示 draft（不顯預估版號 v18，避免誤解）；info 快照內加 status: draft 欄位；差異摘要標頭去版號
