# Tips & Tricks for Lumia Stream (Free Tier)

A collection of power-user tips to get more out of the free version.

---

## 🔌 Connection Tips

### Keep Lumia Stream in the background
Lumia Stream must be running for any overlay or integration to work. It doesn't need to be the focused window — minimise it to the taskbar/dock and it keeps running. See the setup guides for how to auto-start it:
- [macOS auto-start](../getting-started/setup-mac.md#keeping-lumia-stream-running-in-the-background)
- [Windows auto-start](../getting-started/setup-windows.md#running-lumia-stream-on-startup)

### Re-authorise if chat stops responding
Platform tokens expire. If chat suddenly stops showing up:
1. Go to **Settings → Connections**.
2. Disconnect the platform.
3. Reconnect and re-authorise.

---

## 🖼️ Overlay Tips

### One browser source at 1920×1080 beats many small ones
Rather than adding 5 separate browser sources, combine related elements (e.g. leaderboard + follow counter) into one HTML file. Fewer sources = better OBS performance.

### Use `display: none` instead of removing elements
When hiding overlay elements (e.g. after an alert), hide with CSS rather than removing the element from the DOM. This avoids layout reflows and keeps animations smooth.

```javascript
// Good
element.style.display = 'none';

// Avoid during live overlays
element.remove();
```

### Reload an overlay without restarting OBS
Right-click the browser source → **Properties** → **Refresh cache of current page**. This reloads the HTML without touching the rest of your scene.

### Use CSS custom properties for easy theming
Put your colours and fonts as CSS variables at the top of your overlay. Easy to change without hunting through the file.

```css
:root {
  --accent-color: #9147ff;   /* Twitch purple */
  --bg-color: rgba(0,0,0,0.8);
  --font-size-large: 28px;
}
```

---

## 🎯 WebSocket Tips

### Always handle reconnects
Lumia Stream can restart or briefly disconnect. Add reconnect logic to your overlays:

```javascript
function connect() {
  const ws = new WebSocket('ws://localhost:39231');

  ws.addEventListener('close', () => {
    // Try to reconnect after 3 seconds
    setTimeout(connect, 3000);
  });

  ws.addEventListener('message', (event) => {
    // handle events...
  });
}

connect();
```

### Log all events to find what data you need
When building a new feature, log every message to the browser console to see exactly what Lumia Stream sends:

```javascript
ws.addEventListener('message', (event) => {
  console.log(JSON.parse(event.data));
});
```
Then open your overlay in Chrome with DevTools open and trigger events in your channel chat.

### Filter events early
Check the event type before doing any work. Lumia Stream sends many different events — only process what you need:

```javascript
ws.addEventListener('message', (event) => {
  const data = JSON.parse(event.data);
  if (data?.data?.type !== 'chat') return; // ignore non-chat events
  // ... handle chat
});
```

---

## 🎨 Design Tips

### Dark semi-transparent backgrounds read well over any game
```css
background: rgba(0, 0, 0, 0.75);
backdrop-filter: blur(4px);
border-radius: 8px;
```

### Google Fonts work in OBS browser sources
OBS browser sources have full internet access. Add this to your `<head>`:

```html
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap" rel="stylesheet">
```

### Test at your actual stream resolution
If you stream at 1080p, set your OBS canvas to 1920×1080 and set browser sources to the same size. Fonts and layouts look very different at 720p vs 1080p.

---

## 🧹 Performance Tips

### Avoid `setInterval` for animations — use `requestAnimationFrame`
```javascript
// Avoid for visual updates
setInterval(updateVisual, 16);

// Better — syncs to the screen refresh rate
function tick() {
  updateVisual();
  requestAnimationFrame(tick);
}
requestAnimationFrame(tick);
```

### Limit DOM updates
If you're showing chat messages, cap the list at a maximum (e.g. 20 messages). Remove old entries as new ones arrive so the DOM doesn't grow forever.

---

## Next Steps

- [Creating Overlays](../overlays/creating-overlays.md)
- [Leaderboards](../custom-features/leaderboards/README.md)
