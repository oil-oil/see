# see

A [Claude Code skill](https://docs.anthropic.com/en/docs/claude-code/skills) that lets AI agents analyze images and videos via the [ZenMux](https://zenmux.ai) API (Gemini backend).

When installed, Claude automatically routes visual media tasks through this skill — using its own native vision for local images, and falling back to ZenMux for videos, image URLs, and YouTube links.

## What it does

- **Images**: local files or URLs — describe, extract text, analyze UI, compare screenshots
- **Videos**: local files or URLs (including YouTube) — transcribe speech, summarize content, describe actions
- **Smart routing**: if Claude itself is multimodal (e.g. Claude Code), local images are read natively without an API call

## Requirements

- Python 3
- `ffmpeg` + `ffprobe` — required for video (`brew install ffmpeg`)
- `yt-dlp` — optional, for YouTube and webpage video extraction (`brew install yt-dlp`)
- A ZenMux API key

## API Key

Set your key in any of these places (checked in order):

```bash
# env var
export ZENMUX_API_KEY=sk-...

# or .env.local in the project directory (or any parent)
echo "ZENMUX_API_KEY=sk-..." >> .env.local

# or global config
mkdir -p ~/.config/see && echo "sk-..." > ~/.config/see/api_key
```

## Installation

Install via the `claude` CLI:

```bash
claude skill install https://github.com/oil-oil/see
```

Or clone and symlink manually:

```bash
git clone https://github.com/oil-oil/see ~/.agents/skills/see
ln -s ../../.agents/skills/see ~/.claude/skills/see
```

## Usage (CLI)

The script can also be used directly from the command line:

```bash
# Analyze an image
scripts/ask_media.sh --image /path/to/screenshot.png

# Multiple images
scripts/ask_media.sh --image /path/a.png --image /path/b.png

# Video (auto-compresses if >45MB)
scripts/ask_media.sh --video /path/to/demo.mp4

# URL (image, video, or YouTube)
scripts/ask_media.sh "https://example.com/photo.jpg"
scripts/ask_media.sh "https://youtube.com/watch?v=xxx"

# Extra focus
scripts/ask_media.sh --video demo.mp4 --task "focus on the UI interactions"

# Named output
scripts/ask_media.sh --image photo.png --name hero-screenshot
```

On success, prints `output_path=<path>` to stdout. Results are stored in `~/.local/share/see/outputs/`.

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--image` | Image path or URL (repeatable) | |
| `--video` | Video path or URL | |
| `--task` | Extra focus for the analysis | empty |
| `--name` | Short name for the output file | |
| `-o` | Full output file path | auto-generated |
| `--max-upload-mb` | Max size before video compression | 45 |
| `--model` | Model override | `google/gemini-3-flash-preview` |

## License

MIT
