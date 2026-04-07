# Studio Record

A modern macOS screen + facecam recording app with virtual backgrounds, sound control, and a local HTTP API so any tool (Claude Code, scripts, agents) can drive it.

## What it does

- **Screen recording** — full screen capture
- **Face recording** — webcam with optional virtual background (MediaPipe segmentation)
- **Picture-in-picture** — screen + facecam overlay in one recording
- **Liquid Glass UI** — built with customtkinter
- **HTTP API** — POST `/start`, POST `/stop`, GET `/status` on `localhost:17494` so any agent can control it

## Requirements

- Apple Silicon Mac (M1 or later)
- Python 3.10+
- ffmpeg (`brew install ffmpeg`)

## Setup

```bash
git clone https://github.com/nicedreamzapp/studio-record
cd studio-record
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Run

```bash
.venv/bin/python studio_record.py
```

The window opens AND a Flask API starts on `http://127.0.0.1:17494`.

## HTTP API

```bash
# Start a recording
curl -X POST "http://127.0.0.1:17494/start?mode=screen"
# Modes: screen | face | screen_face

# Stop
curl -X POST "http://127.0.0.1:17494/stop"

# Status
curl "http://127.0.0.1:17494/status"
```

Recordings are saved to `~/Desktop/Screen Recordings/`.

## Pairing with Claude Code

Studio Record was built to be driven by [claude-screen-to-phone](https://github.com/nicedreamzapp/claude-screen-to-phone) — when paired, you can say things like *"record this and send it to me"* and Claude will start the recording, do the task, stop the recording, and ship the video to your phone via iMessage.

## Backgrounds

The `backgrounds/` folder contains the virtual background images (Apple wallpapers + abstract gradients) the face-mode segmentation overlays. Drop any `.jpg` or `.png` in there to add your own.
