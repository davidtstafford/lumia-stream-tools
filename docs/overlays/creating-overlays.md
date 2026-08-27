# Creating Overlays in Lumia Stream

Overlays are visual elements that appear on top of your stream — things like alerts, chat boxes, countdowns, and leaderboards. In Lumia Stream, overlays are HTML files loaded as browser sources in OBS or Streamlabs.

---

## How Overlays Work

```
Your Stream Software (OBS / Streamlabs)
    └── Browser Source
            └── Points to an HTML file (local or hosted)
                    └── HTML file connects to Lumia Stream WebSocket
                            └── Receives live stream events → updates visuals
```

No server needed. Everything runs locally on your machine.

---

## Method 1 — Use a Built-In Lumia Widget

Lumia Stream includes a small set of pre-built widgets on the free tier.

1. In the Lumia Stream dashboard, click **Widgets** in the sidebar.
2. Browse the available widgets (e.g., alerts, goal bars).
3. Click a widget and then click **Copy URL**.
4. In OBS, add a **Browser Source** and paste that URL.
5. Set the width and height to match your stream resolution (e.g., 1920×1080).

---

## Method 2 — Create a Custom HTML Overlay

This is the most powerful approach and works fully on the free tier.

### Step 1 — Create your HTML file

Create a new file called `my-overlay.html` anywhere on your computer (e.g., your Desktop or a dedicated `overlays/` folder).

Basic template:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>My Overlay</title>
  <style>
    /* Make the background transparent for OBS */
    body {
      background: transparent;
      margin: 0;
      overflow: hidden;
      font-family: 'Arial', sans-serif;
    }

    .message-box {
      position: absolute;
      bottom: 100px;
      left: 50%;
      transform: translateX(-50%);
      background: rgba(0, 0, 0, 0.7);
      color: white;
      padding: 12px 24px;
      border-radius: 8px;
      font-size: 24px;
      display: none; /* hidden until an event fires */
    }
  </style>
</head>
<body>

  <div class="message-box" id="alert">Welcome!</div>

  <script>
    const alertBox = document.getElementById('alert');

    // Connect to the Lumia Stream local WebSocket API
    const ws = new WebSocket('ws://localhost:39231');

    ws.addEventListener('open', () => {
      console.log('Connected to Lumia Stream');
    });

    ws.addEventListener('message', (event) => {
      const data = JSON.parse(event.data);

      // Show an alert when someone follows
      if (data?.data?.type === 'follow') {
        alertBox.textContent = `Thanks for the follow, ${data.data.username}! 🎉`;
        alertBox.style.display = 'block';

        // Hide after 5 seconds
        setTimeout(() => {
          alertBox.style.display = 'none';
        }, 5000);
      }
    });

    ws.addEventListener('close', () => {
      console.log('Disconnected from Lumia Stream');
    });
  </script>

</body>
</html>
```

### Step 2 — Load it in OBS

1. In OBS, click the **+** button in the **Sources** panel.
2. Select **Browser**.
3. Give it a name (e.g., "Follow Alert").
4. Tick **Local file** and click **Browse** — select your HTML file.
5. Set width to `1920` and height to `1080`.
6. Tick **Shutdown source when not visible** (prevents stale connections).
7. Click **OK**.

### Step 3 — Position the overlay

- The overlay layer covers the full canvas (1920×1080).
- All positioning is done with CSS inside your HTML file (`position: absolute`, `bottom`, `left`, etc.).
- You don't need to drag/resize the browser source in OBS.

---

## Understanding the WebSocket Event Format

When Lumia Stream sends an event, it looks like this (simplified):

```json
{
  "origin": "lumia",
  "type": "event",
  "data": {
    "type": "chat",
    "username": "coolviewer123",
    "message": "Hello stream!",
    "platform": "twitch"
  }
}
```

### Common event types

| `data.type` | Trigger |
|-------------|---------|
| `chat` | Any chat message |
| `follow` | New follower |
| `subscription` | New subscriber |
| `raid` | Incoming raid |
| `bits` | Bit donation (Twitch) |
| `superchat` | Super Chat (YouTube) |
| `channelpoints` | Channel point redemption |

---

## Tips for Overlay Design

- **Transparent backgrounds:** Always set `body { background: transparent; }` so OBS shows your game/cam behind it.
- **Google Fonts:** You can load web fonts in OBS browser sources — the browser source has full internet access.
- **Animations:** Use CSS `@keyframes` for smooth entry/exit effects.
- **Z-index:** Layer multiple overlays by adjusting the source order in OBS (bottom of the list = furthest back).

---

## Next Steps

- [Previewing Overlays](previewing-overlays.md) — test without going live
- [Leaderboards](../custom-features/leaderboards/README.md) — complete ready-to-use examples
