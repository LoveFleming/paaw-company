# 0907 更新包 — 2026-09-07 全天變更

**範圍：** tPAAW dev `b25f42b4`（9/6 20:57）→ `a2960817`（9/7 21:45），共 9 commits。
**用法：** 目錄內路徑 = PAAW repo 相對路徑，`0907/` 底下每一檔下載後放到公司 PAAW 的同一相對路徑覆蓋。共 **42 檔全部要覆蓋**。

> 若公司還沒做過 9/4-9/6 大包（repo 根目錄那 276 檔），要先做那包再做這包。

---

## 覆蓋清單（42 檔）

### ① Linux 放大 icon 修復（16:27，`72431dc5`）
```
packages/ui/src/pages/CodingIDE.tsx        ← 專注模式按鈕（主要回報）
packages/ui/src/pages/BriefingPlayer.tsx   ← 簡報全螢幕
packages/ui/src/pages/AppBuilder.tsx       ← 預覽全螢幕
packages/ui/src/pages/MindMapViewer.tsx    ← 心智圖符合視窗
```
`⛶`/`🗗` 冷門 Unicode 在 Linux 顯示豆腐框 → 改 inline SVG。

### ② 打包管線 v1.0.0（18:25–18:53，`9ea89ed3`/`bdc2267b`/`1c67a893`/`45bb9103`）
```
scripts/pack.mjs                ← git-free 掃目錄 + self-check + zip 內嵌 manifest
scripts/seed/（29 檔整個目錄）  ← 出廠骨架（個人快照已移除）
package.json                    ← 版號 1.0.0
.gitignore                      ← + data/**/.paaw/ 防護
```

### ③ Security Scan 移除派工按鈕（20:28，`72fea970`）
```
packages/ui/src/components/SecurityTab.tsx
packages/ui/src/pages/CodingIDE.tsx        ← 與 ① 同檔，覆蓋即為最新版
```

### ④ EM 派工 / agent 工具鏈修復（21:26–21:45，`b4648a63`/`8064ffc6`/`a2960817`）
```
packages/server/src/lib/auto-dispatch-manager.mjs   ← EM memory 注入參數對調修復 + 長輸出落地
packages/server/src/lib/paaw-agent-loop.mjs         ← action log / memory 修復
packages/server/src/routes/coding-auto-dispatch.mjs
packages/server/src/routes/coding.mjs               ← dispatch_agent/task_* 用呼叫者專案路徑
packages/server/src/tools/index.mjs                 ← _callerPath 注入
```
派工 cwd 不再拿 workspaces[0]、agent memory 能注入、超長輸出落地 `.paaw/coding-memory/dispatch-outputs/`。

---

## ⚠️ 手動刪除（公司機器有這些就刪 — 已從程式碼移除的 junk）

```
data/skills/physical-skill/translate/.paaw/          ← 整個目錄（開發 session）
data/apps/sdlc-architect/.paaw/                      ← 整個目錄（開發 session）
data/skills/building/translate/data/                 ← 整個目錄（路徑 bug 巢狀鏡像產物）
data/apps/pocket/app.html.bak
scripts/seed/config/backup.json                      ← 個人備份快照
scripts/seed/config/agentic-bindings.json            ← 個人綁定快照
```

## 在公司打 v1.0.0 zip（覆蓋完成後）

```bash
which rsync zip || sudo apt install rsync zip   # 前置檢查
node scripts/pack.mjs                            # 產出 dist/paaw-1.0.0.zip + manifest
```
seeded 應為 ~2196 檔、self-check ✓。

## 覆蓋後必做

1. **重啟 server**（5 個 server 檔案有變）
2. 瀏覽器 **hard refresh**（6 個 UI 檔案有變）
3. 不用 npm install（無新 dependency）
