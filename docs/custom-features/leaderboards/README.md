# Leaderboards — Setup Guides

Ready-to-use overlay files for OBS and Streamlabs. Add them as browser sources to display live stream stats on screen.

---

## Available Overlays

| File | Description |
|------|-------------|
| [top-chatters-this-stream.html](top-chatters-this-stream.html) | Live top-5 chatters ranked by message count |
| [recent-follows-this-stream.html](recent-follows-this-stream.html) | Scrolling list of the most recent followers |

Both overlays:
- Connect to the Lumia Stream local WebSocket API (`ws://localhost:39231`)
- Update in real time as events arrive
- Reset cleanly when the browser source is refreshed (great for per-stream sessions)
- Work fully on the free tier

---

## Requirements

- Lumia Stream installed and running ([Mac setup](../../getting-started/setup-mac.md) | [Windows setup](../../getting-started/setup-windows.md))
- OBS Studio or Streamlabs

---

## Top Chatters — `top-chatters-this-stream.html`

### What It Does

- Tracks every chat message received during your stream session
- Displays the top 5 chatters ranked by message count
- Animated progress bars show relative chat activity
- Gold / silver / bronze rank badges for the top 3
- Automatically reconnects if Lumia Stream restarts

### Setup in OBS

1. In OBS, click **+** in the **Sources** panel → select **Browser**.
2. Name it something like `Top Chatters`.
3. Tick **Local file** → click **Browse** → select `top-chatters-this-stream.html`.
4. Set **Width** to `400` and **Height** to `400`.
5. Tick **Shutdown source when not visible** (recommended) → click **OK**.
6. Drag the source to your preferred position (suggested: bottom-left or bottom-right corner).

File path in this repo:
```
lumia-stream-tools/docs/custom-features/leaderboards/top-chatters-this-stream.html
```

### Customisation

Open `top-chatters-this-stream.html` in any text editor (Notepad, TextEdit, VS Code). The CSS variables at the top of the `<style>` block control the appearance:

```css
:root {
  --accent-color: #9147ff;   /* Bar fill colour — change to match your brand */
  --bg-color: rgba(10, 10, 20, 0.85);
  --text-primary: #ffffff;
  --font-family: 'Poppins', sans-serif;
}
```

Save the file, then in OBS right-click the browser source → **Properties** → **Refresh cache of current page** to apply changes.

### Resetting Mid-Stream

The leaderboard resets automatically when the page reloads. To reset manually during a live stream:
1. Right-click the browser source in OBS → **Properties**.
2. Click **Refresh cache of current page**.

### Troubleshooting

| Problem | Fix |
|---------|-----|
| Shows "Waiting for chat..." | Lumia Stream isn't running or not connected to your platform |
| Names appear but scores don't update | Open browser console (F12) and check for WebSocket errors |
| Overlay is invisible/blank | Confirm the file path is correct in the OBS source properties |
| Old names from last stream still showing | Refresh the browser source cache to reset scores |

---

## Recent Follows — `recent-follows-this-stream.html`

### What It Does

- Shows a live scrolling list of recent followers
- New followers appear at the top of the list as they arrive
- Keeps the most recent entries visible
- Resets when the page is reloaded (clean start each stream)

### Setup in OBS

1. In OBS, click **+** in the **Sources** panel → select **Browser**.
2. Name it something like `Recent Follows`.
3. Tick **Local file** → click **Browse** → select `recent-follows-this-stream.html`.
4. Set **Width** to `400` and **Height** to `400`.
5. Tick **Shutdown source when not visible** (recommended) → click **OK**.
6. Position on your canvas as desired.

File path in this repo:
```
lumia-stream-tools/docs/custom-features/leaderboards/recent-follows-this-stream.html
```

### Customisation

Open `recent-follows-this-stream.html` in any text editor. The accent colour variable is at the top of the `<style>` block:

```css
:root {
  --accent-color: #ff4d91;   /* Highlight colour — change to match your brand */
}
```

Save the file and click **Refresh cache** in OBS to apply changes.

### Resetting Mid-Stream

Right-click the browser source in OBS → **Properties** → **Refresh cache of current page**.

### Troubleshooting

| Problem | Fix |
|---------|-----|
| Shows "Waiting for followers..." | Lumia Stream isn't running or not connected to your platform |
| No new followers appearing | Open browser console (F12) and check for WebSocket errors |
| Overlay is invisible/blank | Confirm the file path is correct in the OBS source properties |

---

## How It Works (Technical)

Both overlays connect to Lumia Stream's local WebSocket API at `ws://localhost:39231`. Events arrive as JSON — chat events increment per-user counters, follow events push new usernames to the top of the list. All data lives in the browser tab's memory and resets cleanly on reload. See [Creating Overlays](../../overlays/creating-overlays.md) if you want to understand the WebSocket event format and build your own.

---

## Next Steps

- [General Tips & Tricks](../../tips-and-tricks/general-tips.md)
- [Creating your own overlay from scratch](../../overlays/creating-overlays.md)
