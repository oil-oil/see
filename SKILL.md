---
name: see
description: >
  Analyze images and videos via ZenMux API (Gemini backend). Use for: any video (local or URL),
  image URLs, webpages with embedded video (e.g. YouTube), or local images when the agent is NOT
  natively multimodal. TRIGGER on: "帮我看", "看看这个", "分析一下这个视频", any request to
  view/describe/analyze/summarize visual media.
  DO NOT TRIGGER for local image files when the agent is natively multimodal (e.g. Claude Code
  with vision) — use the Read tool directly instead. Never trigger for text, PDFs, or audio.
---

# See

Run `scripts/ask_media.sh` to analyze visual media. Never call ZenMux API directly.

The script already contains built-in analysis prompts:
- **Image**: focus on overall content, key elements, visible text, and reusable details
- **Video**: focus on subtitles / spoken content first, then visuals, actions, and reusable takeaways

Do not spend time rewriting prompts unless the user has a very specific extra focus. In most cases, just pass the file path or URL directly.

## Usage

```bash
# Image
scripts/ask_media.sh --image /path/to/image.png

# Multiple images
scripts/ask_media.sh --image /path/a.png --image /path/b.png

# Video (auto-compresses if >45MB)
scripts/ask_media.sh --video /path/to/video.mp4

# URL (image, video, or webpage with embedded video)
scripts/ask_media.sh "https://example.com/photo.jpg"
scripts/ask_media.sh "https://youtube.com/watch?v=xxx"

# Optional extra focus
scripts/ask_media.sh --video demo.mp4 --task "重点看界面里的操作步骤"

# Optional output name
scripts/ask_media.sh --image photo.png --name landing-page-hero

# Custom output path
scripts/ask_media.sh --image photo.png -o /tmp/result.md
```

On success, prints `output_path=<path>` to stdout. Read that file for the result.

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--task` | Optional extra focus, not required for normal use | empty |
| `--image` | Image path/URL (repeatable) | |
| `--video` | Video path/URL | |
| positional args | Any file path or URL | |
| `--name` | Optional short output name | |
| `-o` | Output file path | `~/.local/share/see/outputs/YYYY-MM-DD/<timestamp>__<type>__<source>.md` |
| `--max-upload-mb` | Max size before compression | 45 |
| `--model` | Model override | `google/gemini-3-flash-preview` |

## Output

By default, outputs are stored in a shared directory so Codex and Claude can both find them easily.

Each output file includes:
- A metadata header with creation time, media type, source inputs, model, and any extra focus
- The parsed markdown result

Default naming pattern:
- `YYYYMMDD-HHMMSS__image__<source>.md`
- `YYYYMMDD-HHMMSS__images__<source>.md`
- `YYYYMMDD-HHMMSS__video__<source>.md`

Use `--name` when you want a cleaner project-specific filename.

## API Key

Lookup order: `ZENMUX_API_KEY` env var → `.env.local` in cwd/parents → `~/.config/see/api_key`. If missing, ask user.

## Dependencies

- **python3**: required
- **ffmpeg/ffprobe**: required for video (`brew install ffmpeg`)
- **yt-dlp**: optional, for webpage video extraction (`brew install yt-dlp`)

## Workflow

Before calling the script, decide whether you actually need it:

- **Local image(s) + natively multimodal agent** (e.g. Claude Code): use the `Read` tool directly — it renders images inline. Call `Read` once per image for multiple files. After viewing, respond to the user directly in context — no file output needed.
- **Video (any source)**: always use this script — Claude has no native video understanding.
- **Image URL, video URL, or webpage with embedded video**: always use this script — the `Read` tool only works with local files.

If you do need the script:

1. Run the script with file path, URL, or `--image`/`--video` flags.
2. Only add `--task` if the user has a specific extra focus.
3. Read the output file and continue working with the parsed text.
