# 3D Browser

An **anaglyph 3D web browser** for Android (TV, phone, and tablet), for use with
green/magenta or red/cyan glasses. It loads any website in a WebView and composites
the page's video into 3D in real time with a WebGL shader — generating pseudo-depth
from flat 2D video, or converting real Side-by-Side / Top-Bottom stereoscopic footage.
It even runs the shader on cloud-gaming streams (GeForce NOW). Works great on an
Android TV box (tested on NVIDIA Shield and a Dangbei Atom projector) with a D-pad
remote, and on touch devices.

Installs as its own app — it doesn't replace your normal browser.

---

## Features

- **3D on any site's video** — a live WebGL overlay, not just YouTube. Works on web
  players, self-hosted media, and even GeForce NOW game streams.
- **Full-color anaglyph** — the shader keeps the original red and blue untouched and
  only shifts green to carry the depth, so color survives instead of washing out. Pops
  hard on both flat video and games.
- **Two color schemes** — green/magenta and red/cyan.
- **Source modes:**
  - **2D** — fake depth from flat video (luminance + convergence based).
  - **Side-by-Side** / **Top-Bottom** — converts real stereoscopic video, with
    subtractive crosstalk cancellation, auto-unsqueezing full- or half-packed frames.
  - **Auto** — detects full-frame SBS / Top-Bottom by frame shape.
  - **Swap L/R** — for reversed stereo clips.
- **Auto fill-screen** — when a video plays, the 3D fills the screen; a tap or D-pad
  press briefly drops it so you can reach the player controls.
- **Tunable 3D** — Depth, Edge clean, Bright gain, Ghost fill (SBS/TAB), Converge, plus
  a reset-to-default. Settings persist.
  - **Edge clean** (2D only) — eases the depth at sharp edges so on-screen text and HUDs
    don't split into a double image, and tames leftover color fringe, while leaving
    smooth areas at full pop.
  - **Ghost fill** applies to real stereo (SBS/TAB) only, where it cancels crosstalk
    between the two views.
- **D-pad cursor** (TV) — a smooth, constant-speed pointer that clicks with a **real**
  Android touch, so trusted-click targets like reCAPTCHA checkboxes and on-screen
  keyboard fields work. Auto-hides when idle. Touch works directly on phones/tablets.
- **GeForce NOW** — auto-detected: the cursor drives the home grid, and the moment a
  game goes fullscreen the raw controller/d-pad is handed straight to the game, then the
  cursor returns on exit. The 3D shader runs on the game stream.
- **Game mode** — a manual toggle that hands the raw d-pad/controller to the page, for
  any other web-game or cloud site that does its own controller navigation.
- **Low power mode** — for weak GPUs (e.g. the Dangbei Atom): caps the capture at ~720p
  / ~25fps and holds YouTube at 1080p so the device's WebGL context doesn't get overrun.
  **Off by default** — leave it off on capable hardware for full resolution.
- **Context auto-recovery** — if the GPU drops the WebGL context, the overlay rebuilds
  itself instead of locking to a white or black screen.
- **Browser basics** — URL/search bar (Google), favorites on the home screen, history.
- **Ad blocking** — network-level ad/tracker blocking (toggleable per the panel, since
  some sites use anti-adblock), plus YouTube video-ad skip and banner-ad hiding.
- **Device-aware** — desktop layout on TV, mobile layout on phones; YouTube is sent to
  its TV (leanback) interface on a TV so the remote works natively.
- **Highest quality** — pushes YouTube to the top available resolution (up to 4K/8K
  where the player exposes it); capped at 1080p when Low power mode is on.
- **Cleartext HTTP allowed** — local / self-hosted servers (Emby, Plex, Jellyfin) load.

## Controls

| Input | Action |
|---|---|
| D-pad | Move the cursor / navigate YouTube's TV UI / drive a GeForce NOW game |
| OK | Click under the cursor (real tap) / select |
| **Hold OK** (TV) or the **● 3D** button (touch) | Open the 3D settings panel |
| In the panel | Up/Down between controls, Left/Right adjust a slider, OK toggles |
| Back | Browser back; exits fullscreen; closes the panel if open |

The **● 3D** button on touch devices is draggable — move it anywhere clear and it
remembers the spot.

## How it works

A thin native WebView shell loads a local home page and injects two editable asset
files on every page — so you can tweak behavior on-device without recompiling:

- `assets/home.html` — the browser home (search bar, favorites, history).
- `assets/app.js` — the shader, 3D panel, cursor, ad handling, and site tweaks.

The native shell handles per-host user agents, the ad-blocking request filter, the
D-pad → cursor/panel routing, the real-touch click, GeForce NOW controller passthrough,
the on-screen keyboard, and bookmark/history storage.

## Install

Download the APK from [Releases](../../releases) and sideload it. It shows up on the
Android TV home row and in the app drawer. Signed with a debug key — if you rebuild it
yourself, uninstall the old copy first (different signature).

## Sign-in

Google blocks account login inside WebViews, so use the code method: pick **Sign in**,
then enter the code at the URL it shows (e.g. **youtube.com/activate**) on your phone or
PC. That flow doesn't use the blocked web login.

## Build from source

This is an [apktool](https://apktool.org/) project (not Gradle) — builds on a PC or
on-device:

```
apktool b 3dbrowser -o 3dbrowser.apk
```

Then sign it (any key works for sideloading — `apksigner`, or an on-device signer like
APK Editor Pro). To change the 3D behavior or UI, edit `assets/app.js` /
`assets/home.html` — no native rebuild needed.

## Limitations

- **Sign-in** must use the code-pairing flow above.
- **Ad blocking** can trip some sites' anti-adblock — turn the Ad blocker **off** in the
  panel for those.
- Anaglyph ghosting can't be fully eliminated (the glasses physically leak); lowering
  **Depth** and nudging **Converge** so the background sits near the screen plane gives
  the cleanest result. For 2D, **Edge clean** handles text/HUD splitting and fringe.
- On very weak devices the shader can overrun the GPU — turn on **Low power mode**. If
  playback still drops out, the device may be routing video on a hardware overlay the
  shader can't read, or you can lower the source stream's own resolution.
- Fullscreen keeps 3D by filling the screen within the page; it isn't native HTML5
  fullscreen (which would bypass the 3D overlay).

## Credits

Built on my own Anaglyph Browser WebView shell and the SBS→anaglyph conversion from my
SBS-to-Anaglyph app. No third-party site code is bundled — it only loads public websites.

## License

Released under the **MIT License** — see `LICENSE`. Free to use, modify, and
redistribute, shaders included.

## Disclaimer

Not affiliated with, endorsed by, or sponsored by any website or service it can load
(YouTube, Emby, GeForce NOW, etc.). Trademarks belong to their owners. Personal hobby
project that displays public websites — use it in accordance with those sites' terms.
Provided as-is, with no warranty.
