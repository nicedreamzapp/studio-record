# Studio Record

A modern macOS screen + facecam recording app with virtual backgrounds, sound control, and a local HTTP API so any tool (Claude Code, scripts, agents) can drive it.

In one sentence: it records your screen, your webcam, or both, and lets a script start and stop that recording with a single `curl` call.

There are no screenshots or demo video in the repo yet. The whole app is one file, [`studio_record.py`](studio_record.py), and the API below is the quickest way to see it work.

<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s01-banner-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s01-banner-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s01-banner.svg" width="100%" alt="01. What it does. Screen, face, or both, driven by a script"></picture>
<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s01-r1-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s01-r1-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s01-r1.svg" width="100%" alt="Screen + face. Records your screen, your webcam, or both. One curl call. A script can start and stop a recording. Virtual backgrounds. Apple Vision person segmentation on the webcam."></picture>

- **Screen recording**: full display capture at 30 fps through ffmpeg (`avfoundation`), with cursor and clicks, hardware-encoded with `h264_videotoolbox`
- **Face recording**: webcam saved as 1920x1080, 30 fps, with an optional virtual background (Apple Vision person segmentation)
- **Picture-in-picture**: in Screen + Face mode a draggable, always-on-top facecam window floats over your screen, so it is captured in the same screen recording
- **Sound control**: mute toggle (sets macOS input volume to 0 while recording, then restores it) and a cleanup chain on the mic track (high-pass, denoise, compressor, EQ, gate, loudness normalize)
- **Audio/video sync**: mic is captured separately at 48 kHz, trimmed to line up with the first video frame, then muxed into one `.mp4`
- **Liquid Glass UI**: built with customtkinter
- **HTTP API**: POST `/start`, POST `/stop`, GET `/status` on `localhost:17494` so any agent can control it

<img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s01-rule.svg" width="100%" alt="">

<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s02-banner-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s02-banner-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s02-banner.svg" width="100%" alt="02. What I built. Everything lives in one file"></picture>
<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s02-r1-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s02-r1-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s02-r1.svg" width="100%" alt="One file. The whole app is studio_record.py. Three modes. Screen, face, and screen plus face."></picture>

Matt Macosko wrote everything in [`studio_record.py`](studio_record.py):

- The recorder app and its three modes: [`StudioRecordApp`](studio_record.py#L117), [`_set_mode`](studio_record.py#L297)
- Auto-detection of the screen and preferred mic (USB mic first, then MacBook mic): [`_detect_devices`](studio_record.py#L48)
- Virtual background compositing on a background thread, using the Apple Vision segmentation mask: [`_apple_vision_mask`](studio_record.py#L564), [`_process_frame`](studio_record.py#L601), [`_capture_loop`](studio_record.py#L632)
- Recording pipelines, audio cleanup and muxing: [`_start_face_recording`](studio_record.py#L787), [`_start_screen_recording`](studio_record.py#L822), [`_stop_face_recording`](studio_record.py#L878), [`_stop_screen_recording`](studio_record.py#L992)
- Floating picture-in-picture window: [`_open_pip`](studio_record.py#L382)
- Mic mute through AppleScript: [`_mute_mic`](studio_record.py#L359)
- The local HTTP API: [`_run_api`](studio_record.py#L1092)

Upstream tools it relies on (see [CREDITS.md](CREDITS.md)): ffmpeg, Apple Vision (via PyObjC), OpenCV, CustomTkinter, Flask, sounddevice, NumPy, Pillow, and MediaPipe.

<img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s02-rule.svg" width="100%" alt="">

<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s03-banner-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s03-banner-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s03-banner.svg" width="100%" alt="03. Install and run. Apple Silicon Mac, Python 3.10+"></picture>
<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s03-r1-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s03-r1-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s03-r1.svg" width="100%" alt="Apple Silicon. M1 or later. Python 3.10+. Plus ffmpeg from Homebrew. Permissions. Camera, Microphone and Screen Recording."></picture>

- Apple Silicon Mac (M1 or later)
- Python 3.10+
- ffmpeg (`brew install ffmpeg`). The app calls it at `/opt/homebrew/bin/ffmpeg`, which is where Homebrew installs it on Apple Silicon.
- macOS permissions for your terminal: Camera, Microphone, and Screen Recording

### Setup

```bash
git clone https://github.com/nicedreamzapp/studio-record
cd studio-record
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pip install pyobjc-framework-Vision   # needed for virtual backgrounds, not in requirements.txt yet

# The app reads backgrounds from here, not from the repo folder
mkdir -p ~/Desktop/Screen\ Recordings/backgrounds
cp backgrounds/* ~/Desktop/Screen\ Recordings/backgrounds/
```

### Run

```bash
.venv/bin/python studio_record.py
```

The window opens AND a Flask API starts on `http://127.0.0.1:17494`.

<img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s03-rule.svg" width="100%" alt="">

<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s04-banner-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s04-banner-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s04-banner.svg" width="100%" alt="04. HTTP API. localhost:17494"></picture>
<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s04-r1-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s04-r1-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s04-r1.svg" width="100%" alt="POST /start. Modes: screen, face, screen_face. POST /stop. Waits for the file, returns its path. GET /status. Recording, outpath, mode, duration."></picture>

```bash
# Start a recording
curl -X POST "http://127.0.0.1:17494/start?mode=screen"
# Modes: screen | face | screen_face  (default: screen)

# Stop (waits for the file to be written, then returns its path)
curl -X POST "http://127.0.0.1:17494/stop"

# Status: recording, outpath, mode, duration
curl "http://127.0.0.1:17494/status"
```

Recordings are saved to `~/Desktop/Screen Recordings/`.

### Pairing with Claude Code

Studio Record was built to be driven by [claude-screen-to-phone](https://github.com/nicedreamzapp/claude-screen-to-phone). When paired, you can say things like *"record this and send it to me"* and Claude will start the recording, do the task, stop the recording, and ship the video to your phone via iMessage. That flow lives in the other repo; this repo only provides the recorder and its API.

<img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s04-rule.svg" width="100%" alt="">

<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s05-banner-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s05-banner-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s05-banner.svg" width="100%" alt="05. Backgrounds and limits. What to know before you use it"></picture>
<picture><source media="(max-width: 610px), (min-width: 768px) and (max-width: 895px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s05-r1-m.svg"><source media="(max-width: 1149px)" srcset="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s05-r1-mid.svg"><img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s05-r1.svg" width="100%" alt="macOS only. Paths are hard-coded. No auth. The API only listens on 127.0.0.1. No tests. None yet."></picture>

The `backgrounds/` folder contains the virtual background images (Apple wallpapers + abstract gradients) the face-mode segmentation overlays. The app loads them from `~/Desktop/Screen Recordings/backgrounds/` (see Setup). Drop any `.jpg`, `.png`, `.webp` or `.bmp` in that folder to add your own.

### Known limits

- macOS only. Paths (`/opt/homebrew/bin/ffmpeg`, `~/Desktop/Screen Recordings/`) are hard-coded.
- Virtual backgrounds need Apple Vision through PyObjC (`pyobjc-framework-Vision`), which is not listed in `requirements.txt`. The code has a MediaPipe fallback, but the MediaPipe segmenter is never created, so that fallback does not run today.
- Screen mode records one display (the one ffmpeg lists as the capture screen).
- The API has no auth. It only listens on `127.0.0.1`.
- Calling `/start` while a recording is running does not start a second one or change the mode.
- No tests.

<img src="https://raw.githubusercontent.com/nicedreamzapp/studio-record/main/assets/readme/s05-rule.svg" width="100%" alt="">
