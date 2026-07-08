# 🎾 TennisVision — In-Browser Ball & Player Tracker

Live demo: **https://YOUR-USERNAME.github.io/tennis-tracker/** ← open on your phone, works instantly

TennisVision tracks a tennis ball and players in real time from a video clip or your phone camera — entirely in the browser. No servers, no OpenCV, no ML libraries. Every algorithm is implemented from scratch in ~350 lines of vanilla JavaScript.

![TennisVision screenshot](screenshot.png)

## What it does

- **Ball tracking** — locks onto the ball by color and follows it frame to frame, even through brief occlusions (the marker turns red and "coasts" on prediction alone when the ball is hidden)
- **Bounce detection** — counts bounces by detecting vertical velocity sign flips, marking each bounce spot with a fading ✕
- **Player tracking** — detects up to 2–3 moving players and follows them with labeled boxes
- **Live stats** — bounces, ball speed, player count, frames processed
- **Tap-to-calibrate** — tap the ball once in the video to lock its exact color (works with any ball color/lighting)

## How it works

| Component | Technique |
|---|---|
| Ball detection | HSV color segmentation → densest-cell grid search → centroid refinement |
| Ball tracking | 6-state constant-acceleration **Kalman filter** (position, velocity, acceleration) with gated updates and coasting on missed detections |
| Player detection | Frame-difference motion blobs with flood-fill grouping |
| Player tracking | 4-state constant-velocity Kalman filters with greedy nearest-neighbor association |
| Math | Hand-written matrix library (multiply, transpose, Gauss–Jordan inverse) — the Kalman update uses the numerically stable Joseph form |

The pipeline downsamples each frame to 320px wide for analysis, runs detection + filtering, then renders trails, boxes, and bounce marks back onto the full-resolution canvas.

## Try it

1. Open the live demo on any phone or laptop
2. **Load clip** (pick a rally video) or **Use camera**
3. Press **Play**, then **tap the ball once** to lock its color
4. Watch the yellow trail follow it — red segments mean the Kalman filter is predicting through an occlusion

Tip: on iPhone, open the live link in Safari → Share → **Add to Home Screen** to install it like an app.

## Why I built it

I wanted to understand how object tracking actually works under the hood instead of importing a library. Writing the Kalman filter from the math up — state transition matrices, process noise, measurement updates — made concepts like prediction vs. correction concrete. It also had to run at 30fps on a phone, which forced real optimization decisions (grid-based detection instead of per-pixel blob labeling, downsampled analysis canvas, typed arrays).

## Tech

Vanilla JavaScript · Canvas 2D · MediaDevices API · zero dependencies · single HTML file

---

*Built by Eeshan*
