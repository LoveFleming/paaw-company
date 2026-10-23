# FDE — 從 tPAAW 移出的 installed-apps 模組存檔（2026-10-23）

Fleming 2026-10-23 決策：installed-apps 從 PAAW（tPAAW）移出，各 app 走獨立 repo + 自己的 dev server，
之後用 PAAW coding app 的 Import Project 匯入當 Release Unit 開發。

本 branch（orphan）= **sre / pm / secret 三個模組的完整存檔**（程式 + 模組資料根），供日後 FDE 起步參考。

> learning（學習空間）不在這裡 — 它已圓滿併入獨立 repo **LoveFleming/learning-space**（含 10 月修復與 CAP 會考考卷寶庫資料）。

## 內容

```
sre/      SRE 室 v0.2.0（manifest enabled）— server 7 routes + ui pages + crews JSON
pm/       產品經理室 v0.2.0（manifest disabled）— briefing/dossiers/projects/sheets routes + ProjectOffice
secret/   秘書 v0.1.0（manifest disabled）— routes + Secretary 頁
module-data/   各模組的資料根（原 tPAAW data/installed-apps/<id>/，PAAW_ROOT shim 指向處）
  sre/    services.json 等 dossiers
  pm/     projects.json + dossiers
  secret/ categories.json + dossiers
```

## 來源與歷史

- 原位置：tPAAW `installed-apps/<id>/`（程式）+ `data/installed-apps/<id>/`（資料）
- 最後狀態：pm/secret/sre 均含 TASK-169 harden（2026-10-17）、sre 另有 TASK-105 grafana/服務面板
- 移除 commit：見 tPAAW dev「remove module platform」（2026-10-23）

## 如何起死回生（未來 FDE 開發某個 app 時）

1. 以 `LoveFleming/agentic-app-template` 起 standalone 專案
2. 把 `sre/`（或 pm/secret）的 `server/routes/*.mjs` 移植成 template 的 `server/src/routes/`
   - 依賴 `../../../../packages/server/...` 的 shim（shared.mjs / coding-security / stable-hash）要換成
     template 自帶版或 tPAAW 同名檔
3. `ui/pages/*.tsx` 移植到 template 的 `web/src/pages/`，`@paaw-ui/*` import 換成 template 對應元件
4. crews JSON（sre 的 8 個 crew）放 `server/domain-agents/` 或資料目錄
5. `module-data/<id>/` 內容 = 該 app 的資料目錄，搬進新專案 data/
6. PAAW coding app → Import Project → 當 RU 開發

## 為什麼拆

- PAAW 回歸純 platform（module 掛載系統一併移除）
- 各 app 獨立版本控制 + 獨立 dev server，用 coding app 管理（EM 派工 → review → release）
- 學習空間優先（Fleming 為女兒打造），已遷 learning-space repo
