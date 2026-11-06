---
title: RoadScope 隱私權政策 / Privacy Policy
permalink: /privacy/
---

# RoadScope 隱私權政策

生效日期：2026 年 11 月 6 日

RoadScope（以下稱「本 App」）由個人開發者在台灣開發與營運。本 App 採**本機優先**設計：記錄、計時與統計都在你的手機上完成，不需要帳號或網路。本政策說明本 App 在哪些情況下會處理你的資料，以及資料會送到哪裡。

## 1. 只存在你手機上的資料

以下資料只保存在你的手機上，本 App 不會把它們傳到開發者的伺服器：

- 行車紀錄：GPS 位置、速度、時間，以及手機動態感測器資料（例如傾角）。
- 計時結果、路線與計時線、車輛資料與車輛照片、App 設定。

你可以在 App 內刪除紀錄。只有當你自己選擇匯出（例如 GPX 或診斷檔）並分享給他人時，這些資料才會離開手機。

## 2. 帳號與車隊（選用）

只有使用車隊功能時才需要登入。登入後，開發者的伺服器會處理：

- **Email**：用來寄送登入驗證碼。驗證碼信透過第三方寄信服務（Resend）寄出。Email 不會顯示給隊友。
- **暱稱與頭像縮圖**：顯示給同一隊的成員。頭像上傳前會去除 EXIF 等中繼資料（包含拍攝位置）。
- **車隊、成員關係與加入申請**。
- **即時位置**：只有在你按下「上線」之後，才會把你的位置傳給伺服器並轉送給同一隊、同時在線的隊友。即時位置只保存在伺服器記憶體中，不寫入資料庫；下線後不再傳送。伺服器日誌不記錄完整座標或登入憑證。
- **IP 位址**：用於防止濫用（例如限制驗證碼與加入車隊的嘗試次數）。

車隊連續 12 小時沒有人上線時，伺服器會清除該隊的頭像與邀請碼暫存。你可以隨時在 App 的「帳號」分頁刪除帳號；刪除後，你的帳號資料、頭像與成員資料會從伺服器移除（若你是隊長，隊長身分會移交給其他成員，沒有其他成員時車隊會解散）。

## 3. Google 雲端硬碟備份（選用）

備份功能把你的紀錄與設定備份到**你自己的** Google 雲端硬碟，存在 `RoadScope` 資料夾中。

- 本 App 只申請 `drive.file` 權限，只能存取**本 App 自己建立**的檔案，看不到你雲端硬碟中的其他檔案。
- 備份資料直接在你的手機與 Google 之間傳輸，**不經過開發者的伺服器**，開發者也無法讀取。
- 備份不需要 RoadScope 帳號。你可以隨時到 [Google 帳戶的第三方連線設定](https://myaccount.google.com/connections) 撤銷授權，並在雲端硬碟自行刪除備份檔案。

本 App 對於從 Google API 取得之資訊的使用與傳輸，均遵守 [Google API 服務使用者資料政策](https://developers.google.com/terms/api-services-user-data-policy)，包含其中的「有限使用」（Limited Use）規定。本 App 不會把這些資料用於廣告，也不會轉移或出售給任何人。

## 4. 廣告

免費版會在畫面底部顯示 Google AdMob 橫幅廣告；Pro 版不顯示廣告。AdMob 可能依 [Google 的隱私權政策](https://policies.google.com/technologies/ads)收集裝置資訊、廣告 ID 與 IP 位址。本 App **不會把你的位置或行車紀錄提供給廣告 SDK**。

## 5. 其他第三方服務

- **Google 地圖（Android）**：顯示地圖時，Google 會收到地圖範圍的請求，適用 [Google 隱私權政策](https://policies.google.com/privacy)。
- **更新檢查（從 GitHub 安裝的版本）**：App 會從 GitHub 讀取最新版本資訊，GitHub 會收到你的 IP 位址，適用 [GitHub 隱私權聲明](https://docs.github.com/site-policy/privacy-policies/github-general-privacy-statement)。

本 App 不使用分析或追蹤 SDK，也不出售任何個人資料。

## 6. 兒童

本 App 不以 13 歲以下兒童為對象，也不會刻意收集兒童的個人資料。

## 7. 政策變更與聯絡方式

本政策若有變更，會更新本頁並修改上方的生效日期。對本政策有任何問題，請在 [GitHub](https://github.com/RichardstGG/RoadScope-community/issues) 開 issue 聯絡開發者。

---

# RoadScope Privacy Policy (English)

Effective date: November 6, 2026

RoadScope ("the app") is developed and operated by an individual developer in Taiwan. The app is **local-first**: recording, timing and statistics run on your phone and need no account or network. If this English version differs from the Chinese version above, the Chinese version prevails.

## 1. Data that stays on your phone

Ride recordings (GPS position, speed, time and motion-sensor data such as lean angle), timing results, routes and timing lines, vehicles and vehicle photos, and app settings are stored only on your phone and are not sent to the developer's servers. They leave the phone only when you choose to export and share them (for example a GPX or diagnostics file).

## 2. Account and team (optional)

Sign-in is needed only for team features. When signed in, the developer's server processes:

- **Email**, to send sign-in codes through a third-party email service (Resend). Your email is never shown to teammates.
- **Nickname and avatar thumbnail**, shown to members of your teams. EXIF and other metadata (including photo location) is removed before upload.
- **Teams, memberships and join requests.**
- **Live location**, only after you tap "Go online". It is relayed to teammates who are online in the same team, kept in server memory only and never written to the database. Server logs do not record full coordinates or credentials.
- **IP address**, used to prevent abuse (rate limits on sign-in codes and joining teams).

When nobody in a team has been online for 12 hours, the server clears that team's cached avatars and invite codes. You can delete your account at any time from the Account tab; your account data, avatars and memberships are then removed from the server (captaincy passes to another member, or the team is dissolved if no one else remains).

## 3. Google Drive backup (optional)

Backup saves your records and settings to **your own** Google Drive, in a `RoadScope` folder.

- The app requests only the `drive.file` scope, so it can access only files the app itself created and cannot see anything else in your Drive.
- Backup data travels directly between your phone and Google and **never passes through the developer's servers**; the developer cannot read it.
- Backup does not need a RoadScope account. You can revoke access at any time in [your Google Account's third-party connections](https://myaccount.google.com/connections) and delete the backup files from Drive yourself.

RoadScope's use and transfer of information received from Google APIs will adhere to the [Google API Services User Data Policy](https://developers.google.com/terms/api-services-user-data-policy), including the Limited Use requirements. This data is never used for advertising and is never transferred or sold to anyone.

## 4. Advertising

The free version shows a Google AdMob banner at the bottom of the screen; Pro shows no ads. AdMob may collect device information, the advertising ID and IP address under [Google's policies](https://policies.google.com/technologies/ads). The app **never provides your location or ride recordings to the ad SDK**.

## 5. Other third-party services

- **Google Maps (Android)**: when a map is shown, Google receives requests for the map area, under the [Google Privacy Policy](https://policies.google.com/privacy).
- **Update check (versions installed from GitHub)**: the app reads the latest version information from GitHub, which receives your IP address, under the [GitHub Privacy Statement](https://docs.github.com/site-policy/privacy-policies/github-general-privacy-statement).

The app uses no analytics or tracking SDKs and does not sell any personal data.

## 6. Children

The app is not directed to children under 13 and does not knowingly collect their personal data.

## 7. Changes and contact

If this policy changes, this page and the effective date above will be updated. For questions, please open an issue on [GitHub](https://github.com/RichardstGG/RoadScope-community/issues).
