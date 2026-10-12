# Active TheClipBar workflow

Activated and tested on 2026-10-12.

The three source copies are installed or checked out on the connected Mac in `~/Projects/TheClipBar-Toolkit`. This is the working preparation path for ClipBar edits. The Mac must be online for an assistant to operate these local tools.

## Verified setup

- Isolated Python environment: `~/Projects/TheClipBar-Toolkit/.venv`, Python 3.11.14.
- yt-dlp is installed from the ClipBar fork; reported package version 2026.08.19.
- OpenAI Whisper is installed from the ClipBar fork; reported package version 20250625.
- Visual analysis runs directly from the reviewed `skills/watch/scripts/watch.py` source.
- FFmpeg and ffprobe: 9.0.2. Deno: 2.9.6.
- Whisper model: official `base.en`, downloaded and loaded successfully, stored in `~/Projects/TheClipBar-Toolkit/models`.
- Transcription uses CPU, FP32, and four threads. No paid transcription API is required for this path.
- All 29 installed package versions passed the dependency compatibility check.
- A pip-audit scan reported zero known vulnerabilities and zero skipped packages among those 29 versions on this date. This is a version-based advisory check, not a complete code audit or a guarantee about future advisories.
- [Recorded dependency versions](requirements-macos-arm64-20261012.txt) pin the 27 dependencies. The two application packages are installed from their source checkouts.

The dependency snapshot contains version pins, not distribution hashes. Preserve the fork source revisions as well when reproducing the setup. Do not replace the reviewed source copies with same-version PyPI packages and assume they contain identical code.

### Source commits used for the test

| Checkout | Tested commit |
| --- | --- |
| theclipbar-yt-dlp | `60fdec1e8a3807bbd6562aca725e04a853ab5a85` |
| theclipbar-whisper | `9f8e5b59a5148788fd02dc40b3182e1143316439` |
| theclipbar-video-analysis | `dac3cd0d4810cbbe263df3d13de01bc0ed6fd599` |

The subsequent activation changes in the visual-analysis fork only add or update documentation and the dependency snapshot. They do not change the upstream analysis code.

## End-to-end test result

The downloader retrieved a real 15-second excerpt, source interval 00:30 to 00:45, from a source already used in the ClipBar project.

- Result: a playable H.264/AAC MP4, 1280 by 720 pixels, 60 fps, exactly 15.0 seconds.
- Visual analysis: 12 JPEG frames, including checks at three requested timestamps. An extracted frame was opened and visually inspected.
- Local transcription: 11 segments and 41 timed words. The last word ended at 14.98 seconds.
- Verified output formats: JSON, SRT, VTT, TXT, and TSV. Whisper also emitted a JSONL file.
- Word timings were checked for finite, ordered values within the excerpt.
- First transcription invocation took approximately 45 seconds for this sample. This is one observation, not a general performance benchmark.
- Test material and results remain under `work/validation-20261012`, outside all three source repositories.
- `source.json` records the original URL and a 30-second source offset. Frame and caption times refer to the local excerpt. Add the offset when locating the same moment in the original video.

The test verifies the tools and output structure. It does not establish perfect caption accuracy or that every video service will allow downloads.

## Use for a new ClipBar edit

These commands are for the connected Mac. Set the source URL, job name, and resulting local video path for the actual edit. Keep downloaded media, credentials, and generated captions outside Git.

### 1. Choose a job and get the source

```bash
CLIPBAR_ROOT="$HOME/Projects/TheClipBar-Toolkit"
CLIPBAR_JOB="$CLIPBAR_ROOT/work/your-clip-name"
CLIPBAR_URL='PASTE_THE_ORIGINAL_VIDEO_URL'
mkdir -p "$CLIPBAR_JOB/source"

"$CLIPBAR_ROOT/.venv/bin/yt-dlp" \
  --ignore-config --no-plugin-dirs \
  --no-cookies --no-cookies-from-browser --no-exec \
  --no-playlist --restrict-filenames \
  -f 'bv*[height<=720]+ba/b[height<=720]' \
  --merge-output-format mp4 \
  -P "$CLIPBAR_JOB/source" -o '%(id)s.%(ext)s' \
  -- "$CLIPBAR_URL"
```

A known interval can be downloaded with `--download-sections '*00:00:30-00:00:45'`. Record the original URL and the actual requested start/end times with that job. A section changes the time origin of the local media. Preserve this mapping.

The 720p selection is the tested preparation profile. Choose an appropriate higher source resolution separately when the final crop needs it, with the expected extra download and processing cost.

### 2. Extract visual evidence from the local file

```bash
CLIPBAR_VIDEO='/absolute/path/to/the/downloaded/video.mp4'

"$CLIPBAR_ROOT/.venv/bin/python" \
  "$CLIPBAR_ROOT/theclipbar-video-analysis/skills/watch/scripts/watch.py" \
  "$CLIPBAR_VIDEO" \
  --engine local --no-whisper --detail balanced --max-frames 100 \
  --out-dir "$CLIPBAR_JOB/visual" \
  > "$CLIPBAR_JOB/visual-report.md"
```

Read the generated frame images directly. For a brief moment, use a focused range or specific timestamps and check the original footage. A capped sample can miss an event. The tool supplies evidence; it does not guarantee original-source identification.

Always supply the local file and the explicit `--engine local --no-whisper` options. The upstream automatic mode can choose cloud services when keys exist. Its URL download path can inherit downloader configuration, which is why the separate downloader is used first.

### 3. Create local caption drafts

```bash
"$CLIPBAR_ROOT/.venv/bin/whisper" "$CLIPBAR_VIDEO" \
  --model base.en --model_dir "$CLIPBAR_ROOT/models" \
  --device cpu --fp16 False --threads 4 \
  --language en --word_timestamps True \
  --output_format all --output_dir "$CLIPBAR_JOB/captions"
```

This is the tested English-language profile. For other languages, choose and validate a multilingual model. Correct names, slang, overlapping dialogue, and timing before final captions are styled. Generated words can be wrong.

The media and transcription run locally on the Mac. Frames or transcripts intentionally supplied to an assistant are then processed in that assistant's environment.

## Finish the branded edit

Select a coherent moment using the footage, transcript, and frame evidence. Follow the approved ClipBar layout, logo, pink/cyan/yellow graphics, readable portrait framing, large captions, and sound cues timed to the dialogue. Use CapCut and OpusClip as editing references.

The tools above prepare and inspect material. Final editing, playback review, and user approval remain part of producing the finished video. Publishing continues through Metricool when authorized.

## Maintenance

Review source and dependency changes before updating. Recheck advisory results after an update. Keep the original licenses and copyright notices. Preserve the isolated environment so other Python projects are unaffected.

No plugin hook was registered, no WhisperX installer was run, and no production web-app deployment was made during this activation.
