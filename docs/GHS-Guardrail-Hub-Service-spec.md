

AI-Native Product Development Specification

PM 提供 What & Why，Architect / Developer 定義 How，AI 協助設計、開發與測試。

Product Development Schema

#	Section	核心內容	主要負責
1	Product Overview	產品定位、目標、範圍、使用情境	PM + Architect
2	Functional Requirements	功能需求、業務規則、Acceptance Criteria	PM + Architect
3	Non-Functional Requirements	Performance、Availability、Security、Scalability	Architect
4	API & Data Contract	API、Request / Response、Error Code、Schema	Architect
5	Architecture Design	Components、Sequence、Integration、Data Flow	Architect
6	Technical Design	Modules、Interface、核心邏輯、技術選型	Architect + Developer
7	Testing Strategy	Unit、Integration、E2E、Failure Scenarios	Developer + AI
8	Operations & Observability	Metrics、Logging、Tracing、Deployment、Alerting	SRE + Developer


# GHS — Guardrail Hub Service
## 2. Functional Requirements（功能需求）

**Version:** v1.0 Draft  
**Status:** Under Review

### 2.1 Functional Requirements Summary

| ID | Feature | Description |
|---|---|---|
| FR-01 | Guardrail Integration | 統一整合多個外部 Guardrail Service。 |
| FR-02 | AP-based Configuration | 每個 AP 可獨立設定 Guardrail 組合與 Block / Monitor Mode。 |
| FR-03 | Single Guardrail | 支援 AP 使用單一外部 Guardrail。 |
| FR-04 | Parallel Guardrails | 支援 AP 平行使用多個外部 Guardrail。 |
| FR-05 | Block Mode | AP 啟用 Block Mode 時，依整合判決回傳 Safe 或 Block。 |
| FR-06 | Monitor Mode | AP 啟用 Monitor Mode 時，GHS 一律回傳 Safe，只記錄 Metrics 與 Audit Log。 |
| FR-07 | Timeout & Load Protection | 控制等待時間及同時執行數量，避免 Guardrail 影響服務可用性。 |
| FR-08 | Fail-open | Guardrail 無法提供有效判決時，預設允許通過。 |
| FR-09 | Verdict Aggregation | 任一 Guardrail 明確判定 Block，整合判決即為 Block。 |
| FR-10 | Audit Log Integration | 所有 GHS Request 都必須產生 Audit Log。 |
| FR-11 | Metrics Collection | 提供安全判決、服務品質及效能相關 Metrics。 |

---



## 2. Functional Requirements（功能需求）

### 2.1 Functional Requirements Summary

| ID | Feature | Description |
|---|---|---|
| FR-01 | Guardrail Integration | 統一整合多個外部 Guardrail Service。 |
| FR-02 | AP-based Configuration | 每個 AP 可獨立設定 Guardrail 組合及 Block / Monitor Mode。 |
| FR-03 | Single Guardrail | 支援 AP 使用單一外部 Guardrail。 |
| FR-04 | Parallel Guardrails | 支援 AP 平行使用多個外部 Guardrail。 |
| FR-05 | Block Mode | 任一 Guardrail 明確判定 Block，GHS 立即回傳 Block。 |
| FR-06 | Monitor Mode | 任一 Guardrail 明確判定 Block，GHS 立即回傳 Safe，其他檢查可在 Timeout 限制內繼續執行。 |
| FR-07 | Timeout & Load Protection | 控制 Guardrail 檢查時間與並行數量，保護服務可用性。 |
| FR-08 | Fail-open | 外部 Guardrail 無有效判決時，預設允許通過。 |
| FR-09 | Verdict Aggregation | 任一 Guardrail 明確判定 Block，整合判決即為 Block。 |
| FR-10 | Audit Log Integration | 所有 GHS Request 均須產生 Audit Log。 |
| FR-11 | Metrics Collection | 提供 Guardrail 判決、錯誤及效能相關 Metrics。 |

---

### 2.2 Functional Requirements Details

#### FR-01 — Guardrail Integration

**Requirement**

GHS 提供統一的 Guardrail 檢查介面，整合不同廠商或來源的外部 Guardrail Service。

**Acceptance Criteria**

- Gateway 可透過 GHS 使用已整合的 Guardrail。
- Gateway 不需要了解各 Guardrail 不同的 API 格式。
- GHS 能識別外部 Guardrail 的檢查結果或技術失敗。

#### FR-02 — AP-based Configuration

**Requirement**

不同 AP 可以獨立設定要使用的 Guardrail Service，以及整體執行模式。

**Acceptance Criteria**

- AP-A 與 AP-B 可使用不同的 Guardrail 組合。
- AP-A 可使用 Monitor Mode，AP-B 可使用 Block Mode。
- 同一 AP 的 Guardrail 組合與 Mode 可調整。

**Business Rule**

- Block / Monitor Mode 為 **AP 層級設定**，不是個別 Guardrail 的設定。

#### FR-03 — Single Guardrail

**Requirement**

AP 可以選擇單一 Guardrail Service 進行安全檢查。

**Acceptance Criteria**

- AP 只設定一個 Guardrail 時，GHS 僅呼叫該 Guardrail。
- 該 Guardrail 的有效判決可直接作為整合判決。

#### FR-04 — Parallel Guardrails

**Requirement**

AP 可以選擇多個外部 Guardrail Service，同時進行安全檢查。

**Acceptance Criteria**

- AP 可以設定多個 Guardrail。
- GHS 平行執行已選擇的 Guardrail。
- 檢查結果交由 FR-09 統一整合。

#### FR-05 — Block Mode

**Requirement**

當 AP 使用 Block Mode，GHS 根據 Aggregated Verdict 回傳最終安全判決。

**Acceptance Criteria**

- Aggregated Verdict 為 Block → GHS 回傳 Block。
- Aggregated Verdict 為 Safe → GHS 回傳 Safe。
- 任一 Guardrail 明確判定 Block，GHS 立即回傳 Block。
- 提前回覆時，取消其他尚未完成的 Guardrail 檢查。

#### FR-06 — Monitor Mode

**Requirement**

當 AP 使用 Monitor Mode，GHS 採用與 Block Mode 相同的 Guardrail 檢查及 Timeout 機制，但不執行阻擋，最終一律回傳 Safe，並記錄 Metrics 與 Audit Log。

**Acceptance Criteria**

- GHS 等待 Guardrail 判決，不立即回傳 Safe。
- 任一 Guardrail 明確判定 Block，GHS 立即回傳 Safe。
- 提前回覆後，其他尚未完成的 Guardrail 繼續在背景執行，直到完成或 Timeout。
- 所有 Guardrail 完成且沒有 Block，GHS 回傳 Safe。
- 到達 Overall Deadline 且沒有收到 Block，GHS 回傳 Safe。
- 到達 Timeout 時，停止等待並取消未完成的檢查。
- 記錄已取得的 Guardrail 判決、Latency、Timeout、Error 等 Metrics 與 Audit Log。

#### FR-07 — Timeout & Load Protection

**Requirement**

GHS 必須限制 Guardrail 檢查的等待時間與執行負載，避免外部服務過慢或異常影響 Gateway。

**Business Rules**

- Block Mode 與 Monitor Mode 均採用 Early Block 及 Overall Deadline。
- Monitor Mode 提前回覆後，未完成的檢查可在 Deadline 內繼續執行。
- GHS 限制同時執行的外部 Guardrail 檢查數量。
- Monitor Mode 超過 Concurrency Limit 時，允許略過檢查，並記錄 Skipped Metrics 與 Audit Log。

**Acceptance Criteria**

- 任一 Guardrail 明確判定 Block，GHS 立即依 AP Mode 回傳 Block 或 Safe。
- 所有 Guardrail 完成且沒有 Block，GHS 回傳 Safe。
- 到達 Overall Deadline 且沒有收到 Block，依 Fail-open 回傳 Safe。
- 到達 Deadline 時，停止等待並取消未完成的檢查。
- GHS 同時執行的外部 Guardrail 檢查數量不得超過設定的 Concurrency Limit。
- Monitor Mode 超過 Concurrency Limit 時，允許略過檢查，並記錄 Skipped 狀態。

#### FR-08 — Fail-open

**Requirement**

當外部 Guardrail Timeout、Error 或無法提供有效判決時，GHS 預設允許通過。

**Acceptance Criteria**

- 單一 Guardrail Timeout / Error 且沒有其他 Block → Safe。
- 全部 Guardrail Timeout / Error → Safe。
- 任一 Guardrail 已明確判定 Block → 不被 Fail-open 覆蓋。
- 技術失敗狀態仍需記錄。

#### FR-09 — Verdict Aggregation

**Requirement**

GHS 統一整合多個外部 Guardrail 的判決。

**Business Rules**

- 任一 Guardrail 明確回傳 Block → Aggregated Verdict 為 Block。
- 沒有任何 Block → Aggregated Verdict 為 Safe。
- Aggregated Verdict 不受 AP Block / Monitor Mode 影響。
- AP Mode 僅決定是否實際阻擋。

**Acceptance Criteria**

- Allow + Allow → Safe。
- Allow + Block → Block。
- Timeout + Block → Block。
- Timeout + Error → Safe（Fail-open）。
- Monitor Mode 即使 Aggregated Verdict 為 Block，GHS 最終仍回傳 Safe。

#### FR-10 — Audit Log Integration

**Requirement**

GHS 必須呼叫 Audit Log Service，為所有 GHS Request 產生稽核紀錄。

**Acceptance Criteria**

- Allow、Block、Timeout、Error 均產生 Audit Log。
- Block Mode 與 Monitor Mode 均產生 Audit Log。
- Skipped Request 也必須記錄。
- Audit Log 可識別 AP、執行模式、實際 Guardrail 判決及 GHS 最終決策。
- Monitor Mode 背景檢查取得的結果亦須納入 Audit Log。
- 未完成或已取消的檢查不得被記錄為已判定 Safe。

#### FR-11 — Metrics Collection

**Requirement**

GHS 必須提供可觀測性指標，供服務監控及效能分析使用。

**Acceptance Criteria**

- 記錄 Request 數量。
- 記錄 Allow / Block 統計。
- 記錄 Timeout / Error / Skipped 統計。
- 提供 GHS Request 及外部 Guardrail 的 Latency 指標。
- Monitor Mode 背景檢查的判決、Latency 與 Timeout 亦須納入 Metrics。
- Block Mode 與 Monitor Mode 均提供 Metrics。

---

# GHS — Non-Functional Requirements v0.3

## NFR-02 — Availability

**Requirement**

GHS 必須避免外部 Guardrail 或自身服務故障影響 LLM API Gateway 的正常運作。

**Rules**
- 外部 Guardrail Timeout / Error，採用 Fail-open。
- GHS 無法回應時，Gateway 採用 Fail-open，繼續處理使用者請求。
- Audit Log Service 故障不得影響 GHS 判決。
- 暫不設定 Availability SLO。

**Acceptance Criteria**
- 外部 Guardrail 故障不造成 Gateway 請求失敗。
- GHS 無法回應時，Gateway 仍可繼續處理請求。
- Audit Log Service 異常時，GHS 仍能正常回傳判決，並記錄 Audit 呼叫失敗的 Metrics。

---

## NFR-03 — Security

**Requirement**

GHS 必須保護 API 通訊、認證資訊與敏感資料，避免未授權存取及機密資訊洩漏。

**Rules**
- Gateway → GHS：使用 API Key 進行身分驗證。
- GHS → 外部 Guardrail：使用公司內部服務的 Bearer Token。
- Application Logging 不得記錄敏感資訊。
- Audit Log 由 Security Team 提供的 Audit Log Service 負責管理。

**Acceptance Criteria**
- 無效 API Key 不得存取 GHS API。
- GHS 呼叫外部 Guardrail 時須提供有效的 Bearer Token。
- Application Logs 不包含 API Key、Bearer Token 或敏感 Request Content。
- GHS 能將稽核資訊傳送至 Audit Log Service。

---

## NFR-04 — Scalability

**Requirement**

GHS 必須支援 Kubernetes 水平擴展，並能根據預期流量評估所需 Pod 數量。

**Rules**
- GHS 部署於 Kubernetes。
- 初期採用人工調整 Pod 數量。
- 透過 Concurrency Limit 控制外部 Guardrail 檢查負載。
- 必須具備根據流量與效能數據估算 Pod 數量的能力。
- 暫不要求自動擴展（HPA）。

**Acceptance Criteria**
- GHS 可透過增加 Pod 數量擴充處理能力。
- GHS 不超過設定的 Concurrency Limit。
- 可根據 QPS、Guardrail Latency 與單 Pod 處理能力，估算所需 Pod 數量。
- 可透過負載測試驗證 Pod 容量估算。

**待確認參數**
- 預期 QPS 與尖峰流量。
- Concurrency Limit（Per Pod 或 Global）。
- 單 Pod 可承受的處理量。




## 4. API & Data Contract



### 4.1 API Definition

GHS 提供統一的 Guardrail API，依 API Path 識別 Internal / External AP，並根據各 AP 的設定執行 Guardrail 檢查。

| Item | Definition |
|---|---|
| Internal Endpoint | `POST /internal/{app_name}` |
| External Endpoint | `POST /external/{app_name}` |
| Request Format | OpenAI-compatible JSON |
| Response Format | JSON |
| Authentication | AP-specific API Key |
| AP Configuration | 依 Path 識別 AP，取得 Guardrail 組合、Mode 與 Timeout |
| Error Handling | Fail-open，避免影響 Gateway 正常運作 |

**API Examples**

Internal AP：
```http
POST /internal/mes-agent
X-API-Key: <AP_API_KEY>
Content-Type: application/json
```

External AP：
```http
POST /external/customer-agent
X-API-Key: <AP_API_KEY>
Content-Type: application/json
```

**Business Rules**

- 每個 AP 使用獨立的 API Key。
- GHS 根據 `app_name` 識別 AP。
- GHS 必須驗證 API Key 與 AP 的對應關係。
- `internal` / `external` 表示 AP 分類，不代表 Endpoint 必須直接暴露至外部網路。

### 4.2 Request Headers

| Header | Required | Purpose |
|---|---|---|
| `Content-Type` | Yes | `application/json` |
| `X-API-Key` | Yes | AP 身分驗證 |
| `X-Request-ID` | Yes | Audit Log 與 Request Tracking |
| `traceparent` | No | Distributed Tracing |
| `X-User-ID` | No | Audit 使用者識別 |

**Business Rules**

- AP ID 由 API Path 取得，不需要 `X-AP-ID` Header。
- GHS 將必要的 Request Metadata 傳送至 Audit Log Service。
- API Key、Bearer Token 不得記錄於 Application Logs 或 Audit Log。

### 4.3 Request Body

GHS 接收 OpenAI-compatible Payload，不增加 GHS 專屬 Body Wrapper。

**Example**

```json
{
  "model": "example-model",
  "messages": [
    {
      "role": "system",
      "content": "You are a helpful assistant."
    },
    {
      "role": "user",
      "content": "Hello"
    }
  ]
}
```

**Business Rules**

- GHS 根據 AP Configuration 選擇外部 Guardrail Service。
- GHS 將必要的 Request Content 轉換為各 Guardrail 所需格式。
- 外部 Guardrail API 為自訂格式，不要求遵循統一標準。

### 4.4 Response Schema

GHS 使用簡單且統一的 Response Contract，僅包含兩個欄位。

**JSON Schema**

```json
{
  "type": "object",
  "required": ["is_safe", "categories"],
  "properties": {
    "is_safe": {
      "type": "boolean"
    },
    "categories": {
      "type": "array",
      "items": {
        "type": "string"
      }
    }
  },
  "additionalProperties": false
}
```

**Field Definition**

| Field | Type | Description |
|---|---|---|
| `is_safe` | boolean | `true`：Gateway 繼續執行；`false`：Gateway 阻擋 |
| `categories` | string[] | 回覆當下已取得的違規類別，無資料時為空陣列 |

**Example 1 — Safe**

HTTP 200

```json
{
  "is_safe": true,
  "categories": []
}
```

**Example 2 — Block Mode**

HTTP 200

```json
{
  "is_safe": false,
  "categories": ["prompt_injection"]
}
```

**Example 3 — Monitor Mode**

HTTP 200

```json
{
  "is_safe": true,
  "categories": ["prompt_injection"]
}
```

**Example 4 — Fail-open**

HTTP 200

```json
{
  "is_safe": true,
  "categories": []
}
```

### 4.5 Response Rules

| Scenario | HTTP Status | is_safe | categories |
|---|---|---|---|
| Guardrail 判定 Safe | 200 | true | [] |
| Block Mode 偵測違規 | 200 | false | 違規類別 |
| Monitor Mode 偵測違規 | 200 | true | 已取得的違規類別 |
| Guardrail Timeout / Error，無 Block | 200 | true | 已取得的違規類別或 [] |
| GHS Internal Error（可處理） | 200 | true | [] |

**Business Rules**

- Block Mode：任一 Guardrail 明確判定 Block，GHS 立即回傳 `is_safe: false`。
- Monitor Mode：任一 Guardrail 明確判定 Block，GHS 立即回傳 `is_safe: true`。
- Monitor Mode 提前回覆後，其他 Guardrail 繼續在背景檢查，直到完成或 Timeout。
- 背景取得的判決記錄於 Metrics 與 Audit Log，不修改已回傳的 Response。
- 外部 Guardrail Timeout / Error 採用 Fail-open，但不覆蓋已確認的 Block 判決。
- GHS 可處理的 Internal Error 統一回傳 HTTP 200、`is_safe: true`。
- `is_safe: true` 代表 Gateway 可以繼續執行，不代表所有 Guardrail 都確認內容安全。

### 4.6 HTTP Status Codes

| HTTP Status | Meaning | Handling |
|---|---|---|
| 200 | Safe / Block / Fail-open | Gateway 依 `is_safe` 決定是否繼續 |
| 400 | Invalid Request | Request 格式錯誤 |
| 401 | Unauthorized | API Key 無效或不屬於指定 AP |

**Business Rules**

- 外部 Guardrail Timeout / Error 不回傳 HTTP 5xx。
- GHS 可處理的 Internal Error 不回傳 HTTP 500，而採用 HTTP 200 + Fail-open。
- 400 / 401 保留給 Request 格式與身分驗證錯誤。
- GHS Pod 當機、連線失敗或無回應時，由 Gateway 自行採用 Fail-open。
- Internal Error 必須記錄 Metrics，並在可行時記錄 Audit Log。

### 4.7 External Guardrail API Contract

GHS 整合多個自訂格式的外部 Guardrail API。

**Responsibilities**

| Function | Description |
|---|---|
| Request Mapping | 將 OpenAI-compatible Payload 轉換為外部 Guardrail 所需格式 |
| Response Normalization | 將外部結果轉換為 Safe / Block / Error 等統一狀態 |
| Verdict Aggregation | 任一 Guardrail 明確 Block，整合判決即為 Block |
| Timeout Handling | 依 AP 的 Overall Timeout 控制等待時間 |
| Audit & Metrics | 記錄判決、Latency、Timeout、Error |

**Business Rules**

- GHS 呼叫公司內部 Guardrail Service 時使用 Bearer Token。
- Gateway 不需要了解各外部 Guardrail 的 API 格式。
- GHS 不將外部 Guardrail 的原始 Response 直接暴露給 Gateway。

### 4.8 AP Identification & Observability

GHS 依 API Path 識別 AP，作為 Configuration、Metrics 與 Audit Log 的依據。

**AP Attributes**

| Attribute | Example | Purpose |
|---|---|---|
| `app_name` | mes-agent | AP 識別 |
| `app_type` | internal | Internal / External 分類 |
| `mode` | monitor | AP 執行模式 |
| API Key | AP 專屬 | AP 身分驗證，不記錄原始值 |

**Metrics Dimensions**

GHS 必須能依 AP 分析以下指標：

- Request Count / QPS
- GHS Request Latency
- Guardrail Latency
- Safe / Block Count
- Timeout / Error / Skipped Count

**Audit Log**

- 所有 GHS Request 均須產生 Audit Log。
- Audit Log 包含 AP 識別、執行模式、Guardrail 判決與 GHS 最終決策。
- Monitor Mode 背景檢查結果也必須納入 Audit Log。
- Audit Log Service 由 Security Team 負責。
- Audit Log Service 故障不得影響 GHS 判決或 Gateway 正常運作。

---

### 4.9 Design Principles

1. **Minimal API Contract**：Response 僅包含 `is_safe` 與 `categories`。
2. **AP-based Isolation**：每個 AP 擁有獨立 API Key、Guardrail Configuration 與執行模式。
3. **Fail-open by Default**：技術異常盡可能不影響 Gateway。
4. **Block Mode Enforcement**：明確違規時仍能阻擋。
5. **Observability**：支援依 AP 觀察流量、判決與服務品質。
6. **Gateway Availability First**：GHS 不應成為使用者正常使用 LLM 服務的障礙。

# GHS — Guardrail Hub Service

## 5. Architecture Design

**Version:** v1.0  
**Status:** Finalized  
**Date:** 2026-11-14

### 5.1 Components（元件架構）

GHS 採用 Stateless Service 架構，部署於 Kubernetes，統一處理 AP 設定、Guardrail 呼叫、判決整合及觀測資料。

| Component | Responsibility |
|---|---|
| API Handler | 接收 Gateway Request、驗證 API Key、識別 AP。 |
| AP Configuration | 管理各 AP 的 Guardrail 組合、Block / Monitor Mode 與 Timeout。 |
| Guardrail Orchestrator | 平行呼叫 Guardrail，處理 Verdict Aggregation、Timeout、Fail-open 與 Concurrency Control。 |
| Guardrail Adapters | 每個外部 Guardrail 使用獨立 Adapter，處理自訂 API 的 Request / Response Mapping。 |
| Observability Integration | 輸出 Metrics，並呼叫 Audit Log Service。 |

**Architecture Decisions**

- AP Configuration 由 GHS 自行管理，不建立獨立 Configuration Service。
- 每個外部 Guardrail 使用獨立 Adapter，隔離不同的 API 格式。
- Audit Log 採非阻塞方式處理，不影響 Gateway 回覆時間。
- 第一版不額外引入 Redis、NATS、Queue 或 Database。

### 5.2 Sequence（呼叫流程）

**Standard Request Flow**

| Step | Action |
|---|---|
| 1 | Gateway 呼叫 `/internal/{app_name}` 或 `/external/{app_name}`。 |
| 2 | GHS 驗證 AP 專屬 API Key，取得 AP Configuration。 |
| 3 | GHS 根據 AP 設定，透過 Adapters 平行呼叫外部 Guardrail。 |
| 4 | Guardrail Orchestrator 收集判決，依 FR-09 整合結果。 |
| 5 | GHS 根據 AP 的 Block / Monitor Mode 回傳 `is_safe` 與 `categories`。 |
| 6 | GHS 收集 Metrics，並將必要資訊傳送 Audit Log Service。 |

**Block Mode vs. Monitor Mode**

| Scenario | Block Mode | Monitor Mode |
|---|---|---|
| 任一 Guardrail 回 Block | 立即回 Block | 立即回 Safe |
| 其他 Guardrail 未完成 | 取消檢查 | 背景繼續檢查 |
| 全部完成且無 Block | 回 Safe | 回 Safe |
| Overall Timeout | Fail-open | Fail-open |
| Metrics | 記錄 | 記錄 |
| Audit Log | 非阻塞 | 非阻塞 |

**Business Rules**

- Monitor Mode 提前回覆後，其他 Guardrail 在背景繼續執行，直到完成或達到 Timeout。
- 所有未完成的檢查均受到 Timeout 與 Concurrency Limit 保護。
- Guardrail Timeout / Error 不得覆蓋已取得的明確 Block 判決。
- Audit Log Service 故障不得影響 GHS 判決。

### 5.3 Integration（外部系統整合）

| Integration | Protocol / Format | Authentication |
|---|---|---|
| Gateway → GHS | HTTP / OpenAI-compatible JSON | AP-specific API Key |
| GHS → Guardrail Services | HTTP / Custom API | Internal Bearer Token |
| GHS → Audit Log Service | Security Team API Contract | 依 Audit Service 規範 |
| Prometheus → GHS | Metrics Endpoint | 依內部監控規範 |

**Integration Principles**

- Gateway 不需要了解不同 Guardrail 的 API 格式。
- GHS Adapters 負責轉換 Request 與標準化 Response。
- 新增 Guardrail 時，應避免影響既有 Adapter 與 Gateway Contract。
- GHS 不負責 Audit Log 的長期儲存與查詢。

### 5.4 Data Flow（資料流）

| Data | Source | Usage |
|---|---|---|
| Request Data | LLM API Gateway | 提供 Guardrail 檢查內容 |
| AP Configuration | GHS | 決定 Guardrail 組合、Mode、Timeout |
| Guardrail Results | External Guardrail Services | Verdict Aggregation |
| Response Data | GHS | 回傳 `is_safe`、`categories` |
| Metrics Data | GHS | 監控判決、Latency、Timeout、Error、Skipped |
| Audit Data | GHS | 傳送 Audit Log Service |

**Data Handling Principles**

- GHS 不長期儲存 Request Content。
- Application Logs 不記錄敏感資訊、API Key 或 Bearer Token。
- Metrics 支援依 `app_name`、`app_type` 觀測不同 AP 的使用量及服務狀況。
- 所有 GHS Request 都必須產生 Audit Log。
- Monitor Mode 背景取得的判決與效能資訊亦須納入 Metrics 與 Audit Log。

### 5.5 Reliability & Deployment（可用性與部署）

**Deployment**

- GHS 部署於 Kubernetes。
- GHS 採 Stateless 設計，支援多 Pod 運行。
- 初期以人工調整 Pod 數量進行水平擴展。
- 透過 QPS、Guardrail Latency、Concurrency Limit 與負載測試評估所需 Pod 數量。

**Failure Handling**

| Failure Scenario | Handling |
|---|---|
| Guardrail Timeout / Error | Fail-open |
| Guardrail 回傳 Block | 依 AP Mode 回傳 Block 或 Safe |
| GHS 可處理的 Internal Error | HTTP 200，`is_safe: true` |
| GHS 無法回應 | Gateway Fail-open |
| Audit Log Service Failure | 不影響判決，記錄 Error Metrics |
| Monitor Mode 超過 Concurrency Limit | 允許 Skipped，記錄 Metrics 與 Audit Log |

**Reliability Principles**

- Guardrail 檢查必須受到 Overall Timeout 保護。
- GHS 同時執行的外部檢查不得超過 Concurrency Limit。
- Audit Log 不阻塞 Gateway Response

# GHS — Guardrail Hub Service

## 6. Technical Design

**Version:** v1.0  
**Status:** Finalized  
**Date:** 2026-11-14

### 6.1 Technology Stack（技術選型）

| Component | Technology |
|---|---|
| Runtime | Node.js + TypeScript |
| API Framework | Fastify |
| HTTP Client | Native Fetch / Undici |
| Configuration | Configuration Files + Kubernetes Secrets |
| Metrics | Prometheus |
| Deployment | Kubernetes |
| Database | None |

**Design Principles**

- GHS 採 Stateless Service 架構，支援多 Pod 部署。
- 使用成熟套件，避免自行開發基礎功能。
- 第一版不引入 Redis、NATS、Queue 或 Database。
- Configuration 由 GHS 管理，不建立獨立 Configuration Service。

### 6.2 Module Design（模組設計）

| Module | Responsibility |
|---|---|
| API Module | API Routing、API Key Authentication、Request Validation |
| Configuration Module | 管理 AP Configuration、Guardrail Configuration、Mode 與 Timeout |
| Orchestrator Module | 平行執行 Guardrail、Verdict Aggregation、Timeout、Fail-open 與 Concurrency Control |
| Guardrail Adapter Module | 對接各外部 Guardrail 自訂 API，處理 Request Mapping 與 Response Normalization |
| Observability Module | Metrics、Audit Log、Application Logging |

### 6.3 Interface Design（介面設計）

#### API Interface

| Item | Definition |
|---|---|
| Internal Endpoint | `POST /internal/{app_name}` |
| External Endpoint | `POST /external/{app_name}` |
| Request | OpenAI-compatible JSON |
| Response | `is_safe`、`categories` |
| Authentication | AP-specific API Key |

#### AP Configuration

每個 AP 需要以下設定：

| Field | Description |
|---|---|
| `app_name` | AP 名稱 |
| `app_type` | internal / external |
| `api_key` | AP 專屬 API Key，由 Secrets 管理 |
| `mode` | block / monitor |
| `timeout` | AP Overall Timeout |
| `guardrails` | 使用的外部 Guardrail 清單 |

**Rules**

- AP Configuration 使用設定檔管理。
- API Key 與外部 Guardrail Bearer Token 使用 Kubernetes Secrets。
- 不建立 AP Configuration Database。
- GHS 根據 API Path 識別 AP，並驗證其 API Key。

#### Guardrail Adapter Interface

每個 Guardrail 使用獨立 Adapter，負責：

1. 將 OpenAI-compatible Request 轉換為外部 Guardrail API 格式。
2. 呼叫外部 Guardrail Service。
3. 將外部判決統一轉換為 Allow / Block / Error。
4. 回傳 Categories 與 Latency。

Timeout、Cancelled、Skipped 由 Orchestrator 管理並記錄。

### 6.4 Core Logic（核心邏輯）

#### Standard Request Flow

1. API Module 接收 Gateway Request。
2. 根據 API Path 識別 AP 並驗證 API Key。
3. Configuration Module 提供 AP 設定。
4. Orchestrator 透過 Adapters 平行呼叫已選擇的 Guardrail。
5. Orchestrator 整合判決並依 AP Mode 回傳結果。
6. Observability Module 記錄 Metrics，並非阻塞地呼叫 Audit Log Service。

#### Verdict Handling

| Scenario | Block Mode | Monitor Mode |
|---|---|---|
| 任一 Guardrail Block | 立即回 Block | 立即回 Safe |
| 其他 Guardrail 未完成 | 取消檢查 | 背景繼續至完成或 Timeout |
| 全部 Guardrail Allow | Safe | Safe |
| Timeout / Error，無 Block | Safe | Safe |
| GHS 可處理的 Internal Error | HTTP 200 / Safe | HTTP 200 / Safe |

#### Timeout & Concurrency Control

- 每個 AP 獨立設定 Overall Timeout。
- 同一 Request 的所有 Guardrail 共用 Overall Deadline。
- Deadline 到期時停止等待，並取消未完成的檢查。
- **Concurrency Limit 採 Per Pod 設定。**
- 每個 Pod 獨立限制同時執行的外部 Guardrail 檢查數量。
- Monitor Mode 背景檢查同樣計入 Concurrency Limit。
- Monitor Mode 超過 Concurrency Limit 時允許 Skipped，並記錄 Metrics 與 Audit Log。

### 6.5 Error Handling（錯誤處理）

| Scenario | Handling |
|---|---|
| Invalid Request | HTTP 400 |
| Invalid API Key | HTTP 401 |
| Guardrail Timeout / Error | Fail-open |
| GHS Recoverable Internal Error | HTTP 200，`is_safe: true`，`categories: []` |
| Audit Log Service Error | 不影響 Gateway，記錄 Error Metrics |
| GHS 無回應 | Gateway Fail-open |

**Rules**

- 外部 Guardrail Timeout / Error 不回傳 HTTP 5xx。
- 可處理的 GHS Internal Error 統一採用 HTTP 200 + Fail-open。
- 已取得的明確 Block 判決，不被其他 Guardrail 的技術失敗覆蓋。
- Audit Log Service 故障不得影響 GHS 判決。

### 6.6 Observability Implementation

#### Metrics

GHS 必須提供：

- GHS Request Count / Latency。
- 各外部 Guardrail 的 Latency。
- Safe / Block Count。
- Timeout / Error / Skipped Count。
- Audit Log Error Count。
- 支援依 `app_name`、`app_type` 分析各 AP 的使用量與服務品質。

#### Application Logging

- 記錄必要的錯誤、執行狀態與 Debug 資訊。
- 不記錄敏感 Request Content、API Key、Bearer Token。
- 使用 Request ID 協助問題追蹤。

#### Audit Log

- 所有 GHS Request 都必須產生 Audit Event。
- GHS 以非阻塞方式呼叫 Audit Log Service。
- Monitor Mode 背景取得的 Guardrail 判決、Latency 與 Timeout 資訊亦須納入 Audit Log。
- Audit Log Service 故障不影響 Gateway Response。
- 第一版不提供 Audit Log 持久化重試保證。

### 6.7 Deployment（部署）

- GHS 使用 Container 部署至 Kubernetes。
- 採 Stateless Service，支援多 Pod。
- 初期透過人工調整 Replicas 進行水平擴展。
- Configuration Files 與 Kubernetes Secrets 管理 AP 與外部服務設定。
- 每個 Pod 使用獨立 Concurrency Limit。
- 透過 QPS、Guardrail Latency、Concurrency Limit 及負載測試估算所需 Pod 數量。
- 第一版不要求 Kubernetes HPA 自動擴展。

### 6.8 Technical Decisions Summary

| Decision | Selected Design |
|---|---|
| Runtime | Node.js + TypeScript |
| API Framework | Fastify |
| HTTP Client | Native Fetch / Undici |
| Architecture | Stateless Service |
| Deployment | Kubernetes |
| AP Configuration | Configuration Files + Kubernetes Secrets |
| Guardrail Integration | Independent Adapters |
| Execution | Parallel |
| Verdict | Any Block → Block |
| Block Mode | Early Block，取消未完成檢查 |
| Monitor Mode | Early Safe，其他檢查繼續至完成或 Timeout |
| Fail-open | Enabled |
| Timeout | AP-level Overall Timeout |
| Concurrency Limit | Per Pod |
| Metrics | Prometheus |
| Audit Log | Non-blocking |
| Persistent Storage | None |
| Scaling | Manual Pod Scaling |

**Core Design Principle**

GHS 必須維持簡單、低耦合、容易維護及擴展。

在具備 Guardrail 安全檢查能力的同時，盡可能降低對 LLM API Gateway 的效能與可用性影響。

# GHS — Guardrail Hub Service

## 7. Testing Strategy

**Version:** v1.1 Draft

### 7.1 Testing Goals

測試完成後，必須能回答以下問題：

| ID | 想得到的結果 | 驗證方式 |
|---|---|---|
| TG-01 | GHS 判決是否正確？ | Unit Test |
| TG-02 | Block / Monitor / Fail-open 是否符合規格？ | Integration Test |
| TG-03 | Gateway 是否能正常使用 GHS？ | E2E Test |
| TG-04 | Guardrail 很慢或故障，是否影響 Gateway？ | Failure Test |
| TG-05 | 單 Pod 能承受多少 QPS？ | Load Test |
| TG-06 | 多少流量需要增加 Pod？ | Capacity Test |
| TG-07 | Metrics、Audit 是否正確？ | Integration Test |

### 7.2 Functional & Failure Tests

使用 Vitest、Fastify Inject 與 Mock Guardrail。

| Test | Scenario | Expected Result |
|---|---|---|
| T-01 | 全部 Guardrail Allow | Safe |
| T-02 | Block Mode，任一 Block | 立即回 Block，取消其他檢查 |
| T-03 | Monitor Mode，任一 Block | 立即回 Safe，其他檢查繼續至完成或 Timeout |
| T-04 | Guardrail Timeout / Error | Fail-open，不覆蓋既有 Block |
| T-05 | GHS 可處理的 Internal Error | HTTP 200、is_safe=true |
| T-06 | 超過 Concurrency Limit | 不超過上限；Monitor 可 Skipped |
| T-07 | API Key 與 AP Path 不匹配 | HTTP 401 |
| T-08 | 不同 Guardrail API 格式 | Adapter 正確轉換 |
| T-09 | Audit Service 故障 | 不影響 Gateway，產生 Error Metric |
| T-10 | GHS 無法回應 | Gateway Fail-open |

每個測試除了確認 Response，也要驗證對應的 Metrics 與 Audit Event。

### 7.3 Load Test — 要得到什麼結果？

**測試目標：找出單 Pod 的實際處理能力與瓶頸，而不是只把 QPS 壓高。**

AI 撰寫可調整 QPS、Concurrency、測試時間、AP 與 Mock Guardrail 延遲的 Load Test Script，不使用 k6。

建議測試四個情境：

| Scenario | Mock Guardrail 行為 | 觀察重點 |
|---|---|---|
| Normal | 全部快速回 Allow | 基本 QPS 與 Latency |
| Slow | Guardrail 延遲增加 | Concurrency、Memory、Latency |
| Timeout | 部分 Guardrail 不回應 | Fail-open、Timeout、資源是否釋放 |
| Monitor | 提早收到 Block，其他檢查很慢 | 背景 Concurrency、Skipped、資源占用 |

每個情境逐步提高 QPS，觀察服務何時開始出現 Latency 明顯上升、Skipped 增加、CPU / Memory 飽和或 Pod Restart。

### 7.4 Load Test Report

AI 執行測試後，至少產生以下報告：

| 指標 | 目的 |
|---|---|
| Target / Actual QPS | 確認實際達到的吞吐量 |
| GHS P95 Latency | 確認 Gateway 等待時間 |
| Guardrail P95 Latency | 確認外部服務效能 |
| Timeout / Error / Skipped Rate | 確認服務品質與過載狀況 |
| Peak Active Concurrency | 確認是否接近上限 |
| Pod CPU / Memory | 找出資源瓶頸 |
| Pod Restarts | 確認服務穩定性 |

**最終要得到三個結論：**

1. 單 Pod 在指定測試情境下可穩定支援的 QPS。
2. 限制單 Pod Capacity 的主要因素。
3. 根據預期尖峰流量，建議部署多少 Pod，並保留適當容量餘裕。

### 7.5 Test Tools

| Purpose | Tool |
|---|---|
| Unit Test | Vitest |
| API / Integration Test | Fastify Inject + Mock HTTP |
| E2E Test | Gateway + GHS + Mock Guardrail |
| Load Test | AI 自行撰寫測試程式 |
| Capacity Observation | Prometheus + Grafana + Kubernetes Metrics |

不要求 CI 自動化，Developer 與 AI 負責執行測試、產生報告並驗收。

---

## 8. Operations & Observability

**Version:** v1.1 Draft

### 8.1 Grafana Dashboards

第一版只需要三個 Dashboard。

#### Dashboard 1 — GHS Service Overview

**目的：確認 GHS 是否健康、是否影響 Gateway。**

| Panel | 想看到什麼 |
|---|---|
| Request QPS | GHS 每秒處理多少 Request |
| GHS P50 / P95 / P99 Latency | Gateway 等待 GHS 多久 |
| Safe / Block Rate | 實際阻擋比例 |
| Fail-open Rate | 有多少請求因技術異常而放行 |
| Timeout / Error Rate | 是否大量出現外部服務故障 |
| Audit Failure | Audit Service 是否正常 |
| Pod Health | Pod 數量、Restart、CPU / Memory |

#### Dashboard 2 — AP & Guardrail Analysis

**目的：找出哪個 AP 用量高、哪個 Guardrail 品質不好。**

Dashboard 可依 `app_name`、`app_type`、`mode`、`guardrail` 篩選。

| Panel | 想看到什麼 |
|---|---|
| QPS by AP | 哪個 AP 使用量最高 |
| Block Rate by AP | 每個 AP 的違規偵測比例 |
| Guardrail Allow / Block Count | 每個 Guardrail 的原始判決分布 |
| Guardrail P95 Latency | 哪個 Guardrail 最慢 |
| Guardrail Timeout / Error Rate | 哪個 Guardrail 不穩定 |
| Monitor vs. Block | Monitor Mode 偵測到多少違規、Block Mode 實際阻擋多少 |

此 Dashboard 可協助評估是否將 AP 從 Monitor 切換成 Block Mode。

注意：Block Rate 高不一定代表誤判高。真正的 False Positive 仍需要人工或其他方式確認，不能只靠 Metrics 判斷。

#### Dashboard 3 — Pod Capacity & Load

**目的：決定是否要人工增加 Pod。**

| Panel | 想看到什麼 |
|---|---|
| QPS per Pod | 每個 Pod 實際處理量 |
| Active Concurrency | 目前外部 Guardrail 檢查數量 |
| Concurrency Limit | 目前設定的上限 |
| Skipped Rate | 是否因容量不足而略過檢查 |
| CPU / Memory per Pod | 是否接近資源限制 |
| Pod Restart Count | 是否出現資源耗盡或不穩定 |

搭配 Chapter 7 的 Load Test 結果，決定需要增加多少 Pod。

### 8.2 Prometheus Metrics — Developer 必須提供

以下使用實際可實作的 Metric Name。

| Metric Name | Type | Purpose |
|---|---|---|
| `ghs_requests_total` | Counter | GHS Request Count，依 Safe / Block 區分 |
| `ghs_request_duration_seconds` | Histogram | GHS Request Latency |
| `ghs_guardrail_checks_total` | Counter | 各 Guardrail 的 Allow / Block / Timeout / Error / Cancelled / Skipped |
| `ghs_guardrail_duration_seconds` | Histogram | 外部 Guardrail Latency |
| `ghs_guardrail_inflight` | Gauge | 目前執行中的 Guardrail 檢查數 |
| `ghs_guardrail_concurrency_limit` | Gauge | Per-Pod Concurrency Limit |
| `ghs_fail_open_total` | Counter | 因技術異常而放行的請求數 |
| `ghs_audit_events_total` | Counter | Audit 傳送成功 / 失敗數 |

**必要 Labels**

- `app_name`
- `app_type`
- `mode`
- `guardrail`（Guardrail 相關 Metrics）
- `result` / `reason`（依 Metric 類型使用）

Kubernetes 既有 Metrics 提供 Pod CPU、Memory、Restart 等資料，不需要 GHS 重複實作。

禁止將 Request ID、Prompt、User ID、API Key、Bearer Token 放進 Metrics Labels。

**重要區別：**

- `ghs_requests_total`：Gateway 最終收到 Safe 還是 Block。
- `ghs_guardrail_checks_total`：各 Guardrail 真正判定 Allow、Block，或發生 Timeout、Error。
- Monitor Mode 背景檢查結果必須計入後者。

### 8.3 Gateway 必須配合的 Metrics

GHS 無法知道自己 Pod 已當機時，Gateway 發生多少次 Fail-open。

因此 Gateway 端至少需要提供：

| Metric | Purpose |
|---|---|
| GHS Call Count | Gateway 呼叫 GHS 次數 |
| GHS Call Timeout / Error Count | GHS 無回應或連線失敗次數 |
| GHS Call Latency | Gateway 實際等待時間 |

這部分由 Gateway 負責，不是 GHS 的 Metrics。

### 8.4 Logging & Tracing

**Application Logging**

- 使用 Fastify Structured JSON Logs。
- 記錄 Request ID、AP、Guardrail、Error Type。
- 支援 INFO / WARN / ERROR / DEBUG。
- 不記錄 Prompt、Response Content、API Key、Bearer Token。
- DEBUG 也不能包含敏感資訊。

**Tracing**

- 使用 `X-Request-ID` 關聯 Gateway、GHS、Audit Log。
- 支援接收 `traceparent`。
- 第一版不額外建立 Distributed Tracing 平台。

### 8.5 Deployment & Capacity Management

- GHS 部署於 Kubernetes，採 Stateless 多 Pod。
- AP Configuration 使用設定檔與 Kubernetes Secrets。
- Concurrency Limit 為 Per Pod。
- 初期人工調整 Pod Replicas，不使用 HPA。
- 依 Chapter 7 的負載測試結果建立單 Pod Capacity 基準。
- 人工擴 Pod 後，透過 Dashboard 3 確認流量與資源使用是否改善。

### 8.6 Alerting

第一版先建立五類告警，不設定未經驗證的固定門檻。

| Alert | Trigger |
|---|---|
| GHS Unavailable | Pod 不健康或 Gateway 大量呼叫失敗 |
| Guardrail Timeout / Error | Timeout / Error Rate 異常升高 |
| High Concurrency / Skipped | 接近上限或持續出現 Skipped |
| Audit Failure | Audit Log Service 傳送持續失敗 |
| Pod Resource Pressure | CPU / Memory 過高或 Pod Restart |

Alert Threshold 待 Load Test 與實際運作數據確認後設定。