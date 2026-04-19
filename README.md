# Projector Overlay

A browser-based tool for projecting a YouTube video with a timed image overlay – for example, a church logo that fades in and out over a worship stream. Runs entirely in the browser with no build step or backend.

## Setup

Serve the folder over a local HTTP server and open `index.html` in your browser. Any of these work:

- **VS Code** – use whichever local server extension or built-in preview you already have
- **Python** – `python3 -m http.server` in the folder, then visit `http://localhost:8000`
- **Node** – `npx serve .`

> **Why not just open the file directly?** YouTube's IFrame API blocks playback when loaded from disk (`file://`). Any `http://localhost` origin fixes this.

## How to use

### Manager (`index.html`)

The manager is where you create and save projection setups called **sets**.

**Creating a set:**

1. Click **+ New set**.
2. Fill in the fields:
   - **Set name** – a label for your own reference (e.g. "Sunday Service")
   - **YouTube URL** – single videos, playlists and livestreams all work
   - **Overlay image** – PNG with transparency recommended; shown over the video
   - **Show every / Stay on for** – how often the overlay appears and how long it stays
   - **Fade in / Fade out** – transition duration in seconds (0 = instant)
   - **Easing curve** – the acceleration profile of the fade
   - **Video quality** – target resolution; YouTube may cap this based on your connection
3. Click **Save set**.

**Launching:**

- Click **Launch →** on any set. The output window opens in a new browser window.
- Move that window to your projector display, then press **F11** (Windows/Linux) or **Ctrl + Cmd + F** (Mac) for fullscreen.

**Switching sets mid-session:**

- Once the output window is open, the Launch button changes to **Switch →** on all other sets.
- Click **Switch →** to load a different set into the same window – no need to reposition or refullscreen.
- The currently playing set is marked with a green **● Live** indicator.

### Output window (`player.html`)

Opened automatically by the manager. Two keyboard shortcuts work here:

| Key | Action |
|-----|--------|
| Space | Pause / resume |
| F11 | Fullscreen (Windows/Linux) |

Closing the output window resets the manager back to **Launch →** buttons.

## Saving your sets

Sets are saved to your browser's `localStorage` and persist across sessions. They are stored in the browser only – not synced or backed up. Clearing site data in your browser will erase them.

Image files are stored as base64 inside localStorage. Browser limits vary but are typically 5–10 MB total. If saving fails, try a smaller or more compressed image.

## YouTube ads

The embedded player uses your active browser session. If you are logged into a **YouTube Premium** account in the same browser, ads will be suppressed – the iframe is a first-party YouTube frame and shares your session cookies. This is not officially documented by YouTube for the IFrame API but works in practice.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Manager – create, edit and launch sets |
| `player.html` | Output – full-screen video with overlay loop |
