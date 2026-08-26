# Lumia Stream Free Tier — What You Get

Understanding the free tier helps you plan your stream setup without hitting unexpected limits.

---

## ✅ What's Included for Free

| Feature | Free Tier |
|---------|-----------|
| Lighting control (Philips Hue, Nanoleaf, etc.) | ✅ Up to 1 light group |
| Twitch chat integration | ✅ Full |
| YouTube chat integration | ✅ Full |
| OBS/Streamlabs browser source support | ✅ Full |
| Custom HTML overlays | ✅ Full |
| WebSocket API (local) | ✅ Full |
| Chat commands | ✅ Limited set |
| Alert triggers | ✅ Basic alerts |
| Sound board | ✅ Limited clips |
| Number of connected platforms | ✅ 1 at a time |

---

## 🔒 What Requires a Paid Plan

| Feature | Notes |
|---------|-------|
| Multiple light groups | Pro tier |
| Multiple simultaneous platforms | Pro tier |
| Advanced alert sequencing | Pro tier |
| Priority support | Pro tier |

> **Tip:** The free tier is genuinely capable for most solo streamers. The WebSocket API and full browser source support mean you can build powerful custom overlays — like the [Chat Leaderboard](../custom-features/chat-leaderboard/README.md) — entirely for free.

---

## 🔌 The Local WebSocket API

Even on the free tier, Lumia Stream runs a **local WebSocket server** at:

```
ws://localhost:39231
```

This is the backbone of all custom features in this repo. Any HTML file open as a browser source in OBS can connect to it and receive live stream events.

### Events you can listen to (free tier):
- Chat messages
- Subscriptions
- Raids
- Bit donations / Super Chats
- Follows
- Channel point redemptions

---

## 📖 Next Steps

- [Setup on macOS](setup-mac.md)
- [Setup on Windows](setup-windows.md)
