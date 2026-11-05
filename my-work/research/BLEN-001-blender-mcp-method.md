# BLEN-001 — Blender MCP 連線讀取場景（方法記錄）

日期：2026-11-05 ｜ 路徑 B 整合測試 ｜ 狀態：成功

## 目的

驗證「Coding Agent 透過 MCP 協定與本機 Blender 協作、且不破壞既有 3D 場景」這條鏈路可用。

## 架構（資料怎麼流的）

```
node MCP client（本測試腳本）
  ⇄ stdio JSON-RPC（MCP 標準）
   uvx mcp-for-blender v1.30.0（翻譯官）
  ⇄ TCP socket
   127.0.0.1:9876（Blender 插件內的服務生）
  ⇄ 內部呼叫
   Blender 5.2（GUI，/Applications/Blender.app）正在開啟的場景
```

## 執行步驟摘要

1. `uvx mcp-for-blender addon-paths` → 確認使用者插件目錄（唯讀查詢）
2. `uvx mcp-for-blender install-addon` → 裝入 `~/Library/Application Support/Blender/5.2/scripts/addons/blender_mcp.py`
3. 無頭（`--background`）啟用插件失敗 → **插件記錄了安全設計：服務生必須住在有視窗的 Blender**（錯誤訊息："cannot start server in background mode"）；改用 `addon_utils.enable(..., default_set=True)` + `save_userpref` 永久勾選位於：**⏸ 已放棄此路**（實際habot：插件檔已由 install-addon 裝好，動作 3.1 命令列出「enable」等價於手動勾選）
4. 由使用者自行 Cmd+Q 退出舊 Blender（避免未存檔風險），改以 `open -a /Applications/Blender.app` 重開；插件**自動啟動伺服器**，約 6 秒 9876 埠開放
5. `node my-work-blender-mcp-read.js`：spawn `uvx mcp-for-blender` → JSON-RPC initialize/initialized 握手 → `tools/call get_scene_info`（唯讀）→ 0.7 秒取回場景清單

## 安全聲明（重要）

- 只呼叫**唯讀**的 `get_scene_info`；全程未呼叫 `execute_blender_code` 或任何修改性工具
- 全程未對任何 `.blend` 檔「儲存／另存」（場景狀態零改動）
- 插件 socket 僅聽 localhost（官方明示無加密無認證，不應開放遠端）
- 使用者偏好設定被寫入的唯一內容：啟用 `blender_mcp` 插件（可隨時在 Preferences 反勾選撤銷）

## 產物

- 結構化 CSV：`../sources/processed/blender-scene-inventory.csv`（source_id=BLEN-001）
- 完整 MCP 回應：`../artifacts/response_blender_scene_info_BLEN-001.json`（未入庫，artifacts被 gitignore）

## 待辦／延伸

- [ ] 若要持續使用 MCP 編輯場景，可依官方 README 把 `uvx mcp-for-blender` 註冊成 OpenCode 的 MCP 設定（README 有 OpenCode 專屬 JSON 範例）；本次僅以 stdio 一次性呼叫完成測試
- [ ] 注意 blender-mcp 套件已更名 mcp-for-blender（`uvx blender-mcp` 舊名仍可用）
