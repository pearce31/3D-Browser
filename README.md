# 3D Browser

A **3D web browser** for Android (TV, phone, tablet, and projector). It loads any
website in a WebView and turns the page's video into real-time 3D with a WebGL shader —
generating pseudo-depth from flat 2D video, converting real Side-by-Side / Top-Bottom
stereoscopic footage, or passing through existing anaglyph content. It even runs the
shader on cloud-gaming streams (GeForce NOW).

You can view the result two ways:

- **Anaglyph** — on any ordinary screen, with green/magenta or red/cyan glasses (works
  great with full-color sets like TriOviz InfiColor).
- **Xreal SBS** — split per-eye for side-by-side glasses/headsets (Xreal and similar),
  so you get the 3D hands-free, no colored glasses needed.

Tested on NVIDIA Shield, a Dangbei Atom projector, phones, and Xreal glasses. Installs
as its own app — it doesn't replace your normal browser.

---

## Features

- **3D on any site's video** — a live WebGL overlay, not just YouTube. Works on web
  players, self-hosted media, and GeForce NOW game streams.
- **Full-color anaglyph** — keeps the original red and blue untouched and only shifts
  green to carry the depth, so color survives instead of washing out. Pops hard on flat
  video and games alike.
- **Two color schemes** — green/magenta (best color) and red/cyan.
- **Source modes** (what the incoming video *is*):
  - **2D** — fake depth from flat video (luminance + convergence based).
  - **Side-by-Side** / **Top-Bottom** — converts real stereoscopic video, with
    crosstalk cancellation, auto-unsqueezing full- or half-packed frames.
  - **Auto** — detects full-frame SBS / Top-Bottom by frame shape.
  - **Anaglyph (passthrough)** — the video is *already* a green/magenta or red/cyan
    anaglyph; shows it untouched (for your glasses), or de-muxes it cleanly into an
    Xreal headset.
- **Xreal SBS output** — a toggle that splits the image into left/right halves with a
  simulated per-eye lens tint (magenta-left, green-right), so SBS glasses reproduce the
  anaglyph look with no physical glasses. Auto-detects a 3840×1080 ultrawide signal and
  switches each eye to a full, sharp 1920×1080.
- **Swap** — context-aware: in 2D it flips green/magenta (depth polarity, for standard
  green-left glasses vs TriOviz magenta-left); in Xreal SBS it flips the two halves if
  they land on the wrong eyes.
- **Tunable 3D** — Depth, Edge clean, Bright gain, Ghost fill (SBS/TAB), Converge, Lens
  tint (Xreal SBS), plus reset-to-default. Settings persist.
  - **Edge clean** (2D) — eases depth at sharp edges so text/HUDs don't split, and tames
    color fringe, while leaving smooth areas at full pop.
  - **Ghost fill** — real stereo (SBS/TAB) only; cancels crosstalk between the two views.
  - **Lens tint** (Xreal SBS) — 0 = pure channels (cleanest; the headset separates the
    eyes so no bleed is wanted); raise it for more color per eye at the cost of ghosting.
- **D-pad cursor** (TV) — smooth, constant-speed pointer that clicks with a **real**
  Android touch, so trusted-click targets like reCAPTCHA checkboxes and on-screen
  keyboards work. Auto-hides when idle. Touch works directly on phones/tablets.
- **GeForce NOW** — auto-detected: the cursor drives the home grid, and when a game goes
  fullscreen the raw controller/d-pad is handed straight to the game, then the cursor
  returns on exit. The 3D shader runs on the game stream.
- **Game mode** — manual toggle that hands the raw d-pad/controller to the page, for
  other web-game or cloud sites that do their own controller navigation.
- **Low power mode** — for weak GPUs (e.g. the Dangbei Atom): caps the capture at ~720p
  / ~25fps and holds YouTube at 1080p so the device's WebGL context doesn't get overrun.
  **Off by default** — leave it off on capable hardware for full resolution.
- **Context auto-recovery** — if the GPU drops the WebGL context, the overlay rebuilds
  itself instead of locking to a white or black screen.
- **Immersive** — the phone navigation and status bars are hidden (full screen), which
  also keeps the SBS halves aligned. No effect on TV.
- **Browser basics** — URL/search bar (Google), favorites on the home screen, history.
- **Ad blocking** — network-level ad/tracker blocking (toggleable, since some sites use
  anti-adblock) plus YouTube video-ad skip and banner-ad hiding. The setting is global
  and persists.
- **Device-aware** — desktop layout on TV, mobile on phones; YouTube goes to its TV
  (leanback) interface on a TV so the remote works natively.
- **Highest quality** — pushes YouTube to the top resolution the player exposes (up to
  4K/8K); capped at 1080p under Low power mode.
- **Cleartext HTTP allowed** — local / self-hosted servers (Emby, Plex, Jellyfin) load.

## Controls

| Input | Action |
|---|---|
| D-pad | Move the cursor / navigate YouTube's TV UI / drive a GeForce NOW game |
| OK | Click under the cursor (real tap) / select |
| **Hold OK** (TV) or the **● 3D** button (touch) | Open the 3D settings panel |
| In the panel | Up/Down/Left/Right between controls, Left/Right adjust a slider, OK toggles |
| Back | Browser back; exits fullscreen; closes the panel if open |

The **● 3D** button on touch devices is draggable and remembers its spot.

## Viewing in Xreal (or other SBS glasses)

1. In the panel, turn **Xreal SBS: ON** and put the glasses in their **SBS / 3D** mode.
2. Pick your **Source**: *2D* for flat video, *Side-by-Side* / *Top-Bottom* / *Auto* for
   real 3D video, or *Anaglyph (passthrough)* for existing anaglyph clips.
3. If the image lands on the wrong eyes, hit **Swap**.
4. Leave **Lens tint** at the bottom (0) — it looks cleanest there.

**Anamorphic (default):** works at your normal resolution with no setup — each half is
squeezed and the glasses unsqueeze it.

**Sharp 3840×1080 mode:** set the device to output **3840×1080** to the glasses (as a
**second screen / display output**). The app auto-detects that ultrawide signal and
gives each eye a full, crisp 1920×1080. Caveat: 3840×1080 turns the phone's own screen
into a thin strip that's hard to touch, so **use a Bluetooth mouse / air-mouse** (the
cursor works), or set everything up first and switch the resolution last.

> For **real** side-by-side videos you can also just use the Xreal's own native SBS mode
> directly — it's full color and lossless. This app's unique value is turning *flat* 2D
> video into 3D, de-muxing *anaglyph* content, and converting *Top-Bottom* (which the
> glasses don't handle) — things the glasses can't do themselves.

## How it works

A thin native WebView shell loads a local home page and injects two editable asset files
on every page — so you can tweak behavior on-device without recompiling:

- `assets/home.html` — the browser home (search bar, favorites, history).
- `assets/app.js` — the shader, 3D panel, cursor, ad handling, and site tweaks.

The native shell handles per-host user agents, the ad-blocking request filter, the
D-pad → cursor/panel routing, the real-touch click, GeForce NOW controller passthrough,
the on-screen keyboard, immersive full screen, and bookmark/history storage.

## Install

Download the APK from [Releases](../../releases) and sideload it. It appears on the
Android TV home row and in the app drawer. Signed with a debug key — if you rebuild it
yourself, uninstall the old copy first (different signature).

## Sign-in

Google blocks account login inside WebViews, so use the code method: pick **Sign in**,
then enter the code it shows at the given URL (e.g. **youtube.com/activate**) on your
phone or PC. That flow doesn't use the blocked web login.

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
- **Ad blocking** can trip some sites' anti-adblock — turn it **off** in the panel for
  those (it's a single global setting).
- Anaglyph ghosting can't be fully eliminated (the glasses physically leak); lowering
  **Depth** and nudging **Converge** so the background sits near the screen plane gives
  the cleanest result. For 2D, **Edge clean** handles text/HUD splitting and fringe.
- **3840×1080 Xreal mode** requires the device to output that resolution to the glasses,
  which shrinks the phone's own screen — pair a mouse, or set up before switching.
- On very weak devices the shader can overrun the GPU — turn on **Low power mode**. If
  playback still drops out, the device may route video on a hardware overlay the shader
  can't read, or you can lower the source stream's own resolution.
- Fullscreen keeps 3D by filling the screen within the page; it isn't native HTML5
  fullscreen (which would bypass the overlay).

## Credits

Built on my own Anaglyph Browser WebView shell and the SBS→anaglyph conversion from my
SBS-to-Anaglyph app. No third-party site code is bundled — it only loads public websites.

## License

Released under the **MIT License** — see `LICENSE`. Free to use, modify, and
redistribute, shaders included.

## Disclaimer

Not affiliated with, endorsed by, or sponsored by any website or service it can load
(YouTube, Emby, GeForce NOW, Xreal, TriOviz, etc.). Trademarks belong to their owners.
Personal hobby project that displays public websites — use it in accordance with those
sites' terms. Provided as-is, with no warranty.
