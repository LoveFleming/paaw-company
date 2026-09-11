# 0911 — Action Log 三連修（tPAAW `e93625dc` + `e9883883` + `b0d5d988`）

**修的主 bug：** coding agent chat 送出時整個 turn 掛掉：
`TypeError: e.affectedFiles.join is not a function`（action-log.mjs:99:138）

**原因：** LLM 呼叫 `action_log_add` 時 schema 要求 affectedFiles 是 array，
但模型有時給單一字串 → 原封寫進 actions.jsonl → 之後**每次** send chat 的
context injection 讀 action log 組字串時 TypeError → 爛資料在檔案裡一天，聊天炸一天。

## 覆蓋清單（2 檔，tPAAW 相對路徑）

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
