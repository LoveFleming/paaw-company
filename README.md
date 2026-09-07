# PAAW Company Update Package

手動更新包 — 給公司端一個檔案一個檔案下載覆蓋用。
所有路徑 = **PAAW repo（tPAAW）相對路徑**，下載後放到公司 PAAW 的同一個相對路徑覆蓋即可。

> **📅 最新： [`0907/`](0907/) 目錄 = 2026-09-07 全天更新（42 檔 + 覆蓋說明）— 還沒做 9/4-9/6 大包的話先做根目錄那包（276 檔）。**

- **來源範圍：** `2e5b5b76`（9/4 20:19）→ `b25f42b4`（9/6 20:57），共 86 commits（dev branch）
- **已測：** typecheck ✓ / UI build ✓ / 50 route 模組載入 ✓ / API smoke 15 項全過 ✓ / janitor 冪等 ✓
- **不用 npm install**（無新 dependency）

---

## 檔案統計（共 107 檔）

| 分類 | 數量 | 說明 |
|------|------|------|
| A. `packages/` 核心程式 | 89 | server 55 + UI 34 — **全部要覆蓋** |
| B. `data/` 設定檔 | 15 | crews ×10、notebooks ×1、CU prompts ×4 — **全部要覆蓋** |
| ROOT | 3 | `package.json`（+start/typecheck scripts）、`.gitignore`、coding spec 文件 |

> 🗑️ `data/semgrep-rules/golang/`（169 檔）已於 2026-09-08 移除 — 公司端不需要從這邊下載。

> `data/skills/physical-skill/`（399 新技能 + 5 個更新 SKILL.md）已移除 — 公司端已有，不重複放。若需要那 5 個更新的 SKILL.md（code-documenter / code-reviewer / debugging-wizard / sre-engineer / test-master），另外跟管家要。

完整清單見 **MANIFEST.txt**（可照著逐一打勾）。

## 🔧 Hotfix 2026-09-07（`72431dc5`）— Linux 放大 icon 修復

Linux 預設字型缺 `⛶`/`🗗` 符號，放大/全螢幕 icon 顯示豆腐框。改用 inline SVG（不依賴系統字型，三平台一致）。

**此批要覆蓋的 4 檔：**

```
M  packages/ui/src/pages/CodingIDE.tsx      ← coding app 專注模式按鈕（主要回報）
M  packages/ui/src/pages/BriefingPlayer.tsx ← 簡報全螢幕按鈕（同地雷順手修）
M  packages/ui/src/pages/AppBuilder.tsx     ← 預覽全螢幕按鈕（同地雷順手修）
M  packages/ui/src/pages/MindMapViewer.tsx  ← 心智圖「符合視窗」按鈕（同地雷順手修）
```

> 註：BriefingPlayer / AppBuilder / MindMapViewer 這 3 檔不在 9/4-9/6 更新包內（該期間沒改過），公司端的版本直接覆蓋即可。覆蓋後瀏覽器 hard refresh。

## 📦 2026-09-07 晚 — 打包管線（v1.0.0，tPAAW `1c67a893`）

在公司 Linux 打 `node scripts/pack.mjs` 產 zip 需同步的檔案：

**覆蓋：**
```
M  scripts/pack.mjs          ← 打包 script（git-free 掃目錄 + self-check）
M  package.json              ← 1.0.0（已同步）
M  .gitignore                ← 已同步
A  scripts/seed/（29 檔整個目錄）← 出廠骨架（backup.json/agentic-bindings.json 已移除）
```

**手動刪除：**
```
scripts/seed/config/backup.json
scripts/seed/config/agentic-bindings.json
data/skills/physical-skill/translate/.paaw/（整個目錄）
data/apps/sdlc-architect/.paaw/（整個目錄）
data/skills/building/translate/data/（整個目錄，路徑 bug 產物）
data/apps/pocket/app.html.bak
```

**執行前檢查（Linux）：** `which rsync zip`（沒有就 `sudo apt install rsync zip`）→ `node scripts/pack.mjs` → 產出 `dist/paaw-1.0.0.zip` + manifest。seeded 應爲 ~2196 檔、self-check ✓。

## 下載方式

GitHub 網頁點開檔案 → 右上 **Raw** → 另存新檔；或直接用 raw URL：

```
https://raw.githubusercontent.com/LoveFleming/paaw-company/main/<相對路徑>
```

例：`packages/server/src/routes/crew.mjs`

## ⚠️ 公司端要手動刪除（7 檔 — 這些已從程式碼移除）

```
packages/ui/src/components/StandardsEditor.tsx
packages/ui/tsconfig.tsbuildinfo
data/templates/standards/git-commit.md
data/templates/standards/naming.md
data/templates/standards/react.md
data/templates/standards/typescript.md
.DS_Store
```

## ⚠️ 不要覆蓋

- `data/config/backup.json` — 本機備份時間戳，覆蓋會洗掉公司機器的紀錄
- `.paaw/*` — 各機器自己的 runtime 產物，會自己長
- `log/`、`node_modules/` — 機器本地目錄

## 更新後必做

1. **重啟 server**（log/ 新架構 + route 修復都要重啟才生效）
2. 第一次進 Terminal tab → 🧹 清理 → 按一次「立即清掃」
3. 瀏覽器 hard refresh（UI 有新 component）
4. `log/` 目錄不用手動建 — 開機自動建立
