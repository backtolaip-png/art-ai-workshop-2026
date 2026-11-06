# 課堂問題清單

建立日期：2026-11-05（課前環境與目錄第一次整合測試）

---

## Q1：終端機的 `git` 指令無法使用

- **現象**：在終端機執行 `git --version` 時，跳出 xcode-select 提示：
  `No developer tools were found, requesting install.`
- **已確認的事實**：
  - 系統的 `/usr/bin/git` 只是個「快捷入口」，本體（Xcode 命令列開發者工具）尚未安裝。
  - GitHub Desktop 內附 git 2.56.0，路徑在
    `/Applications/GitHub Desktop.app/Contents/Resources/app/git/bin/git`，
    但沒有加進終端機的 PATH，所以終端機叫不到它。
  - `~/.gitconfig` 之前就設定好了（user: backtolaip-png，並啟用 git-lfs），代表這台機器曾經有可用的 Git。
- **想問**：補救方式哪一種比較好？
  (a) 安裝 Xcode 命令列工具（檔案較大）；(b) 把 GitHub Desktop 內附的 git 加進 PATH；(c) 用 Homebrew 裝 git？
  課上希望統一用哪種，避免大家的環境不一致。
- **✅ 2026-11-05 更新：找到免安裝的繞道**：用 GitHub Desktop 內附 git 時加上兩個環境變數即可完整可用（clone 已實測成功）：
  ```
  export GIT_EXEC_PATH="/Applications/GitHub Desktop.app/Contents/Resources/app/git/libexec/git-core"
  export GIT_TEMPLATE_DIR="/Applications/GitHub Desktop.app/Contents/Resources/app/git/share/git-core/templates"
  ```
  原理：內附 git 的「上網助手」二進位檔其實都在 `libexec/git-core`，只是編譯時的前綴路徑寫壞了（`//libexec/git-core`），用變數把正確路徑餵給它就能繞過缺少的 Xcode 工具。
  **待課堂確認**：仍想統一大家的方式（候選 a/b/c 不變）。
  > ⚠️ 注意：光把 `bin/` 加進 PATH 不夠——那條路線的上網助手仍會去叫 xcrun 而失敗；必須用上面兩個變數。
  > **進度更新 2026-11-06**：已點下 macOS「安裝命令列開發者工具」視窗（方案 a 啟動中），安裝完成前終端 `git` 仍不可直接用；期間繼續採用內附 git + 兩個環境變數的繞道。

## Q2：找不到工作坊倉庫（目前資料夾不是 git 倉庫）

- **現象**：目前的工作資料夾 `/Users/wwy`（家目錄）不是 git 倉庫；執行 remote 檢查時沒有任何 remote 可回報。
- **搜尋範圍與結果**：家目錄、桌面、文件、下載、iCloud 雲碟、垃圾桶全部搜尋過，沒有任何 `.git` 資料夾或 `my-work` 資料夾。
- **線索**：GitHub Desktop 最近才安裝（剛完成歡迎精靈，還沒 clone 過任何倉庫）；VS Code 也沒有開啟本機資料夾的紀錄。
- **推測**：我可能只完成 GitHub 網頁上的 fork，還沒把倉庫 clone 到這台電腦。
- **想問**：請老師／助教帶一次正確的 clone 流程，並確認大家統一的存放路徑（例如 `~/Documents/GitHub/<倉庫名>`），方便之後把已建好的 my-work/ 搬進倉庫。
- **✅ 2026-11-05 更新：已解決**：
  - 已用 GitHub API 查到我的 fork：`backtolaip-png/art-ai-workshop-2026`
  - 已 clone 到 `~/Documents/GitHub/art-ai-workshop-2026`，remote 正確指向 fork，分支 `main`
  - 本機預先建好的 `my-work/` 已用 `rsync --ignore-existing`（只加不覆蓋）搬入倉庫的 `my-work/`，講師的 README 範本全數保留
  - 安全驗證已通過：`git check-ignore -v` 確認 `my-work/.env` 與 `my-work/artifacts/*` 均被規則攔截，不會入庫（講師的根 `.gitignore` + 我自己的 `my-work/.gitignore` 雙保險）
  - 遺留：`~/my-work/`（原家目錄副本）先當備份保留，等 push 成功後可刪
  - **✅ 2026-11-06 收尾**：push 已成功（GitHub 線上已驗證最新 commit `4de7650` 與本機指紋一致）；家目錄舊副本經遞迴比對（`.env` 與 `artifacts/*` 逐檔相同，無任何獨有內容）後已刪除，本倉庫 `my-work/` 為唯一工作副本。

---

## 本次測試已完成（供課上核對）

- ✅ 環境檢查：Python 3.15.2、uv 0.13.7、Node.js v26.13.0 皆正常；Git 狀況見 Q1。
- ✅ 四類研究目錄已建立：`/Users/wwy/my-work/{sources/raw, sources/processed, research, artifacts}`（等倉庫 clone 後可整個搬入）。
- ✅ 第一份測試材料（TM-001，虛構示例）已放入 sources/raw/。
- ✅ 索引 CSV（sources/processed/index.csv）與筆記草稿（research/TM-001-notes-draft.md）已產生並互相連結。
- ✅ 沒有任何步驟卡住超過 30 分鐘；本清單是主動記錄，非逾時停機。
- ✅ OpenRouter API 整合測試完成（2026-11-05）：看圖+OCR 與生圖各一次成功；明細見 my-work/sources/processed/api-calls.csv。

---

## 已解決插曲（可帶到課堂分享的實例）：模型地區限制（403）

- **現象**：呼叫 `anthropic/claude-haiku-5.5` 得到 403 `This model is not available in your region.`——模型「來源商」（Anthropic）不對本機所在地區提供服務，與帳戶餘額無關；OpenRouter 未收費。
- **學到的事**：這正是用 OpenRouter 的價值——同一把金鑰、同一個請求格式，換一個模型 slug（改用 `qwen/qwen3.8-flash`）就成功了。挑熱門大廠模型前，先想好一個「備援模型」。
- **金鑰管理實務**：本次金鑰曾以完整字串出現在對話視窗；已排程到 OpenRouter Keys 頁面撤銷重發（Delete → Create Key），.env 也要同步更新。平日金鑰只存 .env， .env 靠 .gitignore（my-work/.gitignore 已排除 .env 與 artifacts/）永不入庫。
