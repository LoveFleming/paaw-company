# 0927 更新包 — OOM 治本（bash 殘留程序帳本 + browser session idle timeout）

**背景：2026-09-27 公司 Linux Chrome + VSCode 被 OOM killer 全砍、Mac mini 同日 jetsam 砍 Chrome。
根因：coding agent 用 bash 起的背景程序（test harness / verify server）在任務結束後變孤兒，
跨日累積吃光 RAM。治本 = 殘留記帳 + agent run 結束精準掃殺 + timeout 樹殺 + browser idle 自動關。**

對應 tPAAW commits：`3150568e` + `41da3943`

## ⚠️ 本包特殊性：兩個檔案是 backport 版，不是 tPAAW HEAD 原檔

公司版依賴閉包已落後 HEAD 38 檔，直接上 HEAD 的 paaw-agent-loop.mjs 會因缺依賴炸掉。
所以 agent-loop / browser-executor 是**基於公司現行版（0911 批次）打上治本 patch** 的 backport 版，
其餘 4 檔是 HEAD 原檔（自包含、零新依賴）。

## 覆蓋清單（6 檔，全部 packages/server/src/lib/）

| 檔案 | 狀態 | 說明 |
|------|------|------|
| `packages/server/src/lib/proc-ledger.mjs` | **A（新檔）** | 程序帳本核心：process group 執行 / 記帳 / 掃殺 / 持久化 |
| `packages/server/src/lib/shell-guard.mjs` | **A（新檔）** | 0913 鐵律補寄：擋 pkill/killall/taskkill，kill 只放行本 RU dev server |
| `packages/server/src/lib/dev-server.mjs` | M（0910→HEAD） | 孤兒偵測 + restart 保護；shell-guard 依賴其 devServerPid |
| `packages/server/src/lib/browser-setup.mjs` | M | 跨平台 channel 解析（自包含；repo 之前沒 mirror 過，保險附上） |
| `packages/server/src/lib/paaw-agent-loop.mjs` | M（**backport**） | runShell 改走帳本；兩個 loop 進場掛 runId、結尾掃殘留 |
| `packages/server/src/lib/browser-executor.mjs` | M（**backport**） | session 30 分鐘 idle 自動 close |

## 手動刪除

無

## 部署步驟（照 SOP）

1. 六個檔案照路徑覆蓋 tPAAW
2. 重啟 paaw-server
3. `npm run build`（本包無 UI 變更，build 是保險）
4. `node scripts\pack.mjs --skip-build`

## 部署後行為（自動生效，無需設定）

- agent 每次 bash 的背景程序（`&` 起 harness/server）在 **agent run 結束時自動掃殺**（只殺該 run 自己 spawn 的 process group，絕不碰系統程序）
- bash 指令 **timeout 改殺整棵 process 樹**（以前只殺 shell、孤兒存活）
- browser session **30 分鐘無動作自動關閉**（每個 session = 一個完整 Chrome instance）
- 殘留帳本落盤 `log/tmp/proc-ledger-state.json`，**server 重啟後自動領養**舊殘留
- 超過 2 小時的殘留由背景保險絲自動收掉

## 快速驗證（可選）

跑一輪 coding 任務後：`ps aux | grep -E "harness|fake-paaw"` 應該看不到任務結束後還活著的 node 殘留。
