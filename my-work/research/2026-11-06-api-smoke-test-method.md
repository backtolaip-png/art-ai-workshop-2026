# API 整合測試方法誌（2026-11-06 第二次）

> 本文記錄 artifacts/ 三個新檔案的製造參數（AGENTS.md 規定）。原始回應 JSON 與費用明細同步於
> `../sources/processed/api-calls.csv`（OCR-VIS-002 / 002b / GEN-IMG-002）。

## 共同前提

- 平台：OpenRouter（同一把金鑰，餘額上限 \$50，已儲值）
- 金鑰管理：`my-work/.env`（未追蹤檔案），由 shell `source` 載入，全程未列印、未貼入對話、未入庫
- 本機環境教訓：python.org 版 Python 3.15 尚未安裝 SSL 根憑證（`Install Certificates.command` 未跑），
  `urllib` 對 HTTPS 會 `CERTIFICATE_VERIFY_FAILED`；本日全程改用 `curl`（系統憑證）發送，Python 只負責組裝請求與解析回應。
- 提示詞核心：一律要求「保留原文逐字 OCR、不翻譯不潤飾」，符合 provenance 規則。

## A. 視覺／OCR 測試（qwen/qwen3.8-flash）

- 輸入：Wikimedia Commons 公共領域高解析 《神奈川沖浪裏》
  （`https://upload.wikimedia.org/wikipedia/commons/0/0d/Great_Wave_off_Kanagawa2.jpg`，URL 路線）
- 任務：①繁體中文描述構圖②逐字 OCR 落款與出版標記
- 第 1 次（`response_vision_qwen3.8-flash-2.json`）：`max_tokens=600` → 被 reasoning 鏈耗盡，
  `finish_reason=length`，content 為空；扣費 \$0.00067378（截斷同樣計費，務必記帳）。
- 第 2 次（`response_vision_qwen3.8-flash-3.json`）：`max_tokens=2000` + `reasoning:{"enabled":false}` →
  `finish_reason=stop`，成功。OCR 命中「冨嶽三十六景／神奈川沖浪裏／北斎」，與館藏著錄一致。\$0.00043103。
- 結論：帶思考鏈模型呼叫必須預留 reasoning 參數空間， или 關閉思考；否則答案會被截斷白燒錢。

## B. 圖像生成測試（qwen/qwen-image-2.1-turbo）

- 端點：`POST /api/v1/images`；參數 `resolution: "1K"`、`aspect_ratio: "3:2"`
- prompt：「博物館手稿檢視室一景：深色木檢視桌上平放一幅浮世繪版畫（神奈川沖浪裏風格），旁有白色紗手套、
  尺規與放大鏡，柔和北向窗光，冷色調，學術攝影風格，橫幅構圖」
- 輸出：`genimage_qwen-image-2.1-turbo-2.png`（1024px 級，1,065,152 bytes）；回應中繼資料（去 b64）存
  `response_genimage_qwen-image-2.1-turbo-2.json`
- 費用：\$0.0184／張，與官方 `GET /api/v1/images/models/qwen/qwen-image-2.1-turbo/endpoints` 標示完全一致。
- 註：官方 blogs/docs 顯示 `input_references` 可傳 0–4 張參考圖（圖生圖），本日未測。

## C. 未能測試：向量／Embedding 相似度

- `GET /api/v1/models` 目錄中搜「embed」零命中；官方 embeddings 文件頁 404。
- 結論：OpenRouter 今日不再提供統一 embeddings 端點；語義檢索需求需另行選型（本地下沉 sentence-transformers、
  或獨立向量服務），留待課堂討論（已記 `../questions.md`）。

## 費用小結

| 項目 | 費用 |
|---|---|
| 視覺第 1 次（截斷） | \$0.00067378 |
| 視覺第 2 次（成功） | \$0.00043103 |
| 生圖 1K×1 | \$0.0184 |
| **合計** | **\$0.01950481**（≈ NT\$0.65）[生圖入帳有延遲，餘額對帳見 csv] |
