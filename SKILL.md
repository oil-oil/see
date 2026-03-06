---
name: see
description: >
  Let AI see images and videos by converting visual media to text via ZenMux API.
  Accepts local files OR URLs (image links, video links, webpages with embedded video).
  Supported formats: .png/.jpg/.jpeg/.webp/.gif/.mp4/.mov/.mkv/.webm and any URL.
  TRIGGER when: user asks to view/see/look at/describe/analyze/summarize any image or video,
  user provides an image path or video path, user shares an image/video URL or a webpage URL
  containing video (e.g. YouTube), user says "看看这个图/视频", "帮我看", "分析一下这个视频".
  DO NOT TRIGGER when: user asks about text files, PDFs, or audio-only content.
---

# See

Run `scripts/ask_media.sh` to analyze visual media. Never call ZenMux API directly.

## Usage

```bash
# Image
scripts/ask_media.sh --task "describe this" --image /path/to/image.png

# Multiple images
scripts/ask_media.sh --task "compare these" --image /path/a.png --image /path/b.png

# Video (auto-compresses if >45MB)
scripts/ask_media.sh --task "summarize this video" --video /path/to/video.mp4

# URL (image, video, or webpage with embedded video)
scripts/ask_media.sh --task "what is this" "https://example.com/photo.jpg"
scripts/ask_media.sh --task "summarize" "https://youtube.com/watch?v=xxx"

# Custom output path
scripts/ask_media.sh --task "describe" --image photo.png -o /tmp/result.md
```

On success, prints `output_path=<path>` to stdout. Read that file for the result.

## Options

| Flag | Description | Default |
|------|-------------|---------|
| `--task` | What to analyze | "Describe this content in detail." |
| `--image` | Image path/URL (repeatable) | |
| `--video` | Video path/URL | |
| positional args | Any file path or URL | |
| `-o` | Output file path | `.runtime/see/<timestamp>.md` |
| `--max-upload-mb` | Max size before compression | 45 |
| `--model` | Model override | `google/gemini-3-flash-preview` |

## API Key

Lookup order: `ZENMUX_API_KEY` env var → `.env.local` in cwd/parents → `~/.config/see/api_key`. If missing, ask user.

## Dependencies

- **python3**: required
- **ffmpeg/ffprobe**: required for video (`brew install ffmpeg`)
- **yt-dlp**: optional, for webpage video extraction (`brew install yt-dlp`)

## Workflow

1. Convert user request into a `--task` sentence.
2. Run the script with file path, URL, or `--image`/`--video` flags.
3. Read the output file and continue working with the parsed text.
