# kotoba-flow

[English](README.md)

[![Python](https://img.shields.io/badge/Python-3.11%2B-blue.svg)](https://www.python.org/)
[![Discord Bot](https://img.shields.io/badge/Discord-Bot-5865F2.svg)](https://discord.com/developers/docs/intro)
[![NotebookLM Ready](https://img.shields.io/badge/NotebookLM-Ready-34A853.svg)](https://notebooklm.google/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](#)

使用 kotoba-flow，把 Discord 學習頻道整理成結構化的日文學習資料。

這個專案可以幫你從 Discord 匯出日文單字與文法筆記、記住每個頻道的增量匯出位置、把原始訊息正規化成結構化條目，並產生可直接餵給 NotebookLM 的 Markdown，方便做摘要、測驗與後續複習工具。

## 截圖

### Discord 匯出指令

![Discord export command](docs/images/discord_export_command.png)

### Bot 回覆結果

![Bot response](docs/images/discord_export_result.png)

### 原始 JSON 匯出

![Raw JSON export](docs/images/raw_export_json.png)

### NotebookLM 檔案輸出

![NotebookLM-ready files](docs/images/notebooklm_files.png)

## 快速開始

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
python bot/private_reader_bot.py
```

回到 Discord 後執行：

- `/export_here`
- `/export_status`

再處理匯出檔：

```powershell
python parser/normalize.py --input data/raw/exports/your_export.json --output data/normalized/normalized_entries.json
python parser/build_notebooklm_files.py --input data/normalized/normalized_entries.json --output-dir data/notebooklm
```

## 輸入格式提醒

如果你的 Discord 筆記格式越一致，parser 的效果通常會越好。

建議：

- 文法筆記加上 `文法：`
- 能寫的話加上 `意思：`
- 例句盡量加上 `例文：`
- 也支援英文欄位 `Grammar:`, `Meaning:`, `Example:`
- 單字筆記可以保留比較自然的寫法，但格式越一致，辨識會越準

範例：

```text
文法：〜ようにする
意思：盡量做到...
例文：毎日日本語を勉強するようにしている。
```

```text
Grammar: 〜ようにする
Meaning: try to make it a habit to...
Example: 毎日日本語を勉強するようにしている。
```

```text
咄嗟（とっさ）
ごく短い時間
例文：動揺から咄嗟の間に立ちなおっていた
```

## 簡單流程

1. 在 Discord Developer Portal 建立 bot
2. 開啟需要的 bot 設定，尤其是 `Message Content Intent`
3. 用正確的 OAuth2 scopes 與權限把 bot 邀請進 server
4. 把 bot token 與 Discord 相關 ID 填進本機 `.env`
5. 在終端機啟動 bot
6. 在目標 Discord 頻道執行 `/export_here`
7. 匯出成 JSON 後再交給 parser 處理

完整實作說明：

- [後端與部署教學](docs/backend_deployment_guide.md)

## 專案概覽

這個專案可以幫你：

- 用 bot 匯出 Discord 頻道訊息
- 記住每個頻道的增量匯出位置
- 將日文學習筆記整理成結構化條目
- 產生可輸入 NotebookLM 的 Markdown
- 為後續複習 app 或卡片系統準備資料

## 功能

- 私人頻道 slash command 匯出
- 頻道級 checkpoint 增量匯出
- 需要時可重新完整匯出
- 保留原始 JSON 匯出檔
- 基礎單字與文法 parser
- NotebookLM 友善的 Markdown 輸出

## Slash Commands

- `/export_here`
  只匯出上次 checkpoint 之後的新訊息
- `/export_here full_export:true`
  重新完整匯出整個頻道
- `/export_status`
  查看目前頻道的匯出 checkpoint
- `/export_reset_here`
  重設目前頻道的匯出 checkpoint
- `/whoami`
  顯示你的 Discord 使用者 ID

## Discord 必要設定

- Bot 權限：
  - `View Channel`
  - `Read Message History`
  - `Send Messages`
  - `Use Application Commands`
- Developer Portal：
  - 開啟 `Message Content Intent`

## 專案結構

```text
bot/
  export_channel.py
  private_reader_bot.py
parser/
  normalize.py
  build_notebooklm_files.py
data/
  raw/
  normalized/
  notebooklm/
docs/
  images/
examples/
```

## 主要輸出檔案

- `data/raw/exports/`
  Discord 原始匯出 JSON
- `data/raw/export_state.json`
  增量匯出 checkpoint
- `data/normalized/normalized_entries.json`
  結構化學習條目
- `data/notebooklm/`
  可直接餵給 NotebookLM 的 Markdown

## 範例檔案

`examples/` 內含安全的範例輸出：

- `examples/sample_export.json`
- `examples/sample_normalized_entries.json`
- `examples/sample_notebooklm_vocab.md`
- `examples/sample_notebooklm_grammar.md`

這些只是示範用的小型範例，不包含你的私人 Discord 資料。

## Roadmap

- 加強文法辨識與抽取品質
- 將混合型長訊息拆成多個學習條目
- 在匯出後加上自動處理流程
- 直接輸出成 flashcard 友善格式，例如 CSV
- 加入輕量級 spaced repetition 複習 UI
- 為 parser 規則補上測試
