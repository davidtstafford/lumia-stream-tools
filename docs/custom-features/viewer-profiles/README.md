# Viewer Profiles — Database Reference

Lumia Stream's **Engagement → Viewer Profiles** feature maintains a persistent SQLite database that survives across streams. Every viewer who triggers any event is recorded and their totals are continuously updated.

**Database path (macOS):**
```
~/Library/Application Support/Lumia Stream/lumia_local_storage.sqlite
```

---

## Tables

### `viewer_profile_groups` — one row per viewer (primary reference)

This is the main table. Each row is a deduplicated viewer merged across all their linked platform accounts.

| Column | Type | Description |
|--------|------|-------------|
| `user_id` | TEXT | Your Lumia account ID |
| `group_key` | TEXT | Unique viewer identifier (e.g. `eggieberttestacc`) |
| `viewer_key` | TEXT | Platform-scoped ID (e.g. `twitch:1362524977`) |
| `username` | TEXT | Login username |
| `display_name` | TEXT | Display name |
| `avatar` | TEXT | Profile picture URL |
| `platform` | TEXT | Primary platform (`twitch`, `youtube`, `kick`, `tiktok`, etc.) |
| `linked_profiles_json` | JSON | Array of all linked platform profiles |
| `first_seen` | TEXT | ISO timestamp of first ever event |
| `last_seen` | TEXT | ISO timestamp of most recent event |
| `command_summary_json` | JSON | Summary of chat command usage — see below |
| `alert_summary_json` | JSON | Summary of all alerts fired — see below |
| `first_chatter_summary_json` | JSON | Summary of first-chatter events |
| `achievement_ids_json` | JSON | Array of earned achievement IDs |
| `merged_viewer_json` | JSON | **Full denormalised viewer record** — see below |
| `search_text` | TEXT | Pre-built search string (username + platform) |
| `total_alerts` | INTEGER | Total alert events ever |
| `total_commands` | INTEGER | Total chat commands triggered |
| `total_chats` | INTEGER | Total chat messages sent |
| `total_bits` | INTEGER | Total Twitch bits given |
| `total_kicks` | INTEGER | Total Kick channel points spent |
| `total_points` | INTEGER | Total channel points redeemed |
| `total_gifts` | INTEGER | Total gift subscriptions given |
| `total_donations` | REAL | Total donation money (across all platforms) |
| `achievement_count` | INTEGER | Number of achievements earned |
| `unique_alerts` | INTEGER | Unique alert types triggered |
| `unique_commands` | INTEGER | Unique commands triggered |
| `total_extensions` | INTEGER | Total Twitch extension interactions |
| `source_updated_at` | TEXT | ISO timestamp of last DB write |

---

### `viewer_profile_entries` — one row per platform identity

A viewer with accounts on multiple platforms gets one row here per platform. `viewer_profile_groups` merges these into a single record.

| Column | Type | Description |
|--------|------|-------------|
| `user_id` | TEXT | Your Lumia account ID |
| `viewer_key` | TEXT | Platform-scoped ID (e.g. `twitch:1362524977`) |
| `group_key` | TEXT | The parent group this entry belongs to |
| `username` | TEXT | Login username on this platform |
| `display_name` | TEXT | Display name on this platform |
| `first_seen` | TEXT | ISO timestamp |
| `last_seen` | TEXT | ISO timestamp |
| `data` | JSON | Full viewer data for this platform identity (same shape as `merged_viewer_json`) |

---

### `viewer_profile_state` — one row (global counters)

Aggregated counters for the current week and month, used by the Lumia dashboard.

| Column | Type | Description |
|--------|------|-------------|
| `user_id` | TEXT | Your Lumia account ID |
| `updated_at` | TEXT | ISO timestamp of last update |
| `pending_first_chatters_json` | JSON | Pending first-chatter tracking |
| `week_key` | TEXT | Current ISO week (e.g. `2026-W35`) |
| `month_key` | TEXT | Current month (e.g. `2026-08`) |
| `weekly_counters_json` | JSON | Per-event counts this week (e.g. `{"twitch_week_follower_count": 5}`) |
| `monthly_counters_json` | JSON | Per-event counts this month |
| `weekly_tops_json` | JSON | Top viewers this week by category |
| `monthly_tops_json` | JSON | Top viewers this month by category |

---

### `viewer_profile_achievements` — one row per earned achievement

| Column | Type | Description |
|--------|------|-------------|
| `user_id` | TEXT | Your Lumia account ID |
| `achievement_id` | TEXT | Achievement identifier |
| `group_key` | TEXT | Viewer who earned it |
| `label` | TEXT | Human-readable achievement name |
| `sort_label` | TEXT | Sortable version of the label |

---

## JSON Field Structures

### `alert_summary_json`

Summary of the top alert and total counts:

```json
{
  "total": 3,
  "unique": 2,
  "top": {
    "key": "twitch-follower",
    "count": 1,
    "lastUsed": "2026-08-26T22:33:33.751Z",
    "name": "twitch-follower"
  }
}
```

### `command_summary_json` / `first_chatter_summary_json`

```json
{
  "total": 5,
  "unique": 3
}
```

### `merged_viewer_json` — full viewer record

This is the richest field. Structure:

```json
{
  "viewerKey": "twitch:1362524977",
  "username": "eggiebert",
  "displayName": "eggiebert",
  "avatar": "https://...",
  "platform": "twitch",
  "linkedProfiles": [ ... ],
  "firstSeen": "2026-08-26T22:33:33.751Z",
  "lastSeen":  "2026-08-26T22:33:33.751Z",

  "commands":               {},   // chat-command triggers: { "commandName": { count, lastUsed } }
  "chatbotCommands":        {},   // chatbot-command triggers
  "twitchPointsCommands":   {},   // twitch channel point redeems
  "twitchExtensionsCommands": {},
  "kickPointsCommands":     {},
  "chatMessages":           {},   // chat messages per platform
  "firstChatters":          {},   // first-chatter events per stream

  "alerts": {
    "twitch-follower":          { "count": 1, "lastUsed": "...", "name": "twitch-follower" },
    "twitch-subscriber":        { "count": 2, "lastUsed": "...", "name": "twitch-subscriber" },
    "twitch-giftSubscription":  { "count": 5, "lastUsed": "...", "name": "twitch-giftSubscription" },
    "twitch-bits":              { "count": 3, "lastUsed": "...", "name": "twitch-bits" },
    "twitch-raid":              { "count": 1, "lastUsed": "...", "name": "twitch-raid" }
    // ... any alert type that has fired for this viewer
  },

  "totalBits":              500,   // Twitch bits total
  "totalKicks":             0,     // Kick channel currency
  "totalJewels":            0,     // TikTok jewels
  "totalDiamonds":          0,     // TikTok diamonds
  "totalStars":             0,     // Facebook stars
  "totalPoints":            0,     // Channel points redeemed
  "totalGiftSubscriptions": 5,     // Gift subs given (all platforms)

  "moneyTotals": {
    "streamlabs":           12.50,
    "streamelements":       5.00,
    "kofi":                 3.00
    // keyed by platform/service name, value is summed donation amount
  },

  "charityTotals": {
    "twitch": 10.00
  },

  "achievements": {}
}
```

---

## Useful Queries

### Top chatters (all time)
```sql
SELECT username, display_name, platform, total_chats, avatar, last_seen
FROM viewer_profile_groups
WHERE total_chats > 0
ORDER BY total_chats DESC
LIMIT 10;
```

### Top gifters (all time)
```sql
SELECT username, display_name, platform, total_gifts, avatar, last_seen
FROM viewer_profile_groups
WHERE total_gifts > 0
ORDER BY total_gifts DESC
LIMIT 10;
```

### Top bits donors
```sql
SELECT username, display_name, total_bits, avatar, last_seen
FROM viewer_profile_groups
WHERE total_bits > 0
ORDER BY total_bits DESC
LIMIT 10;
```

### Top donators (by money — requires JSON parsing)
```sql
SELECT username, display_name, total_donations, avatar, last_seen
FROM viewer_profile_groups
WHERE total_donations > 0
ORDER BY total_donations DESC
LIMIT 10;
```

### Top channel point redeemers
```sql
SELECT username, display_name, total_points, avatar, last_seen
FROM viewer_profile_groups
WHERE total_points > 0
ORDER BY total_points DESC
LIMIT 10;
```

### Followers (viewers who have ever followed)
```sql
SELECT username, display_name, platform, avatar, last_seen
FROM viewer_profile_groups
WHERE alert_summary_json LIKE '%twitch-follower%'
ORDER BY last_seen DESC
LIMIT 10;
```

### Subscribers
```sql
SELECT username, display_name, platform, avatar, last_seen
FROM viewer_profile_groups
WHERE alert_summary_json LIKE '%twitch-subscriber%'
ORDER BY last_seen DESC
LIMIT 10;
```

### Viewers who have raided
```sql
SELECT username, display_name, platform, avatar, last_seen
FROM viewer_profile_groups
WHERE alert_summary_json LIKE '%twitch-raid%'
ORDER BY last_seen DESC
LIMIT 10;
```

### Weekly follower count (from global state)
```sql
SELECT json_extract(weekly_counters_json, '$.twitch_week_follower_count') AS followers_this_week
FROM viewer_profile_state;
```

### Monthly subscriber count
```sql
SELECT json_extract(monthly_counters_json, '$.twitch_month_subscriber_count') AS subs_this_month
FROM viewer_profile_state;
```

---

## Alert Event → Database Mapping

| Alert event | Where it appears in DB |
|-------------|------------------------|
| `twitch-follower` | `alert_summary_json`, `merged_viewer_json.alerts`, `weekly_counters_json.twitch_week_follower_count` |
| `twitch-subscriber` | `alert_summary_json`, `merged_viewer_json.alerts` |
| `twitch-giftSubscription` | `alert_summary_json`, `merged_viewer_json.alerts`, **`total_gifts`** |
| `twitch-bits` | `alert_summary_json`, `merged_viewer_json.alerts`, **`total_bits`** |
| `twitch-raid` | `alert_summary_json`, `merged_viewer_json.alerts` |
| `twitch-points` | **`total_points`**, `merged_viewer_json.twitchPointsCommands` |
| `streamlabs-donation` | **`total_donations`**, `merged_viewer_json.moneyTotals.streamlabs` |
| `streamelements-donation` | **`total_donations`**, `merged_viewer_json.moneyTotals.streamelements` |
| `kofi-donation` | **`total_donations`**, `merged_viewer_json.moneyTotals.kofi` |
| `tiktok-gift` | **`total_jewels`** / **`total_diamonds`** in `merged_viewer_json` |
| `facebook-star` | **`total_stars`** in `merged_viewer_json` |
| `youtube-superchat` | **`total_donations`**, `merged_viewer_json.moneyTotals.youtube` |
| `twitch-charityDonation` | `merged_viewer_json.charityTotals.twitch` |
| Any alert | **`total_alerts`**, `alert_summary_json.total`, `merged_viewer_json.alerts[eventName]` |
| Any chat-command | **`total_commands`**, `merged_viewer_json.commands[commandName]` |
| Any chat message | **`total_chats`**, `merged_viewer_json.chatMessages` |

---

## Notes for Overlay Builders

- **Accessing the DB from an HTML overlay** requires a local server — a plain HTML file cannot read SQLite directly. See the `server.py` approach in this folder.
- **The `-wal` and `-shm` files** alongside the `.sqlite` file are part of SQLite's WAL journal. Always open the database **read-only** (`sqlite3` flag `-readonly`, Python: `?mode=ro`) to avoid corrupting Lumia's data.
- **Polling interval**: querying every 30–60 seconds is safe. The DB is written by Lumia on each event so fresh data is always available.
- **`merged_viewer_json`** is the most complete record but requires JSON parsing. For overlays that just need totals, the plain integer columns (`total_gifts`, `total_bits`, etc.) are faster to query and don't need parsing.
