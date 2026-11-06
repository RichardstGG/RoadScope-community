# 貢獻指南 / Contributing

## 翻譯

- 翻譯檔是 Flutter 的 ARB（JSON）。每個語言一個檔案，鍵名和繁體中文範本相同。
- **只改值，不改鍵名**，也不要刪掉 `{placeholder}` 這類佔位符；佔位符的名稱和數量要和範本一致。
- 繁體中文是範本，由 App 主程式維護，請不要在這裡修改；發現中文有錯請開 issue。
- 標成 `machine-draft` 的檔案是機器翻譯初稿，最需要母語者校對。
- 一個 PR 只改一個語言比較好審。PR 說明請寫你的母語程度與改了哪些地方。
- 送出的翻譯會由同步腳本檢查後拉回 App，下一個版本才會出現。

## 格式說明

格式說明的正式版本在 App 主程式的 `contracts/`，這裡是同步過來的副本。發現說明不清楚或有錯，請開 issue，不要直接改副本。

## 回報問題

請開 [issue](https://github.com/RichardstGG/RoadScope-community/issues)。App 的錯誤回報也可以開在這裡，請附上 App 版本與手機型號；**不要貼出含有位置的軌跡檔**。

## 授權

送出 PR 即表示你同意你的貢獻以 [CC BY 4.0](LICENSE) 授權。

---

## Translations (English summary)

- Files are Flutter ARB (JSON), one per language, with the same keys as the Traditional Chinese template.
- Change values only. Keep every `{placeholder}` with the same name and count.
- The Traditional Chinese template is maintained in the app repository; report mistakes as issues.
- Files marked `machine-draft` are machine translations that need native-speaker review.
- Please keep one language per pull request. Contributions are licensed under CC BY 4.0.
- Do not attach track files that contain locations to issues.
