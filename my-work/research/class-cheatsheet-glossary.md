# 課堂防迷失術語對照表（白話版）｜2026-11-06

> 用法：堂上聽到唔明嘅詞，快速掃呢份表。左邊係老師可能講嘅術語，右邊係你已經親手做過嘅事——用自己做過嘅嘢去理解新詞，一定快。

## Git / 版本控制

| 術語 | 白話 | 你已經做過 |
|---|---|---|
| Fork | 影印一份到自己帳號 | 已有 `backtolaip-png/art-ai-workshop-2026` |
| Clone | 整份下載返本機 | `~/Documents/GitHub/art-ai-workshop-2026` |
| Commit | 蓋章記錄一次改動 | 你已有 4 個 commit |
| Push | 上傳到 GitHub | 按過 Push origin |
| .gitignore | 見死不救名單（永不上傳） | `.env` 同 `artifacts/` 曾驗證被欄截 |
| remote / origin | 雲端地址 | origin = 你嘅 fork |

## AI / API

| 術語 | 白話 | 你已經做過 |
|---|---|---|
| API key | 提款卡密碼 | 存在 `.env`，唔貼對話 |
| token | 文字粒（模型計費單位） | api-calls.csv 有 in/out 數 |
| prompt | 你對 AI 講嘅要求 | 已照 prompt 生成圖 |
| 多模態 | 文＋圖＋影像一齊食 | qwen3.8-flash 睇圖＋OCR 已驗 |
| OCR | 相機變文字 | 「冨嶽三十六景」逐字命中 |
| reasoning / 思考鏈 | AI 唔直接答，先寫草稿 | 600 token 截斷那一跤 |
| hallucination | AI 作大咗 | 生圖書脊亂碼「UKIYOB」就係例 |

## 資料 / 研究

| 術語 | 白話 | 你已經做過 |
|---|---|---|
| provenance | 出處追溯（邊度嚟） | source_id＋頁碼三件套 |
| 可重現 | 第二次照步驟攞返同樣結果 | 方法誌就係寫俾未來自己 |
| raw / processed | 原件／處理品分家 | sources/raw vs processed |
| embedding / 向量 | 將意思變成座標以便相似度比較 | ⚠️ OpenRouter 無 → Q3 課堂問 |
| corpus | 你的資料集語料庫 | index.csv 就係入門版 |

## MCP / 工具協作

| 術語 | 白話 | 你已經做過 |
|---|---|---|
| MCP | 翻譯官，等 agent 講共同語言 | uvx mcp-for-blender 0.7 秒通 |
| stdio / JSON-RPC | 一問一答嘅行式對話 | initialize 握手 |
| socket / 埠 | 本機服務員的門牌 | 127.0.0.1:9876 |
| read-only | 只睇唔改 | get_scene_info 對比 BLEN-001 |

## 聽唔明時可以直接講嘅一句話

- 「老師可以舉返一個實例比喻嗎？」
- 「呢個同我平時做研究的手法有咩分別？」
- 「我未完全跟上這一步，可否慢啲重複一次？」

## 三條已準備好嘅提問（問 working 也 OK）

1. 終端 git 統一方案：Xcode 命令列工具 vs GitHub Desktop 內嵌 vs Homebrew？→ Q1
2. 語義檢索用什麼替代 OpenRouter 冇咗的 embedding（本機 sentence-transformers？獨立向量服務？）→ Q3
3. AI 生成物（逐次計費、亂碼瑕疵）幾時可以當研究素材、幾時警惕唔可以直接當史料？→ 這堂主題本身
