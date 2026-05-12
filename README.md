# OCULA — Eye-Controlled Cursor

> A free, browser-based assistive input device.
> Move the cursor with your gaze. Dwell to click. No special hardware, no install — just your webcam.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![MediaPipe Face Mesh](https://img.shields.io/badge/MediaPipe-Face%20Mesh-4285F4.svg)](https://developers.google.com/mediapipe)
[![Accessibility](https://img.shields.io/badge/A11y-AAC%20demo-5eead4.svg)](#)
[![Open Source](https://img.shields.io/badge/Open%20Source-%E2%9D%A4-red.svg)](#)

---

## What is it?

OCULA is an open-source **eye-tracking cursor** built as a single HTML file. It uses your laptop webcam and MediaPipe Face Mesh to estimate where you're looking, smooths the signal, and lets you "click" by **dwelling** on a target for a fraction of a second.

The included demo is a 6-tile **AAC (Augmentative and Alternative Communication) board** — proof that a no-hands, no-hardware communication aid can run in a tab.

## Demo

Open `index.html`, allow camera access, run the 5-point calibration, and start gazing.

## Why it exists

Dedicated eye trackers like Tobii cost USD 200–3000+ and require drivers, dongles, and Windows software. Many people who would benefit from gaze input — ALS patients, RSI sufferers, late-stage cerebral-palsy users, anyone with a temporary arm injury — never get access.

OCULA is a **research / educational** project showing that "good enough for an AAC board, browser-only, free" is achievable on a 2019-era laptop.

## Features

- **Webcam-only gaze estimation** — no IR, no special camera
- **5-point on-screen calibration** with progress feedback
- **Dwell-to-click** with adjustable speed (fast / medium / slow)
- **Visual cursor** with progress ring showing dwell fill
- **AAC communication board** demo — 6 large gaze-clickable tiles
- **Recalibrate** without reloading
- **Face-not-detected warning** when the user moves out of frame
- **Welcome → calibrate → tracking** state machine with proper transitions
- Fully **client-side** — no frames or landmarks leave the browser

## Quick start

```bash
# 1. Just open it
open index.html

# 2. Or serve it (recommended — some browsers restrict camera on file://)
python3 -m http.server 8080
# then visit http://localhost:8080
```

1. Click **Begin**.
2. Look at each of the 5 calibration dots until the ring fills.
3. The AAC board appears. Look at a tile — when the cursor's progress ring fills (default 0.8 s) the tile activates.
4. Use the **Speed** button to cycle dwell time. Use **Recalibrate** if accuracy drifts (e.g. you moved).

## How it works

```
Webcam ──▶ MediaPipe Face Mesh ──▶ Iris + eye-corner landmarks
                                          │
                                          ▼
                                 Per-eye gaze vector
                                          │
                                          ▼
            5-point affine calibration → screen (x, y)
                                          │
                                          ▼
                       Exponential smoothing → cursor
                                          │
                                          ▼
                  Dwell timer per hovered tile → click
```

- **MediaPipe Face Mesh** gives 468 face landmarks at video frame rate, including dedicated iris landmarks.
- The relative position of each iris inside its eye socket is mapped to screen space via a calibration captured from 5 known points.
- A low-pass filter on the screen coordinates keeps the cursor stable.
- Hovering over a `.gaze-clickable` element starts a dwell timer; on completion the element's `data-action` fires.

## Calibration tips

- Sit roughly 50–70 cm from the camera.
- Keep your head reasonably still during calibration — head pose isn't compensated yet.
- Good even lighting on your face helps a lot. Backlight = bad.
- Recalibrate any time you change posture significantly.

## Limitations (honest list)

OCULA is not a medical device. It's a fun, open prototype.

- **No head-pose compensation** — moving your head shifts the gaze estimate.
- **Accuracy is roughly ~3–5 °** of visual angle, enough for 6 large tiles, not enough for general text input.
- **Webcam-dependent** — low light, glasses with strong reflections, or fish-eye lenses will degrade accuracy.
- Not benchmarked against clinical eye trackers.

PRs that improve any of the above are very welcome.

## Tech stack

- [MediaPipe Face Mesh](https://developers.google.com/mediapipe) `0.4` — face + iris landmarks
- [MediaPipe Camera Utils](https://developers.google.com/mediapipe) `0.3` — webcam plumbing
- Vanilla HTML / CSS / JS — no framework, no bundler, no npm

## Browser support

Works in any browser with `getUserMedia` + WASM SIMD. Tested on:
- Chrome 120+ (desktop)
- Edge 120+
- Safari 17+
- Firefox 121+

Mobile browsers technically run it but accuracy is poor — the demo is desktop-first.

## Privacy

Everything — face detection, iris tracking, calibration, smoothing — runs **inside your browser tab**. No camera frames, landmarks, or coordinates are uploaded anywhere. There is no backend.

## Roadmap / Ideas

- Head-pose decoupling (use full mesh, not just iris-in-socket)
- Saccade vs. fixation classifier instead of fixed dwell time
- On-screen keyboard with word prediction
- 9-point or 13-point calibration option
- Export gaze trace for research use

## Contributing

Issues and PRs welcome. This project is meant to be hacked on — please consider sharing improvements upstream so the broader accessibility community benefits.

## License

[MIT](LICENSE) — free for personal, educational, clinical, and commercial use.

## Credits

Built as an open experiment in webcam-only assistive tech. If OCULA helped you or someone you know, please open an issue and tell the story — it informs the roadmap.
