# pack branch — PAAW 打包工具（scripts/）

> 純工具包：只有 `scripts/` 目錄，對應 tPAAW repo 根目錄的相對路徑。
> 來源：tPAAW dev（2026-09-20 驗證可打包），pack.mjs 最新版（含 Windows bsdtar 支援 + 防禦性清理）。

## 內容

- `scripts/pack.mjs` — 打包主工具（build UI → stage → data-seed 播種 → self-check → zip + manifest）
- `scripts/seed/` — data-seed 骨架（出廠空樣板，無個人資料、無 API key）
- `scripts/postinstall.cjs` — package.json postinstall hook（Linux emoji 字體，mac/win no-op）
- `scripts/dev-server.mjs` / `paaw-sync(.mjs)` / `runtime-guard-scanner.mjs` — 開發輔助工具（package.json 有引用，需一起在）

## 公司使用 SOP（Windows）

1. 下載本 branch（Code → Download ZIP，或逐檔下載）
2. 把 `scripts/` 整個目錄覆蓋到公司 tPAAW 根目錄（`tPAAW\scripts\`）
3. 先建 UI：`npm run build`
4. 打包：`node scripts\pack.mjs --skip-build`（UI 已建時）或 `node scripts\pack.mjs`（連 build 一起）

產出在 `dist\paaw-<version>.zip` + `dist\paaw-<version>.manifest.json`（含 sha256）。

## 前置需求（缺一會被 self-check 擋下）

- `package.json`（版本號 x.y.z）
- `packages/ui/dist/index.html`（npm run build 產物）
- `data/` 產品資產：`prompts/ semgrep-rules/ skills/ apps/ workflows/ ai-settings/ crews/ config/`
  （公司更新包已涵蓋；pack.mjs 直接掃目錄，不依賴 git）

## 安全保證（self-check 自動擋）

- 不出貨：`log/`、`.paaw/`、`node_modules/`、`data/`（個人資料）、`.env*`
- data-seed 個人內容清單：backup.json / user.json / ui-state.json / distilled-memory / notes/default / knowledge 內容 / crews/conversation
- stage < 300 檔或產品資產 < 2000 檔 → 拒絕打包（防缺料靜默出貨）

## 驗證紀錄（2026-09-20 Mac mini 實跑）

- stage 374 檔 ✓ / 產品資產 overlay 2196 檔 ✓ / self-check ✓
- 產出 paaw-1.0.0.zip 32.3 MB，zip 內含 paaw-manifest.json + scripts/ 完整 6 檔 + data-seed
