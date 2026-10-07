# Entity / Value Failure Monitor

Home Assistant blueprint: detect stale sensor values and offline (unavailable/unknown) entities, with periodic reminders.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

[English](#english) | [繁體中文](#繁體中文)

---

<a id="english"></a>
## English

### What is this

A Home Assistant automation blueprint that watches a configurable set of entities and notifies you when something fails silently:

- **Stale values** — a `sensor` entity whose value has not updated for longer than the staleness threshold
- **Offline entities** — any entity whose state stays `unavailable` / `unknown` longer than the offline threshold (switches, lights, appliances, …)

While a fault persists, reminders are re-sent at the reminder interval. Once the entity recovers, the timer resets automatically. Tapping the notification opens the entity's detail page.

### Add Blueprint

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2FNyquist1992%2Fentity-value-failure-monitor%2Fmain%2Fentity_value_failure_monitor.yaml)

Or manually copy `entity_value_failure_monitor.yaml` into `<config>/blueprints/automation/` and reload automations.

### How it works

1. A time-pattern trigger runs the check at the configured interval.
2. The monitored scope is resolved from labels, areas, floors and individually selected entities (union, deduplicated).
3. For each entity, the time since `last_updated` is compared against the applicable threshold:
   - state `unavailable` / `unknown` → offline threshold (all domains)
   - any other state → staleness threshold (`sensor` entities only; a switch sitting at `off` is normal)
4. A notification fires on the first threshold crossing, then again every reminder interval while the fault persists (a modulo window keeps the polling quiet in between).

### Configuration

| Input / 參數 | Description / 說明 |
|---|---|
| Labels / Areas / Floors / Additional entities | Monitored scope (mixed freely, union) |
| device_class filter | Applies to staleness detection only; offline detection is never filtered |
| Staleness threshold (minutes) | Sensor value not updated this long → stale (default 30) |
| Offline threshold (minutes) | `unavailable`/`unknown` this long → offline (default 5) |
| Reminder interval (hours) | Re-notify period while a fault persists (default 12) |
| Check interval | Polling period; bounds the notification delay (default every 5 min) |
| Notification action | Fully customizable; template variables: `stuck_entity_name`, `stuck_entity_id`, `stuck_minutes`, `stuck_value`, `stuck_type` (`stale`/`offline`) |

### Notes

- Requires Home Assistant 2024.10+ (uses the modern `trigger:` / `action:` syntax).
- Blueprint updates propagate to automations created from it on reload — inputs you configured are kept.
- The automation runs in `single` mode; with a very large scope, raise the check interval.
- The default notification uses `notify.notify`; select your own notify service in the notification action.

### License

MIT License — see [LICENSE](LICENSE).

---

<a id="繁體中文"></a>
## 繁體中文

### 這是什麼

Home Assistant 自動化藍圖：監控一組可自由設定的實體，於「靜默故障」發生時發出通知。

- **數值停滯** — `sensor` 實體的數值超過停滯門檻未更新
- **設備離線** — 任何實體的狀態為 `unavailable` / `unknown` 且超過離線門檻（開關、燈具、家電等皆適用）

故障持續期間會依提醒間隔重複提醒，實體恢復後自動重新計算。點擊通知可直接跳轉至該實體的詳情頁。

### 新增藍圖

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2FNyquist1992%2Fentity-value-failure-monitor%2Fmain%2Fentity_value_failure_monitor.yaml)

或手動將 `entity_value_failure_monitor.yaml` 複製到 `<config>/blueprints/automation/` 後重新載入自動化。

### 運作原理

1. 依設定的巡檢週期以 time pattern 觸發檢查。
2. 監控範圍由標籤、區域、樓層與指定實體取聯集（去重）。
3. 逐一比對 `last_updated` 與適用門檻：
   - 狀態為 `unavailable` / `unknown` → 離線門檻（不限 domain）
   - 其他狀態 → 停滯門檻（僅 `sensor`；開關長期維持 `off` 屬正常）
4. 首次越過門檻即發通知，之後每隔提醒間隔再提醒一次（以取餘數視窗讓其餘巡檢保持安靜）。

### 設定參數

| Input / 參數 | Description / 說明 |
|---|---|
| 標籤／區域／樓層／附加實體 | 監控範圍（可自由混用，取聯集） |
| device_class 篩選 | 僅作用於停滯判定；離線判定不受篩選 |
| 停滯門檻（分鐘） | sensor 數值逾時未更新即判定停滯（預設 30） |
| 離線門檻（分鐘） | `unavailable`/`unknown` 持續達門檻即判定離線（預設 5） |
| 重複提醒間隔（小時） | 故障持續期間的提醒週期（預設 12） |
| 巡檢週期 | 檢查間隔，決定通知的最大延遲（預設每 5 分鐘） |
| 通知動作 | 可完全自訂；模板變數：`stuck_entity_name`、`stuck_entity_id`、`stuck_minutes`、`stuck_value`、`stuck_type`（`stale`/`offline`） |

### 注意事項

- 需要 Home Assistant 2024.10+（使用新版 `trigger:` / `action:` 語法）。
- 藍圖更新會在重新載入後套用到由它建立的自動化，已設定的輸入值會保留。
- 自動化為 `single` 模式；監控範圍很大時建議放寬巡檢週期。
- 預設通知使用 `notify.notify`，請在通知動作中改選自己的 notify 服務。

### License

MIT License — 見 [LICENSE](LICENSE).
