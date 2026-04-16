# mmx CLI Design

## Command Grammar

All commands follow `resource + verb`:

```
mmx <resource> <verb> [flags]
```

When a resource has only one subcommand, the CLI auto-forwards:

```bash
mmx image "A cat in a spacesuit"      # same as: mmx image generate --prompt "A cat in a spacesuit"
mmx quota                             # same as: mmx quota show
```

## Command Tree

```
mmx
├── auth
│   ├── login              Authenticate via OAuth or API key
│   ├── status             Show current authentication state
│   ├── refresh            Manually refresh OAuth token
│   └── logout             Revoke tokens and clear stored credentials
├── text
│   └── chat               Send a chat completion (MiniMax Messages API)
├── speech
│   ├── synthesize         Synchronous TTS, <=10k chars
│   └── voices             List available voices
├── image
│   └── generate           Generate images
├── video
│   ├── generate           Create a video generation task
│   ├── task
│   │   └── get            Query video task status
│   └── download           Download a completed video by file ID
├── music
│   ├── generate           Generate a song
│   └── cover              Generate a cover version from reference audio
├── file
│   ├── upload             Upload a file to MiniMax storage
│   ├── list               List uploaded files
│   └── delete             Delete an uploaded file
├── search
│   └── query              Search the web
├── vision
│   └── describe           Describe an image by path, URL, or file ID
├── quota
│   └── show               Display Token Plan usage and remaining quotas
├── config
│   ├── show               Display current configuration
│   ├── set                Set a config value
│   └── export-schema      Export command schemas for agent tooling
├── update                 Show how to update the CLI
└── help                   Print command help and API reference links
```

## Exit Codes

| Code | Meaning |
|------|---------|
| 0    | Success |
| 1    | General / server error |
| 2    | Usage error (bad flags) |
| 3    | Authentication error |
| 4    | Rate limit or quota exceeded |
| 5    | Timeout |
| 10   | Content sensitivity filter |

## Authentication

Persistent auth storage:

- `~/.mmx/config.json` stores `api_key`, region, base URL, output format, and timeout
- `~/.mmx/credentials.json` stores OAuth access and refresh tokens

Request-time credential resolution order:

1. `--api-key` flag
2. `~/.mmx/credentials.json` (OAuth)
3. `api_key` in `~/.mmx/config.json`

`$MINIMAX_API_KEY` is used as a bootstrap input during auth setup and can be persisted into `config.json`.

## Configuration

Config precedence: flag > env var > config file > default.

Config file: `~/.mmx/config.json`
