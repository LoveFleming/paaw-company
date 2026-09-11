# 0911 — Action Log 型別防禦修復（tPAAW `e93625dc`）

**修的 bug：** coding agent chat 送出時整個 turn 掛掉，error：
`TypeError: e.affectedFiles.join is not a function`

**原因：** LLM 呼叫 `action_log_add` 時 schema 要求 affectedFiles 是 array，
但模型有時給單一字串（如 `"a.mjs, b.mjs"`）。原封寫進 actions.jsonl 後，
**之後每次 send chat** 的 context injection 讀 action log 組字串時直接 TypeError
→ 只要爛資料在檔案裡，每次聊天都炸。

## 覆蓋清單（1 檔）

```
M  packages/server/src/lib/action-log.mjs   ← normalizeAffectedFiles() 寫入+讀取雙側防禦
```

## 步驟

1. 覆蓋 `packages/server/src/lib/action-log.mjs`
2. 重啟 server
3. 直接重試原本掛掉的 chat — 舊爛資料讀取時自動 normalize，不需要手動清 actions.jsonl

（想手動確認的話：專案 `.paaw/coding-memory/actions.jsonl` 裡 affectedFiles
不是 array 的行，修復後讀取會自動轉正常，不炸。）
