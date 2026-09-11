# 0911 — Action Log 三連修 + refresh 斷線治本

**批次 2（晚間，tPAAW `442073ff`）：refresh/斷網斷線治本 — agent 繼續跑、回覆不丟**

**修的主 bug：** Chrome refresh（vite full reload）或斷網後：
1. coding app developer chat（a2a SSE）斷線 → server 繼續跑但回覆沒人接 → 永久丟失
2. AgentConsole WS 斷線且無 auto-reconnect → 對話清空接不回

## 覆蓋清單（本批 4 檔，tPAAW 相對路徑）

```
M  packages/server/src/routes/a2a.mjs           ← streamStates registry 攔截 SSE buffer；GET /a2a/:agentId/stream-state 接回進度/最終回覆；client 斷線不 abort；完成後 server 落地回覆（TTL 15min）
M  packages/server/src/websocket/ws-handler.mjs ← spawn 支援 resumeSessionId（10min TTL），resume 保留 history、事件送新 ws；kill 明確 drop resumable
M  packages/ui/src/components/AgentConsole.tsx  ← WS auto-reconnect（1s 起 backoff 上限 15s）+ sessionStorage 記 session id + resume 帶回 history；restartSession 清 resume 痕跡
M  packages/ui/src/pages/CodingIDE.tsx          ← developer chat 斷線後輪詢 stream-state（3s），done 補 finalContent、執行中顯示最新動作；dedupe 防雙落地重複
```

步驟：4 檔覆蓋 → 重啟 server（npm run dev）。
驗證：`curl "http://127.0.0.1:4097/a2a/developer/stream-state?cwd=<專案路徑>"` 回 `{"running":false,"done":false,"exists":false}` = 通（Mac 已驗證 OK，含 5173 vite proxy）。

---

**批次 1（中午，tPAAW `e93625dc` + `e9883883` + `b0d5d988`）：Action Log 三連修**

**修的主 bug：** coding agent chat 送出時整個 turn 掛掉：
`TypeError: e.affectedFiles.join is not a function`（action-log.mjs:99:138）

**原因：** LLM 呼叫 `action_log_add` 時 schema 要求 affectedFiles 是 array，
但模型有時給單一字串 → 原封寫進 actions.jsonl → 之後**每次** send chat 的
context injection 讀 action log 組字串時 TypeError → 爛資料在檔案裡一天，聊天炸一天。

## 覆蓋清單（批次 1 兩檔，tPAAW 相對路徑）

```
M  packages/server/src/lib/action-log.mjs     ← ⭐ 主修復：normalizeAffectedFiles() 寫入+讀取雙側
M  packages/server/src/routes/coding.mjs      ← 順手修：GET /api/coding-crew/action-log 本來就是死的
```

coding.mjs 兩處（兩個 commit）：
1. `:crewId` 路由排除清單漏了 `action-log` → 永遠 404 "Crew not found"
2. handler 用了不存在的 `sendJSON` helper（copy-paste 自其他檔）→ 500

## 步驟

1. 2 檔照相對路徑覆蓋
2. 重啟 server
3. 直接重試原本掛掉的 chat — 舊爛資料讀取時自動 normalize，**不用手動清 actions.jsonl**

## 驗證（覆蓋後可 curl）

```
curl "http://127.0.0.1:4097/api/coding-crew/action-log?path=<專案路徑>&limit=2"
```
回傳 `{"entries":[...],"text":"..."}` = 通（Mac 已驗證 OK）。
