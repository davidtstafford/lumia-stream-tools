# Setting Up Lumia Stream on macOS

---

## Requirements

- macOS 11 (Big Sur) or later
- An active Twitch, YouTube, or other supported streaming account
- OBS Studio or Streamlabs (optional, for overlays)

---

## Step 1 — Download and Install

1. Go to [lumiastream.com](https://lumiastream.com) and click **Download**.
2. Select the **macOS** version (`.dmg` file).
3. Open the downloaded `.dmg` file.
4. Drag **Lumia Stream** into your **Applications** folder.
5. Open **Launchpad** or **Finder → Applications** and launch Lumia Stream.

> **First launch security prompt:** macOS may say the app is from an unidentified developer.  
> Fix: Go to **System Settings → Privacy & Security**, scroll down, and click **Open Anyway**.

---

## Step 2 — Create or Sign In to Your Account

1. The app will open a browser window asking you to log in or create a free account.
2. Sign up with your email or log in with an existing account.
3. Once authenticated, the app will return to the dashboard.

---

## Step 3 — Connect Your Streaming Platform

### Twitch
1. In the Lumia Stream dashboard, click **Settings** (gear icon) in the sidebar.
2. Click **Connections**.
3. Click **Connect** next to **Twitch**.
4. A browser window opens — log in to Twitch and click **Authorize**.
5. Return to Lumia Stream. You should see a green "Connected" badge.

### YouTube
1. Follow the same steps but click **Connect** next to **YouTube**.
2. Sign in with your Google account and grant the requested permissions.

---

## Step 4 — Test That Chat Is Working

1. Open your Twitch or YouTube channel in a browser.
2. In the Lumia Stream dashboard, click the **Chat** tab.
3. Type a message in your channel chat.
4. The message should appear in the Lumia Stream chat feed within a second or two.

---

## Step 5 — Confirm the WebSocket API is Running

The WebSocket API powers all custom overlays in this repo.

1. Open **Terminal** (Spotlight → `terminal`).
2. Run:
   ```bash
   curl -s http://localhost:39231
   ```
3. You should get a response (even an error page). If it times out, Lumia Stream is not running — make sure it's open in the background.

---

## Step 6 — Allow Lumia Stream Through the Firewall (if needed)

If your custom overlays can't connect to the WebSocket:

1. Go to **System Settings → Network → Firewall**.
2. Click **Options...**.
3. Find **Lumia Stream** in the list and set it to **Allow incoming connections**.

---

## Keeping Lumia Stream Running in the Background

Lumia Stream needs to be open for overlays to work. To have it start automatically:

1. Go to **System Settings → General → Login Items**.
2. Click **+** and add **Lumia Stream** from your Applications folder.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| App won't open ("unidentified developer") | System Settings → Privacy & Security → Open Anyway |
| Chat not appearing | Check Connections tab — re-authorize the platform |
| WebSocket not responding | Make sure Lumia Stream is open; check Firewall settings |
| High CPU usage | Disable lighting if you have no lights connected |

---

## Next Steps

- [Free Tier Overview](free-tier-overview.md)
- [Creating Overlays](../overlays/creating-overlays.md)
- [Custom Features — Leaderboards](../custom-features/leaderboards/README.md)
