# Previewing Overlays

Before going live, always preview and test your overlays. There are two ways to do this.

---

## Method 1 — Preview Directly in a Browser (Fastest)

You can open any overlay HTML file directly in Chrome or Edge to check its appearance and debug errors.

1. Open **Chrome** or **Edge** (not Safari — Safari blocks `ws://` connections to localhost by default).
2. Press `Ctrl+O` (Windows) or `Cmd+O` (Mac).
3. Navigate to your HTML file and open it.
4. The overlay will load and attempt to connect to Lumia Stream.

> **Make sure Lumia Stream is running** before opening the file, otherwise the WebSocket connection will fail. The overlay should still render its default/empty state even if not connected.

### Checking for errors

1. Right-click anywhere on the page → **Inspect** (or press `F12`).
2. Click the **Console** tab.
3. Look for red error messages.

Common errors:
| Error | Cause | Fix |
|-------|-------|-----|
| `WebSocket connection failed` | Lumia Stream not running | Start Lumia Stream |
| `Uncaught SyntaxError` | Bad JSON or JS typo | Check your code |
| Overlay appears white | Missing `background: transparent` | Add CSS fix (see below) |

**White background fix:**
When previewing in a browser the background will be white (that's normal — the transparency only works in OBS). To check layout without the white: temporarily add a background colour during testing, then remove it before using in OBS.

```css
/* Temporary — remove before using in OBS */
body { background: #1a1a2e; }
```

---

## Method 2 — Preview Inside OBS

This is the most accurate preview because OBS renders the overlay exactly as viewers will see it.

1. Add your HTML file as a Browser Source in OBS (see [Creating Overlays](creating-overlays.md)).
2. In OBS, right-click the browser source in the **Sources** panel.
3. Select **Properties**.
4. At the bottom, click **Interact** — this opens a live interactive view of the overlay.
5. You can also click **Refresh cache of current page** to reload the overlay after changes.

### Simulate events for testing

To trigger events without actually going live:

1. In the Lumia Stream dashboard, go to **Activity Feed** or **Test** (if available on your plan).
2. Some versions have a **Test Alert** button per event type.
3. Alternatively, simply type in your own Twitch chat while Lumia Stream is connected — chat events fire immediately.

---

## Method 3 — Use a Simple Local HTTP Server (Advanced)

Some browsers restrict `file://` access. If your overlay uses multiple files (images, fonts, JS modules), serve them locally.

### macOS / Windows (Python required)
```bash
# Navigate to your overlay folder
cd /path/to/your/overlays

# Start a local server on port 8080
python3 -m http.server 8080
```

Then open `http://localhost:8080/my-overlay.html` in Chrome.

---

## Checklist Before Going Live

- [ ] Overlay connects to WebSocket without errors (check browser console)
- [ ] Default/empty state looks correct (no placeholder text showing)
- [ ] Transparent background works in OBS
- [ ] Tested with a real chat message
- [ ] Overlay is positioned correctly on the OBS canvas
- [ ] Font size is readable at your stream resolution

---

## Next Steps

- [Tips & Tricks](../tips-and-tricks/general-tips.md)
- [Leaderboards](../custom-features/leaderboards/README.md)
