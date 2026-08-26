# Chat Leaderboard — Setup Guide

A live **Top 5 Chatters** leaderboard overlay for OBS and Streamlabs. Shows your most active chat participants in real time, updated every time someone sends a message.

![Leaderboard preview — five ranked rows with username and message count, animated bar fill]

---

## What It Does

- Tracks every chat message received during your stream session
- Displays the top 5 chatters ranked by message count
- Animates bar widths to show relative activity
- Updates live as chat activity changes
- Automatically reconnects if Lumia Stream restarts
- Resets when the page is reloaded (i.e. when you start a new stream session)

---

## Requirements

- Lumia Stream installed and running ([Mac setup](../../getting-started/setup-mac.md) | [Windows setup](../../getting-started/setup-windows.md))
- OBS Studio or Streamlabs
- The `leaderboard.html` file from this folder

---

## Setup — Step by Step

### Step 1 — Download the file

The overlay file is `leaderboard.html` in this folder.

If you cloned this repo, the file is already on your computer at:
```
lumia-stream-tools/docs/custom-features/chat-leaderboard/leaderboard.html
```

### Step 2 — Add it as a Browser Source in OBS

1. In OBS, click the **+** button in the **Sources** panel.
2. Select **Browser**.
3. Name it something like `Chat Leaderboard`.
4. Tick **Local file**.
5. Click **Browse** and select `leaderboard.html`.
6. Set **Width** to `400` and **Height** to `400`.
7. Tick **Shutdown source when not visible** (recommended).
8. Click **OK**.

### Step 3 — Position it on your scene

- Drag the browser source to where you want it on the canvas.
- Suggested position: bottom-left or bottom-right corner.
- The leaderboard is designed at 400×400px but you can resize it in OBS if needed.

### Step 4 — Make sure Lumia Stream is running

The overlay won't show any data unless Lumia Stream is open and connected to your streaming platform. Check the Lumia Stream dashboard — you should see a green "Connected" status for your platform.

### Step 5 — Go live and test

Type a few messages in your own chat from different accounts (or ask a friend to). Within a second or two, names should appear on the leaderboard. The order updates automatically.

---

## Customisation

Open `leaderboard.html` in any text editor (Notepad, TextEdit, VS Code). The customisation variables are at the top of the `<style>` block:

```css
:root {
  --accent-color: #9147ff;   /* Bar fill colour — change to match your brand */
  --bg-color: rgba(10, 10, 20, 0.85);
  --text-color: #ffffff;
  --font-family: 'Poppins', sans-serif;
  --max-entries: 5;          /* Number of entries to show */
}
```

Change `--accent-color` to match your stream colour scheme. Save the file and click **Refresh cache** in OBS to see the update.

---

## Resetting the Leaderboard

The leaderboard resets automatically when the browser source reloads (e.g. when OBS restarts, or when you click **Refresh cache**). This makes it easy to start fresh each stream.

To manually reset mid-stream:
1. Right-click the browser source in OBS.
2. Select **Properties**.
3. Click **Refresh cache of current page**.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Leaderboard shows "Waiting for chat..." | Lumia Stream isn't running, not connected to chat, or the token in `leaderboard.html` is wrong/missing |
| Names appear but scores don't change | Check the browser console (F12) for WebSocket errors |
| Overlay is invisible/blank | Check that `leaderboard.html` path is correct in OBS |
| Names from last stream are still showing | Refresh the browser source cache to reset scores |

---

## How It Works (Technical)

The overlay connects to Lumia Stream's local WebSocket API at `ws://localhost:39231/?token=YOUR_TOKEN`. Every time a `chat` event arrives, it increments a counter for that username in memory. A ranked list is rebuilt and the DOM is updated. All data lives in the browser tab's memory — nothing is written to disk, so it resets cleanly on reload.

---

## Next Steps

- [General Tips & Tricks](../../tips-and-tricks/general-tips.md)
- [Creating your own overlay from scratch](../../overlays/creating-overlays.md)
