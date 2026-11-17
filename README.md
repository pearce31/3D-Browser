# 3D Browser

An **anaglyph 3D web browser** for Android (TV, phone, and tablet), for use with
green/magenta or red/cyan glasses. It loads any website in a WebView and composites
the page's video into 3D in real time with a WebGL shader — generating pseudo-depth
from flat 2D video, or converting real Side-by-Side / Top-Bottom stereoscopic footage.
Works great on an Android TV box (tested on NVIDIA Shield) with a D-pad remote, and on
touch devices.

Installs as its own app — it doesn't replace your normal browser.

---

## Features

- **3D on any site's video** — a live WebGL overlay, not just YouTube.
- **Two color schemes** — green/magenta and red/cyan.
- **Source modes:**
  - **2D** — fake depth from flat video (luminance + convergence based).
  - **Side-by-Side** / **Top-Bottom** — converts real stereoscopic video, with
    subtractive crosstalk cancellation, auto-unsqueezing full- or half-packed frames.
  - **Auto** — detects full-frame SBS / Top-Bottom by frame shape.
  - **Swap L/R** — for reversed stereo clips.
- **Auto fill-screen** — when a video plays, the 3D fills the screen; a tap or D-pad
  press briefly drops it so you can reach the player controls.
- **Tunable 3D** — Depth, Bright gain, Ghost fill, Desaturate, Converge, plus a
  reset-to-default. Settings persist.
- **D-pad cursor** (TV) — a smooth, constant-speed pointer with click; auto-hides when
  idle. Touch works directly on phones/tablets.
- **Browser basics** — URL/search bar (Google), favorites on the home screen, history.
- **Ad blocking** — network-level ad/tracker blocking (toggleable per the panel, since
  some sites use anti-adblock), plus YouTube video-ad skip and banner-ad hiding.
- **Device-aware** — desktop layout on TV, mobile layout on phones; YouTube is sent to
  its TV (leanback) interface on a TV so the remote works natively.
- **Highest quality** — pushes YouTube to the top available resolution (up to 4K/8K
  where the player exposes it).
- **Cleartext HTTP allowed** — local / self-hosted servers (Emby, Plex, Jellyfin) load.

## Controls

| Input | Action |
|---|---|
| D-pad | Move the cursor (TV) / navigate YouTube's TV UI |
| OK | Click under the cursor / select |
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
D-pad → cursor/panel routing, the on-screen keyboard, and bookmark/history storage.

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
  the cleanest result.
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
(YouTube, Emby, etc.). Trademarks belong to their owners. Personal hobby project that
displays public websites — use it in accordance with those sites' terms. Provided
as-is, with no warranty.
