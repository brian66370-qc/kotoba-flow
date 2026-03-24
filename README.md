# kotoba-flow

[中文](README.zh-TW.md)

[![Python](https://img.shields.io/badge/Python-3.11%2B-blue.svg)](https://www.python.org/)
[![Discord Bot](https://img.shields.io/badge/Discord-Bot-5865F2.svg)](https://discord.com/developers/docs/intro)
[![NotebookLM Ready](https://img.shields.io/badge/NotebookLM-Ready-34A853.svg)](https://notebooklm.google/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](#)

Turn a Discord study channel into structured Japanese learning data with kotoba-flow.

This project helps you export Japanese vocabulary and grammar notes from Discord, keep incremental checkpoints, normalize raw messages into study entries, and generate NotebookLM-ready Markdown for summaries, quizzes, and future review tools.

## Screenshots

### Discord export command

![Discord export command](docs/images/discord_export_command.png)

### Bot response

![Bot response](docs/images/discord_export_result.png)

### Raw JSON export

![Raw JSON export](docs/images/raw_export_json.png)

### NotebookLM-ready files

![NotebookLM-ready files](docs/images/notebooklm_files.png)

## Quick Start

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
python bot/private_reader_bot.py
```

Inside Discord, run:

- `/export_here`
- `/export_status`

Then process the exported file:

```powershell
python parser/normalize.py --input data/raw/exports/your_export.json --output data/normalized/normalized_entries.json
python parser/build_notebooklm_files.py --input data/normalized/normalized_entries.json --output-dir data/notebooklm
```

## Input Format Tip

The parser works better when your Discord notes follow a consistent format.

Recommended:

- add `文法：` for grammar notes
- add `意思：` for meanings when possible
- add `例文：` for example sentences
- `Grammar:`, `Meaning:`, and `Example:` are also supported
- vocabulary notes can stay more natural, but consistent formatting improves accuracy

Examples:

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

## Simple Workflow

1. Create a bot in Discord Developer Portal.
2. Enable the required bot settings, especially `Message Content Intent`.
3. Invite the bot to your server with the correct OAuth2 scopes and permissions.
4. Put the bot token and Discord IDs into your local `.env`.
5. Start the bot from your terminal.
6. Run `/export_here` in the target Discord channel.
7. Export the messages into a JSON file, then process them with the parser.

Full implementation guide:

- [Backend and deployment guide](docs/backend_deployment_guide.md)

## Overview

This project helps you:

- export messages from a Discord channel with a bot
- keep incremental checkpoints for each channel
- normalize Japanese learning notes into structured entries
- generate Markdown files for NotebookLM
- prepare data for future review apps or flashcards

## Features

- private channel export with slash commands
- incremental export with per-channel checkpoints
- full re-export when needed
- raw JSON archive output
- starter parser for vocabulary and grammar notes
- NotebookLM-friendly Markdown generation

## Slash Commands

- `/export_here`
  Export only new messages since the last checkpoint
- `/export_here full_export:true`
  Export the whole channel again
- `/export_status`
  Show the saved checkpoint for the current channel
- `/export_reset_here`
  Reset the checkpoint for the current channel
- `/whoami`
  Show your Discord user ID

## Required Discord Settings

- Bot permissions:
  - `View Channel`
  - `Read Message History`
  - `Send Messages`
  - `Use Application Commands`
- Developer Portal:
  - enable `Message Content Intent`

## Project Structure

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

## Output Files

- `data/raw/exports/`
  Raw Discord channel exports
- `data/raw/export_state.json`
  Incremental export checkpoints
- `data/normalized/normalized_entries.json`
  Structured study entries
- `data/notebooklm/`
  Markdown files ready for NotebookLM

## Example Files

Safe example outputs are included in `examples/`:

- `examples/sample_export.json`
- `examples/sample_normalized_entries.json`
- `examples/sample_notebooklm_vocab.md`
- `examples/sample_notebooklm_grammar.md`

These are small demonstration files for GitHub readers and do not contain your private Discord data.

## Roadmap

- Improve grammar detection and extraction quality
- Split mixed long-form messages into multiple study entries
- Add automatic pipeline command after export
- Export directly to flashcard-friendly formats such as CSV
- Add a lightweight review UI for spaced repetition
- Add test coverage for parser rules
