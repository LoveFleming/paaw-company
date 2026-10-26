# DEPLOY9 — 2026-10-26 公司手動更新包（packages/ 全量）

- **來源**：tPAAW dev `1c3607a9`（2026-10-26）
- **內容**：packages/ 全量 392 檔，路徑 = tPAAW 相對路徑（雙螢幕比對用）
- **適用對象**：目前跑 deploy8 v3（來源 ee80170d）的環境 — **實際只需動 54 檔**（41 改 + 6 新增 + 7 刪除），其餘 338 檔與現行相同

## ⚠️ 必刪 7 檔（installed-apps 平台拆除殘留 — 509b3044，不刪 server 會掛舊入口）
```
packages/server/src/lib/app-modules.mjs
packages/server/src/lib/module-ui-bundle.mjs
packages/server/src/lib/secretary-tools.mjs
packages/ui/src/components/ModulePageLoader.tsx
packages/ui/src/components/ModulePickerModal.tsx
packages/ui/src/moduleRuntime.ts
packages/ui/src/pages/AppModules.tsx
```

## 新增 6 檔
```
packages/server/src/lib/crosscut-concepts.mjs
packages/server/src/lib/feature-map-store.mjs
packages/server/src/lib/feature-subfeature.mjs
packages/server/src/lib/test-signals.mjs
packages/ui/src/components/DispatchReports.tsx
packages/ui/src/components/MermaidBlock.tsx
```

## 修改 41 檔
```
packages/server/src/data-home.mjs
packages/server/src/lib/agent-rules.mjs
packages/server/src/lib/audit-log.mjs
packages/server/src/lib/code-graph.mjs
packages/server/src/lib/context-compaction.mjs
packages/server/src/lib/feature-map-v2.mjs
packages/server/src/lib/feature-map-validator.mjs
packages/server/src/lib/paaw-agent-loop.mjs
packages/server/src/lib/paaw-sandbox.mjs
packages/server/src/lib/qa-results.mjs
packages/server/src/lib/release-requests.mjs
packages/server/src/lib/review-boundary.mjs
packages/server/src/lib/script-guard.mjs
packages/server/src/lib/tree-sitter-parser.mjs
packages/server/src/paaw-server.mjs
packages/server/src/routes/ai-settings.mjs
packages/server/src/routes/apps.mjs
packages/server/src/routes/coding-features.mjs
packages/server/src/routes/coding-releases.mjs
packages/server/src/routes/coding.mjs
packages/server/src/routes/llm-logs.mjs
packages/ui/package.json
packages/ui/src/App.tsx
packages/ui/src/components/ChatMessages.tsx
packages/ui/src/components/EMDashboard.tsx
packages/ui/src/components/FeatureMap.tsx
packages/ui/src/components/MarkdownText.tsx
packages/ui/src/components/ReleaseRequests.tsx
packages/ui/src/components/SkillsUsagePanel.tsx
packages/ui/src/components/git/DecisionCard.tsx
packages/ui/src/i18n/locales/en.json
packages/ui/src/i18n/locales/ja.json
packages/ui/src/i18n/locales/zh-mix.json
packages/ui/src/i18n/locales/zh.json
packages/ui/src/pages/ChatView.tsx
packages/ui/src/pages/CodingIDE.tsx
packages/ui/src/pages/FileViewer.tsx
packages/ui/src/pages/HelpDesk.tsx
packages/ui/src/pages/SkillBuilder.tsx
packages/ui/tsconfig.json
packages/ui/vite.config.ts
```

## 一定要跑 npm install
ui 加了新依賴 \`mermaid ^12.2.0\`（全站 chat 渲染 Mermaid 圖）— 蓋完檔要先 \`npm install\` 再 build。

## 重要變更摘要（ee80170d → 1c3607a9，36 commits）
- **App Module 平台全拆**（509b3044）：installed-apps 遷出 tPAAW，module 入口/UI/route 移除（即上面 7 個刪除檔）
- **Feature Map 全新 L1 骨架 deterministic-v3**（1c7147c4）：語言無關、100% 覆蓋；+ L2 子群精煉、橫切概念（Cross-cutting Concepts）overlay、測試自足性判定
- **RU 隔離模型 — File Mounts 三通道一致**（5b6bb25f，10-24 定調）
- **sandbox 修復系列**：egress 判定納入直譯器（d4472317）、providers.json/.env 防護掛載點修正、EPERM 歸因細分、path-guard 黑名單改相對路徑不再誤擋 monorepo
- **compaction doom-loop 修復**（e1c781d9）：預算被 max_tokens 砍半 + tail 膨脹 + 每步重壓
- **Mermaid**：全站 chat 渲染 + 圖內字體/縮放一系列修正
- **API Tester** 右欄 Developer AI 統一全站 side chat + 工具列分類改軟體工廠四部門
- **派工報告改獨立 tab page**（a7a91235，入口 👷 生產 menu）
- **audit**：egress 已 allow 指令不再誤歸因白名單 + Tool Log 長指令摺疊（46a3f856）
- **script-guard**：curl | python3 -m json.tool 不算下載即執行（4d7a86b2）
- 其他：ai-settings workspaces 讀檔路徑修正、llm-logs 最新在前、release NextSteps 帶證據行、CU 命名/回歸修一輪

## 公司更新 SOP
```
1. 蓋檔：54 檔 delta（上面的清單）照相對路徑覆蓋 tPAAW（或整包 packages/ 392 檔全蓋也行，內容一致）
2. 刪除：上面 7 檔手動刪掉
3. npm install    # mermaid 新依賴
4. npm run build
5. 重啟 server
```
