# Setting Up Lumia Stream on Windows

---

## Requirements

- Windows 10 (version 1903) or later, or Windows 11
- An active Twitch, YouTube, or other supported streaming account
- OBS Studio or Streamlabs (optional, for overlays)

---

## Step 1 — Download and Install

1. Go to [lumiastream.com](https://lumiastream.com) and click **Download**.
2. Select the **Windows** version (`.exe` installer).
3. Run the installer — you may see a **Windows Defender SmartScreen** warning.  
   Click **More info** → **Run anyway** (this is normal for new apps).
4. Follow the on-screen installer steps (default options are fine).
5. Lumia Stream will launch automatically when the installer finishes.

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

1. Open **PowerShell** (Start → search `powershell`).
2. Run:
   ```powershell
   Test-NetConnection -ComputerName localhost -Port 39231
   ```
3. If `TcpTestSucceeded` shows **True**, the API is running.  
   If **False**, make sure Lumia Stream is open.

---

## Step 6 — Allow Lumia Stream Through Windows Firewall (if needed)

If your custom overlays can't connect to the WebSocket:

1. Open **Windows Defender Firewall** (Start → search `firewall`).
2. Click **Allow an app or feature through Windows Defender Firewall**.
3. Click **Change settings** → **Allow another app...**.
4. Browse to the Lumia Stream executable (usually `C:\Users\<you>\AppData\Local\Programs\lumia-stream\Lumia Stream.exe`).
5. Tick both **Private** and **Public** network boxes and click **OK**.

---

## Running Lumia Stream on Startup

Lumia Stream needs to be open for overlays to work. To start it automatically:

1. Press `Win + R`, type `shell:startup`, and press Enter.
2. Create a shortcut to `Lumia Stream.exe` inside that folder.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Installer blocked by SmartScreen | Click "More info" → "Run anyway" |
| Chat not appearing | Check Connections tab — re-authorize the platform |
| WebSocket not responding | Make sure Lumia Stream is open; check Firewall |
| App crashes on launch | Update Windows, then reinstall Lumia Stream |
| OBS browser source blank | See [Previewing Overlays](../overlays/previewing-overlays.md) |

---

## Next Steps

- [Free Tier Overview](free-tier-overview.md)
- [Creating Overlays](../overlays/creating-overlays.md)
- [Chat Leaderboard feature](../custom-features/chat-leaderboard/README.md)
