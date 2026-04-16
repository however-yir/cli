<img src="https://file.cdn.minimax.io/public/MMX.png" alt="MiniMax" width="100%" />

<p align="center">
  <strong>The official CLI for the MiniMax AI Platform</strong><br>
  Built for AI agents. Generate text, images, video, speech, and music — from any agent or terminal.
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/mmx-cli"><img src="https://img.shields.io/npm/v/mmx-cli.svg" alt="npm version" /></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT" /></a>
  <a href="https://nodejs.org"><img src="https://img.shields.io/badge/node-%3E%3D18-brightgreen.svg" alt="Node.js >= 18" /></a>
</p>

<p align="center">
  <a href="README_CN.md">中文文档</a> · <a href="https://platform.minimax.io">Global Platform</a> · <a href="https://platform.minimaxi.com">CN Platform</a>
</p>

## Features

- **Text** — Multi-turn chat, streaming, system prompts, JSON output
- **Image** — Text-to-image with aspect ratio and batch controls
- **Video** — Async video generation with progress tracking
- **Speech** — TTS with 30+ voices, speed control, streaming playback
- **Music** — Text-to-music with lyrics, instrumental mode, auto lyrics, and cover generation from reference audio
- **Vision** — Image understanding and description
- **Search** — Web search powered by MiniMax
- **Dual Region** — Seamless Global (`api.minimax.io`) and CN (`api.minimaxi.com`) support

<img src="https://file.cdn.minimax.io/public/MMX-CLI.png" alt="MiniMax" width="100%" />

## Install

```bash
# For AI agents (OpenClaw, Cursor, Claude Code, etc.): add skill to your agent
npx skills add MiniMax-AI/cli -y -g

# Or install CLI globally for terminal use
npm install -g mmx-cli
```

> Requires [Node.js](https://nodejs.org) 18+

> **Requires a MiniMax Token Plan** — [Global](https://platform.minimax.io/subscribe/token-plan) · [CN](https://platform.minimaxi.com/subscribe/token-plan)

## Quick Start

```bash
# Authenticate with a persistent API key
mmx auth login --api-key sk-xxxxx

# Start creating
mmx text chat --message "What is MiniMax?"
mmx image "A cat in a spacesuit"
mmx speech synthesize --text "Hello!" --out hello.mp3
mmx video generate --prompt "Ocean waves at sunset" --download sunset.mp4
mmx music generate --prompt "Upbeat pop" --lyrics "[Verse] La da dee, sunny day" --out song.mp3
mmx search "MiniMax AI latest news"
mmx vision photo.jpg
mmx quota
```

Resources with a single subcommand auto-forward, so `mmx image ...` is equivalent to `mmx image generate ...`, and `mmx quota` is equivalent to `mmx quota show`.

## Command Quickstart Matrix

| Goal | Shortcut | Explicit form | Typical stdout |
| --- | --- | --- | --- |
| Chat with a model | `mmx text --message "What is MiniMax?"` | `mmx text chat --message "What is MiniMax?"` | Assistant text |
| Generate images | `mmx image "A cat in a spacesuit"` | `mmx image generate --prompt "A cat in a spacesuit"` | Saved file path(s) or JSON metadata |
| Generate a video and save it | `mmx video --prompt "Ocean waves" --download sunset.mp4` | `mmx video generate --prompt "Ocean waves" --download sunset.mp4` | Saved file info or path |
| Start an async video job | `mmx video --prompt "Ocean waves" --async` | `mmx video generate --prompt "Ocean waves" --async` | `{"taskId":"..."}` |
| Synthesize speech | `mmx speech synthesize --text "Hello!" --out hello.mp3` | Same | Saved file info or path |
| Generate music | `mmx music generate --prompt "Upbeat pop" --lyrics-file song.txt --out song.mp3` | Same | Saved file info or path |
| Describe an image | `mmx vision photo.jpg` | `mmx vision describe --image photo.jpg` | Description text |
| Search the web | `mmx search "MiniMax AI"` | `mmx search query --q "MiniMax AI"` | Search results or JSON |
| Inspect auth or quota | `mmx auth status` / `mmx quota` | `mmx auth status` / `mmx quota show` | Human-readable status table or JSON |

## Authentication Modes

| Mode | Best for | How to use it | Stored in |
| --- | --- | --- | --- |
| One-off API key override | CI, scripts, temporary testing | Add `--api-key sk-...` to any command | Not persisted |
| Persistent API key login | Local shells, agent workstations | `mmx auth login --api-key sk-...` | `~/.mmx/config.json` |
| OAuth browser login | Interactive user sessions | `mmx auth login` | `~/.mmx/credentials.json` |
| OAuth device-code login | SSH, headless terminals | `mmx auth login --no-browser` | `~/.mmx/credentials.json` |
| Environment bootstrap | CI or first-run setup | `export MINIMAX_API_KEY=sk-...` | Environment only unless you save it |

Request-time credential resolution is:

1. `--api-key`
2. `~/.mmx/credentials.json` (OAuth)
3. `api_key` in `~/.mmx/config.json`

Useful auth commands:

```bash
mmx auth login --api-key sk-xxxxx   # validate + save api_key to ~/.mmx/config.json
mmx auth login                      # browser-based OAuth flow
mmx auth login --no-browser         # device-code OAuth flow
mmx auth status --output json
mmx auth refresh
mmx auth logout
```

## Commands

### `mmx text`

```bash
mmx text chat --message "Write a poem"
mmx text --message "Hello" --model MiniMax-M2.7-highspeed
mmx text chat --system "You are a coding assistant" --message "Fizzbuzz in Go"
mmx text chat --message "user:Hi" --message "assistant:Hey!" --message "How are you?"
cat messages.json | mmx text chat --messages-file - --output json
```

### `mmx image`

```bash
mmx image "A cat in a spacesuit"
mmx image generate --prompt "A cat" --n 3 --aspect-ratio 16:9
mmx image generate --prompt "Logo" --out-dir ./out --out-prefix brand
```

### `mmx video`

```bash
mmx video generate --prompt "Ocean waves at sunset" --async
mmx video generate --prompt "A robot painting" --download sunset.mp4
mmx video task get --task-id 123456
mmx video download --file-id 176844028768320 --out video.mp4
```

### `mmx speech`

```bash
mmx speech synthesize --text "Hello!" --out hello.mp3
mmx speech synthesize --text "Stream me" --stream | mpv -
mmx speech synthesize --text "Hi" --voice English_magnetic_voiced_man --speed 1.2
echo "Breaking news" | mmx speech synthesize --text-file - --out news.mp3
mmx speech voices --output json
```

### `mmx music`

```bash
# Generate with lyrics
mmx music generate --prompt "Upbeat pop" --lyrics "[Verse] La da dee, sunny day" --out song.mp3
# Auto-generate lyrics from prompt
mmx music generate --prompt "Indie folk, melancholic, rainy night" --lyrics-optimizer --out song.mp3
# Instrumental (no vocals)
mmx music generate --prompt "Cinematic orchestral" --instrumental --out bgm.mp3
# Cover generation from a reference audio file
mmx music cover --prompt "Jazz, piano, warm female vocal" --audio-file original.mp3 --out cover.mp3
mmx music cover --prompt "Indie folk" --audio https://example.com/song.mp3 --out cover.mp3
```

### `mmx vision`

```bash
mmx vision photo.jpg
mmx vision describe --image https://example.com/img.jpg --prompt "What breed?"
mmx vision describe --file-id file-123
```

### `mmx search`

```bash
mmx search "MiniMax AI"
mmx search query --q "latest news" --output json
```

### `mmx config` · `mmx quota` · `mmx update`

```bash
mmx quota
mmx quota --output json
mmx config show
mmx config set --key region --value cn
mmx config export-schema --command "video generate" | jq .
mmx update
```

## JSON Output and Scripting

Every command supports the global `--output json` flag. For shell automation, combine it with `jq`, `--quiet`, and `--non-interactive`.

```bash
# Inspect the active auth source
mmx auth status --output json | jq .

# Parse remaining quota by model
mmx quota --output json \
  | jq '.model_remains[] | {model: .model_name, remaining: (.current_interval_total_count - .current_interval_usage_count)}'

# Feed a saved conversation into text chat
cat conversation.json | mmx text chat --messages-file - --output json

# Export one command as an agent tool schema
mmx config export-schema --command "video generate" > video-generate.tool.json

# Use quiet mode when you only need the returned path or URL
mmx image "Paper-cut fox" --quiet
mmx music generate --prompt "Lo-fi beat" --instrumental --output-format url --quiet
```

## Shell Workflows for Agents

```bash
# 1) Upload a file, capture the file_id, then reuse it in a follow-up command
FILE_ID=$(mmx file upload --file spec.pdf --quiet)
mmx vision describe --file-id "$FILE_ID" --prompt "Summarize the architecture diagram"
```

```bash
# 2) Kick off a long-running video job, poll it later, then download explicitly
TASK_ID=$(mmx video generate --prompt "A robot painting" --async | jq -r '.taskId')
FILE_ID=$(mmx video task get --task-id "$TASK_ID" --output json | jq -r '.file_id')
mmx video download --file-id "$FILE_ID" --out ./artifacts/robot.mp4
```

```bash
# 3) Keep agent runs deterministic and pipe-friendly
mmx text chat \
  --message "Return exactly three deployment risks as JSON." \
  --output json \
  --non-interactive
```

```bash
# 4) Turn generated text into speech in one shell flow
mmx text chat --message "Write a 20-word greeting for new users." --quiet \
  | mmx speech synthesize --text-file - --out welcome.mp3
```

## Download Paths and Generated File Names

| Command | Default output location | How to make it explicit |
| --- | --- | --- |
| `mmx image` | Current working directory as `image_001.jpg`, `image_002.jpg`, ... | Use `--out-dir` and optionally `--out-prefix` |
| `mmx speech synthesize` | Current working directory as `speech_<timestamp>.<format>` | Use `--out ./path/file.mp3` |
| `mmx music generate` | Current working directory as `music_<timestamp>.<format>` | Use `--out ./path/file.mp3` |
| `mmx music cover` | Current working directory as `cover_<timestamp>.<format>` | Use `--out ./path/file.mp3` |
| `mmx video generate --download` | Exactly the path you pass to `--download` | Prefer this for reproducible workflows |
| `mmx video generate` without `--download` | Auto-downloads to a temp directory and prints the local path | Add `--download` if you need a stable location |
| `mmx video generate --async` | No file is downloaded yet; stdout is only the task id JSON | Later call `mmx video task get` / `mmx video download` |

Troubleshooting tips:

- Relative paths are resolved from your current shell working directory.
- If a file seems to disappear after `mmx video generate`, you probably ran without `--download`; the printed path points to the temp copy.
- `--quiet` is useful for scripting because it prints the path, URL, or file id without extra tables.
- If you need predictable artifact names in CI, always pass `--out`, `--download`, or `--out-dir` + `--out-prefix`.

## Thanks to

<a href="https://github.com/MiniMax-AI/cli/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=MiniMax-AI/cli" />
</a>

## License

[MIT](LICENSE)
