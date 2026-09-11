# 0910 — Dev Server Controller Phase 1（tPAAW `2f96ad37`）

**功能：** coding agent 可自主啟動/重啟專案 dev server 並讀 log（dev_server 工具）。
Agent 改完 UI code 後可自己驗證 dev server 起得來、log 沒炸，不用人肉重啟。

## 覆蓋清單（4 檔，tPAAW 相對路徑）

```
M  .paaw/agents/coding.developer.json
M  data/crews/coding.developer.json
A  packages/server/src/lib/dev-server.mjs        ← ⭐ 新檔（dev server 控制器）
M  packages/server/src/lib/paaw-agent-loop.mjs   ← ⭐ 註冊 dev_server 工具
```

## 步驟

1. 4 檔照相對路徑覆蓋（dev-server.mjs 是新檔）
2. 重啟 server（paaw-agent-loop.mjs 是 server 端）
3. coding developer agent 自動有 dev_server 工具可用

⚠️ 覆蓋 paaw-agent-loop.mjs 前確認你本地版本 = 0910 之前的 mirror 版（a2960817）。
若公司端有自己改過，先比對再蓋。
