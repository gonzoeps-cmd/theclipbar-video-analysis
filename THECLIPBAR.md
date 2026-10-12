# TheClipBar: visual clip analysis

Reviewed on 2026-10-12. This repository is a source fork of [bradautomates/claude-video](https://github.com/bradautomates/claude-video), selected from the account's starred repositories.

Reviewed upstream commit: `03ceb42f7fa2c4439aca01752118044baabffb8f`.

## Status

- The source was copied and its initial commit was verified against the reviewed upstream commit.
- GitHub Actions are disabled on this fork. No workflow runs were present when checked.
- The upstream MIT license and copyright notice are preserved.
- This note is the only addition made during the copy. Upstream defaults and code have not been changed.
- No plugin, skill, package, model, or application was installed or executed as part of this copy.
- This was a focused static review of the entry point, configuration, setup hook, installer, process helper, media download handling, frame extraction, and cloud transcription/analysis routes. It is not a complete security audit, dependency clearance, or runtime compatibility test.

## Why this fits TheClipBar

Use timestamped frame samples to inspect reference edits, separate reaction shots from source footage, identify visible source clues, and plan a cut. Pair this evidence with a transcript.

Frame sampling can miss a brief event. The script does not itself guarantee identification of an original clip, a person, or a source URL. Check the relevant interval and original upload before making those claims.

## Starting workflow: local video file and explicit options

For the first integration, provide an already downloaded, trusted local media file. Use the separate yt-dlp fork for URL retrieval and the separate official Whisper fork for local transcription.

This example is documentation only; it was not run. It requires an independently verified Python 3.10+ installation and FFmpeg/ffprobe on the execution machine.

From this repository, replacing the media path:

```bash
python3 skills/watch/scripts/watch.py /absolute/path/to/clip.mp4 --engine local --no-whisper --detail balanced --max-frames 100 --out-dir ./clipbar-analysis
```

The source path must point to a local file for this starting workflow. The script writes a Markdown report to standard output and creates a separate run directory containing frame images. Inspect those images directly; do not infer visual details from a transcript alone.

- `--engine local` prevents selection of the Gemini analysis path, even when a Gemini key is present.
- `--no-whisper` disables this wrapper's local and cloud transcription fallbacks. Use the separate official Whisper tool for transcription.
- `balanced` uses scene extraction with a frame cap; a focused `--start` / `--end` interval or explicit `--timestamps` can help inspect a particular moment.
- Supplying a local file avoids this wrapper's yt-dlp download path and inherited downloader configuration. It also means this run does not fetch online captions.
- Local processing here describes the script's processing route. If an agent later receives the generated frames or transcript, that material is processed in that agent's execution environment.

## Settings that need attention before broader use

The unmodified upstream defaults are `WATCH_ENGINE=auto` and `WATCH_WHISPER_BACKEND=auto`. Auto can select Gemini when a Gemini key is available, or a Groq/OpenAI transcription service when a matching key is available. Keep the explicit options above on every initial run.

The repository contains optional Gemini video uploads, Groq/OpenAI audio uploads, cookie options, and a managed WhisperX installer. Those capabilities were copied as source; they were not activated.

The URL download helper can inherit existing yt-dlp configuration. Do not use that route as though it were an isolated downloader. Start with the separate downloader guide, which supplies explicit configuration and cookie controls.

The setup hook only performs a setup check and prints an advisory. The optional installer can install Homebrew packages or fetch installers and model dependencies. Review the installation route and resolved dependencies for the target machine before running it. Avoid putting API keys in chat, committed files, shell commands, or reports.

## TheClipBar toolkit

| Source copy | Role |
| --- | --- |
| [theclipbar-yt-dlp](https://github.com/gonzoeps-cmd/theclipbar-yt-dlp) | Retrieve an authorized source video from a known URL. |
| [theclipbar-whisper](https://github.com/gonzoeps-cmd/theclipbar-whisper) | Produce local transcripts, caption files, and word timestamps. |
| [theclipbar-video-analysis](https://github.com/gonzoeps-cmd/theclipbar-video-analysis) | Produce timestamped visual evidence for reviewing and planning clips. |

A practical sequence is source retrieval, transcript and visual review, edit selection, then final editing in the existing ClipBar/FFmpeg or chosen editor workflow.

Keep TheClipBar's approved logo, pink/cyan/yellow styling, prominent captions, portrait framing, and deliberate sound cues in the final edit. These tools provide source material and analysis; they do not apply a complete branded video treatment.

Publishing remains through the established Metricool workflow with the user's authorization. Copying these repositories does not authorize publishing content.

## Review evidence

- [MIT license](https://github.com/bradautomates/claude-video/blob/03ceb42f7fa2c4439aca01752118044baabffb8f/LICENSE)
- [Engine and backend selection](https://github.com/bradautomates/claude-video/blob/03ceb42f7fa2c4439aca01752118044baabffb8f/skills/watch/scripts/config.py)
- [Entry point and local/cloud branches](https://github.com/bradautomates/claude-video/blob/03ceb42f7fa2c4439aca01752118044baabffb8f/skills/watch/scripts/watch.py)
- [Process helper](https://github.com/bradautomates/claude-video/blob/03ceb42f7fa2c4439aca01752118044baabffb8f/skills/watch/scripts/runtime.py)
- [Frame extraction](https://github.com/bradautomates/claude-video/blob/03ceb42f7fa2c4439aca01752118044baabffb8f/skills/watch/scripts/frames.py)
- [Downloader and inherited configuration](https://github.com/bradautomates/claude-video/blob/03ceb42f7fa2c4439aca01752118044baabffb8f/skills/watch/scripts/download.py)
- [Setup and optional installer](https://github.com/bradautomates/claude-video/blob/03ceb42f7fa2c4439aca01752118044baabffb8f/skills/watch/scripts/setup.py)

Review upstream changes before updating this fork. Preserve the MIT notice when redistributing copied code.
