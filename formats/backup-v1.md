# Backup v1

`schemaVersion: 1`。這是使用者自己的雲端硬碟中的本機檔案格式，不是 HTTP／WebSocket 協定，資料不經過 RoadScope 伺服器。依據[決策 0009](../../../docs/decisions/0009-ads-pro-backup.md) M7、M8、M11、M16、C11–C15、C18b。本文件定義消費端規則；四份 JSON Schema 定義型別，驗證器補足唯一性及既有 route／timing-result 的語義檢查。

## 1. 檔案配置與共通規則

```text
RoadScope/<裝置資料夾>/
  device.json
  manifest.json
  state.json
  recordings/<recordingId>.ndjson      # location-log v1，rawJson 原樣輸出，上傳後不改
  recordings/<recordingId>.meta.json   # 可覆蓋
  photos/<vehicleId>.jpg
```

- JSON 為 UTF-8 單一物件；四種文件（含 meta）都必填整數 `schemaVersion: 1`。所有整數限 JavaScript 安全整數範圍，大小與數量不得負值。
- ID 是區分大小寫的非空不透明字串，不使用顯示名稱判斷身分。檔名中的 ID 以 UTF-8 百分比編碼為單一檔名片段（等同 `encodeURIComponent`）；不得將 ID 當路徑，或解碼後拼接到任意本機路徑。一般 UUID 不需要編碼。
- 本契約的 `*AtUtc` 使用真正存在的 UTC 日曆時間：`YYYY-MM-DDTHH:mm:ss[.小數]Z`，不接受時區位移、閏秒或不存在的日期。Schema 的 `date-time` format 必須啟用驗證，不能只檢查字串外形。內嵌 TimingResult 的時間仍依原契約解讀。
- 雜湊是檔案**實際完整位元組**的 SHA-256，小寫 64 位十六進位；大小是位元組數。JSON 不採 canonical JSON，不可重排／重新序列化後拿舊雜湊比對。照片 hash 同樣針對原始 JPEG 位元組。
- `.ndjson` 直接遵守 [location-log v1](../../location-log/v1/README.md)，不另造樣本格式。從 `RawLocationSamples.rawJson` 原樣輸出每行（不刪未知欄位、不改數字／null），以 LF 分隔並以 LF 結尾；保留可用的原始事件。檔中紀錄必須屬於對應的 `recordingId`。首次上傳成功後，該檔案與其 hash／大小永遠不變，不能因重新匯出順序或換行差異而覆寫。
- v1 只定義未壓縮 `.ndjson`。若之後加入 `.ndjson.gz`，須升版或新增明確的編碼、路徑與雜湊範圍欄位；不得沿用 v1 路徑偷偷壓縮。

## 2. device.json

Schema：[`device.schema.json`](device.schema.json)。所有下列欄位必填。

| 欄位 | 意義 |
|---|---|
| `schemaVersion` | `1` |
| `deviceId` | 安裝時隨機產生並持久保存的裝置識別值（建議 UUID v4）；不能從型號、帳號、硬體序號推導 |
| `displayName` | 1–64 個 Unicode code points，非 UTF-16 code units；使用者可改名 |
| `createdAtUtc` | 此資料夾首次建立時間；接續資料夾不重設 |
| `appVersion` | 最近寫入 device.json 的 App 版本，非空字串 |

第一次備份預設顯示手機型號，允許使用者改名；資料夾同名時加 `-2`（之後遞增）。資料夾名稱與 `displayName` 可修改，仍以 `deviceId` 識別歸屬，不因改名建立另一份備份。

**單一寫入者（C12）**：每次備份與發布清單前都讀取 `device.json`。`deviceId` 不是自己時，拒絕寫入這個資料夾的任何檔案，提示建立新資料夾。缺失、讀取失敗或不支援版本時也不得自行認領非空資料夾。

唯一的接管流程：新手機完成還原後，由使用者明確選擇「之後備份到這個資料夾」（另可選建立新資料夾，預設接續）。接續時把 `device.json.deviceId` 改成自己的，保留建立時間與未知欄位；舊手機下次讀到不符時拒寫。此操作是明確的所有權轉移，不能在普通備份碰到 ID 不符時自動執行。新 manifest 的 `deviceId` 跟隨新寫入者；舊 manifest 可以仍記著前任 ID，取聯集時不得因此丟棄其項目。這是合作式單一寫入者協定，不是 Drive 存取權限或跨裝置同步鎖。

## 3. manifest.json

Schema：[`manifest.schema.json`](manifest.schema.json)。必填 `schemaVersion`、`deviceId`（此次發布者，必須符合目前 device.json）、`updatedAtUtc`、`recordings`、`photos`；陣列可為空。

每個 `recordings[]` 項目必填：

| 欄位 | 意義 |
|---|---|
| `recordingId` | 對應檔名與 meta 的 ID；整份陣列唯一 |
| `samplesSha256` | NDJSON 的 SHA-256，或 `null` |
| `samplesBytes` | NDJSON 位元組數，或 `null`；與 hash 必須同時為 null／同時有值 |
| `metaSha256` | meta.json 的 SHA-256，永遠不可為 null |

兩個 `samples*` 都為 null 表示定位點已在本機刪除，只能備份剩下的中繼資料；不是空檔、不是刪雲端的指令。這種項目仍必須上傳 meta。之後若有定位點，可由 null 補為實際 hash／大小；**一旦有值就不得改變，也不得改回 null**。本機封存後，雲端已上傳的定位點仍永遠保留。

每個 `photos[]` 項目必填 `vehicleId`（整份陣列唯一）與 `sha256`。JPEG 位於 `photos/<vehicleId>.jpg`，可更新。車輛沒有照片時不新增項目；已上傳照片及其清單項目保留，不能因本機移除照片或軟刪車輛而從雲端刪除。

**只增不減（C15）**：寫 `manifest.json` 前先讀雲端現有清單，依 `recordingId` 取聯集。保留僅雲端存在、僅本機存在與雙方都有的項目；不得以本機清單直接覆蓋雲端清單。雙方都有時，以雲端已存在的非 null `samples*` 為準，`metaSha256` 可改為此次成功上傳的 meta hash；雜湊或大小衝突時停止並回報，不覆寫定位點。`photos` 同樣依 `vehicleId` 保留舊項目並加入新項目，成功更新照片時才更新 hash。未知欄位必須保留，已知欄位的更新不得順帶刪除擴充資訊。

## 4. recordings/*.meta.json

Schema：[`recording-meta.schema.json`](recording-meta.schema.json)。以下全部必填，可空值以 `null` 明示：

| 欄位 | 規則 |
|---|---|
| `schemaVersion`、`recordingId` | `1`；與檔名／manifest 相符 |
| `kind` | `timing` 或 `free` |
| `status` | 只允許 `finished`／`interrupted`；不得備份仍在錄製的 `recording` |
| `createdAtUtc`、`endedAtUtc` | 開始時間；結束時間可 null（例如中斷時未知），不以 0 代替 |
| `vehicleId`、`mergedIntoId` | 非空 ID 或 null；合併目標不得是自己 |
| `placeName`、`note` | 字串或 null |
| `distanceM` | 有限非負公尺數或 null（未知） |
| `samplesArchived` | 布林，表示匯出時本機是否已清除定位點、只剩封存中繼資料；不是雲端刪除旗標 |
| `timingResults` | 完整 TimingResult 陣列，可為空；不另寫結果 schema |

`timingResults[]` 直接 `$ref` 到 [timing-result v1](../../timing-result/v1/result.schema.json)，同時沿用其語義驗證；每項 `recordingId` 必須符合 meta。每組 `(routeId, routeVersion, algorithmVersion)` 唯一。還原時直接使用保存的計時結果，**不重算**。需要核對路線參照時，使用 state 中相同 ID＋版本的路線快照，沿用原有 timing-result 驗證器的 `--route`。

`mergedIntoId` 保留原紀錄的合併關係，不產生新的 NDJSON 或新紀錄 ID。跨紀錄參照可能因只還原部分資料而暫時找不到，不得丟棄此 ID。`samplesArchived: true` 與 manifest 的非 null 樣本 hash 可以同時成立：前者是本機狀態，後者表示雲端仍有不可變定位點。

## 5. state.json

Schema：[`state.schema.json`](state.schema.json)。必填 `schemaVersion`、`exportedAtUtc`、`vehicles`、`currentVehicleId`、`routes`、`appSettings`。

- `vehicles[]`：每台車必填 `vehicleId`、`name`、`brand`、`model`、`displacementCc`、`modelYear`、`category`、`createdAtUtc`、`deletedAtUtc`。ID 唯一，建立時間必填；其餘資料可 null。排氣量／年份為非負安全整數。category 使用現有值 `bicycle`、`motorcycle`、`heavyMotorcycle`、`car`、`truck`、`racing`、`other` 或 null。軟刪除車輛仍保留，`deletedAtUtc` 為刪除時間，未刪為 null。照片以 manifest／vehicleId 關聯，不匯出本機 `photoFile` 絕對路徑。
- `currentVehicleId`：目前選取的未軟刪車輛 ID，必須存在於這份 vehicles 中，無選取時為 null。
- `routes[]`：直接 `$ref` 到 [route v1 路線快照](../../route/v1/route.schema.json)，在快照物件上加必填非空白 `name`。不包第二層、不複製線的 schema；同時沿用 route 語義檢查。同一 `(routeId, routeVersion)` 唯一；相同 routeId 的各版本共用同一名稱。
- `appSettings`：可為空物件；**僅允許下表列出的鍵**。不備份更新檢查時間、權限、帳號工作階段、Google／RoadScope token、裝置路徑或其他裝置相關設定。

| appSettings 鍵 | 型別與語義 |
|---|---|
| `language_preference` | 非空語言標籤字串（例如 `zh-TW`、`en`）；null 表跟隨系統。鍵缺少表示未匯出；不認識的語言值保留，顯示時回退系統語言 |
| `free_recording_speed_hidden` | 布林，自由紀錄隱藏速度的使用者偏好 |

設定白名單是「未知欄位必須容忍」的唯一例外：未列出的設定鍵必須拒絕，避免誤備份裝置狀態或憑證。增加設定鍵需要契約 PR；為讓舊版仍可讀，需升 major 或協調消費端支援後再輸出。

**封存與統計（C18、C18b）**：v1 不另存一份可能與紀錄重複計數的彙總。所有已清定位點的紀錄仍以獨立 meta＋manifest 項目保留（從未上傳定位點者使用 null 樣本欄位），里程與完整 timingResults 是重建封存貢獻的來源。這要求匯出端包含所有封存紀錄的中繼資料，不可只遍歷還有定位點的紀錄。B-2／B-3 必須保留這些逐筆資料；此 issue 不定義內部資料表結構。

## 6. 寫入、失敗與續傳

1. 確認 device.json 的寫入者；先完成本機快照與檔案驗證，不匯出進行中的紀錄。
2. 先傳紀錄 NDJSON、meta 與照片。每份檔案上傳成功並核對 hash／大小後才可被新 manifest 引用。NDJSON 已存在時驗證後沿用，不覆寫。
3. 全部檔案成功才重新讀雲端 manifest，取聯集並再次檢查寫入者；先寫 `manifest.json`，再寫 `state.json`。任一檔案失敗時，不發布此次新 manifest／state；不能以讀取失敗當成空清單。
4. 中斷後下次重讀雲端清單，從缺少的檔案開始；清單外的孤立檔案可檢查後沿用。備份不刪除雲端任何檔案；使用者若要清雲端，自行在雲端硬碟操作（M16）。

Drive 多檔案不是交易：meta／照片可覆蓋，或 manifest 已寫但 state 尚未寫時中斷，讀取端可能暫時看到 hash 不符或不同批次的檔案。還原前必須檢查存在性、hash、大小與 ID 對應；不符即停止該次還原並提示重試，不能匯入損壞／半批資料、或把缺檔當成刪除命令。單文件驗證器不讀整個資料夾，也不替傳輸層驗證實際檔案 hash、JPEG 編碼或跨檔 ID。

## 7. 還原、相容與遷移

還原可選任何裝置資料夾，不要求 deviceId 等於自己。兩種範圍：全部還原，或只還原路線、車庫、設定與統計（不下載定位點）。規則一律**只補缺的、不覆蓋**（C13）：

- 紀錄以 recordingId、車輛（含已軟刪）以 vehicleId、路線快照以 `(routeId, routeVersion)`、路線名稱以 routeId 判斷；手機已存在者保留本機內容。照片也只補缺的，不蓋掉本機版本。
- currentVehicleId 只在本機尚未設定時套用，且套用後仍須指向未軟刪車輛；App 設定只在使用者尚未改過時套用。不能用恰好等於預設值推斷「從未改過」。
- 全部還原遇到 null 樣本 hash 的項目只能還原中繼資料／封存，不能捏造定位點。若本機已有同 ID 的封存中繼資料而尚無樣本，補回缺少的樣本仍不覆蓋本機編輯。
- 兩種範圍都讀取 manifest 的**全部 meta**，包括雲端獨有項目；未還原成完整紀錄者，以其 distanceM 與 timingResults 合併到本機封存（C18b）。依 recordingId 去重，不能只讀 state 或只計算 samplesArchived 為 true 的項目。
- 完整紀錄補回時，移除同 recordingId 的封存貢獻，改由現有紀錄計算（C18）；同一筆不得同時累加封存與現有紀錄。最佳圈與分段依 routeId＋routeVersion＋algorithmVersion 分組，保留合併關係，避免改變既有統計語義。

除 appSettings 的白名單之外，所有物件的未知欄位皆需保留其 JSON 值，讀取、保存、更新與再匯出不得報錯或丟棄。內嵌 route／timing-result 的未知旗標與欄位仍遵循各自契約。未知欄位不能改變已知 v1 欄位的語義。

任何文件（含內嵌快照／結果）的 schemaVersion 比自己新時，**拒絕整次還原並提示更新 App**；不得先匯入部分資料或降版解讀。缺版本、錯誤型別與不合法已知欄位同樣拒絕。v1 是新契約，沒有舊 backup 版本需遷移，不改既有 location-log、route 或 timing-result；未來破壞性改版須另開版本，不能覆寫 v1 規則。

## 8. 工具與 fixtures

```bash
node contracts/tools/validate-backup.mjs device [--json] [--device-id <本機ID>] <device.json> [...]
node contracts/tools/validate-backup.mjs manifest [--json] [--previous <雲端manifest.json>] <manifest.json> [...]
node contracts/tools/validate-backup.mjs recording-meta|state [--json] <file.json> [...]
node contracts/tools/validate-backup.mjs samples [--json] <file.ndjson> [...]
npm test --prefix contracts/tools
```

`--device-id` 是拒寫規則檢查；不提供時僅驗證文件（還原可讀其他裝置）。`--previous` 檢查新 manifest 有保留全部舊項目，以及已上傳的樣本 hash／大小不可改；不替 B-3 建立聯集，也不授予寫入權。接管後新 manifest 的 deviceId 可改變，寫入權仍以當下 device.json 為準。`samples` 直接呼叫現有 `validateLocationLog`，不另寫 NDJSON 規則。

CLI 結束碼：0 通過，1 JSON／schema／語義驗證失敗，2 用法錯誤，3 執行失敗，4 不支援的整數 schemaVersion（NDJSON 沿用 location-log 的結果）。多檔時 4 優先於 1；讀檔失敗為 3。驗證器不改寫輸入；通過不代表完成 Drive 傳輸、還原或實機驗收。

`testdata/contracts/backup/v1/` 的 valid／invalid 是獨立文件案例，檔名前綴標示 schema，invalid 的 `.expected` 指定必要錯誤碼；範例 hash 為合成值，不是一份可傳輸的完整資料夾。NDJSON 案例沿用 location-log 的原始合成資料。`scenarios/` 包含雲端多／本機多／雙方都有、null 補為有值的聯集前後對照，以及移除／改寫樣本拒絕和裝置接管前後的寫入者檢查；測試驗證對照符合契約，不實作匯出／匯入或網路傳輸。
