# Projector Overlay

A browser-based projection control system that synchronizes YouTube videos (and local video files) with timed, animated image overlays. Built for live events like church services. Runs entirely in the browser with no build step or backend.

## Setup

Run this in the project folder, then open `http://localhost:8000` in your browser:

```
python3 -m http.server
```

Alternatively: `npx serve .` (Node), or any VS Code local server extension.

> **Why not just open the file directly?** YouTube's IFrame API blocks playback when loaded from `file://`. Any `http://localhost` origin fixes this.

## Screenshots

### Manager

![Manager — set list](screenshots/manager-list.png)

![Manager — editing a set](screenshots/manager-edit.png)

### Live control panel

![Live control panel](screenshots/manager-live.png)

### Output window

![Player — overlay on video](screenshots/player-overlay.png)

### Visual effects — worm drag

![Worm drag effect](screenshots/player-worm-drag.png)

![Worm drag — stamp accumulation](screenshots/player-worm-stamps.png)

---

## How to use

### Manager (`index.html`)

The manager is where you build and launch **sets** — saved projection configurations.

**Creating a set:**

1. Click **+ New set**.
2. Give the set a name (e.g. "Sunday Service") and an optional scheduled time.
3. Add one or more videos — YouTube URLs, playlists, livestreams, or local video files.
4. Add one or more overlay images.
5. Configure overlay timing, fade, effects, and position (see below).
6. Click **Save set**.

**Launching:**

- Click **Launch →** on any set. The output window opens in a new browser window.
- Move that window to your projector display and press **F11** (Windows/Linux) or **Ctrl + Cmd + F** (Mac) for fullscreen.
- While live, the manager shows a live control panel with media tiles you can click to seek to a specific video or overlay image.

**Switching sets mid-session:**

- Once the output window is open, **Switch →** appears on all other sets.
- Click **Switch →** to load a different set into the same window — no need to reposition or re-fullscreen.
- The active set is marked with a green **● Live** indicator.

### Output window (`player.html`)

Opened automatically by the manager.

| Key | Action |
|-----|--------|
| Space | Pause / resume |
| F11 | Fullscreen (Windows/Linux) |

Closing the output window resets the manager back to **Launch →** buttons.

---

## Feature overview

### Multiple videos per set

Each set can contain a sequence of videos that play in order. Per video:

- YouTube URLs (single videos, playlists, livestreams, shorts, embeds) or local video files
- **Start time** – seek to a specific timestamp before playback begins
- **Max duration** – cap how long the video plays before advancing to the next one
- **Quality preset** – auto, 240p up to 4K (YouTube may further cap based on your connection)
- **Mute on launch** – optional

The manager calculates and displays the total runtime for a set, accounting for per-video duration caps.

### Multiple overlays per set

Each set can contain multiple overlay images that cycle in sequence. Per overlay:

- PNG or JPEG image
- **Show every** – how often the overlay appears (cycle interval)
- **Stay on for** – how long the overlay remains visible
- **Fade in / out** – transition duration in seconds (0 = instant)
- **Easing curve** – acceleration profile (ease, linear, ease-in, ease-out, ease-in-out)
- **Size** – 10–200% of viewport
- **Position** – drag the overlay on the canvas preview to position it; a rule-of-thirds grid assists placement

### Visual effects (GPU-accelerated)

Overlays can have an animated GPU effect applied via WebGL (PixiJS):

| Effect | Description |
|--------|-------------|
| Wave-H | Horizontal wave distortion |
| Wave-V | Vertical wave distortion |
| Ripple | Concentric radial ripples |
| Twist | Rotational distortion from center |
| Swirl | Rotating vortex |
| Glitch | Random scanline offsets (VHS-style) |
| Pixelate | Animated pixel block size |
| Worm drag | Cursor writing animation with autonomous path generation |

Each effect has a speed control. Effects degrade gracefully — the base overlay still works without WebGL.

### Bounce mode

Set an overlay to bounce around the viewport instead of appearing at a fixed position. Configurable bounce speed (10–600 px/s).

### Multi-display (leader/follower)

A launched set can drive additional follower displays — useful for side screens or confidence monitors. Follower windows sync state via BroadcastChannel and self-correct if they drift more than 1.25 seconds behind the leader.

### Live control panel

While a set is running, the manager shows:

- Current video and overlay index
- Pause/resume overlay cycling independently of the video
- Media tiles — click to seek to any video or overlay in the sequence
- A muted preview of the live output
- Live wall clock

---

## Storage

Sets are saved as JSON. Two storage backends are supported:

| Backend | How it works |
|---------|-------------|
| **File System** (preferred) | Uses the File System Access API — you pick a local folder and sets are saved as `.json` files you can back up or move |
| **IndexedDB** (fallback) | Stored in the browser; no folder required but not portable |

Local video files are stored as blobs in IndexedDB with automatic duration caching. Images are compressed automatically to a maximum of 1920px before saving.

**Export / Import**: Use the export button to save a full JSON backup of all sets. Import to restore.

---

## YouTube ads

The embedded player uses your active browser session. If you are logged into a **YouTube Premium** account in the same browser, ads will be suppressed — the iframe shares your session cookies. Not officially documented by YouTube but works in practice.

---

## Files

| File | Purpose |
|------|---------|
| `index.html` | Manager — create, edit, and launch sets |
| `player.html` | Output — full-screen video with overlay loop |
