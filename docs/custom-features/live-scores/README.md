# Live Scores & Lineups — Setup Guides

Ready-to-use overlay files for OBS and Streamlabs. Add them as browser sources to display a live match scoreboard or team lineups on screen — powered by [Sofascore](https://www.sofascore.com)'s public data, no API key required.

Works for any sport Sofascore covers — football, rugby league, rugby union, and more.

---

## Available Overlays

| File | Description |
|------|-------------|
| [match-score.html](match-score.html) | Live scoreboard — team logos, score, match clock, kick-off time |
| [match-lineups.html](match-lineups.html) | Starting lineup + bench for both teams |

Both overlays:
- Pull data directly from Sofascore's public API — no sign-up, no API key
- Refresh automatically on a timer (no page reload needed)
- Work fully on the free tier

---

## Requirements

- OBS Studio or Streamlabs
- A Sofascore match URL for the game you want to display

---

## Match Score — `match-score.html`

### What It Does

- Shows team logos, names, and live score
- **LIVE** badge with pulsing dot while the match is in progress
- Period label (e.g. "2nd half", "Half Time")
- Best-effort live match minute (e.g. `~62'`)
- Kick-off date/time shown before the match starts
- Score flashes briefly whenever it changes

### Setup in OBS

1. Go to [sofascore.com](https://www.sofascore.com), open the match you want to show.
2. Copy the URL from your browser's address bar — e.g.:
   ```
   https://www.sofascore.com/football/match/newcastle-united-tottenham-hotspur/IO#id:16363256
   ```
3. Open `match-score.html` in any text editor (Notepad, TextEdit, VS Code) and paste it into the `MATCH_URL` constant near the top of the `<script>` block.
4. In OBS, click **+** in the **Sources** panel → select **Browser**.
5. Name it something like `Match Score`.
6. Tick **Local file** → click **Browse** → select `match-score.html`.
7. Set **Width** to `520` and **Height** to `130`.
8. Click **OK**.

File path in this repo:
```
lumia-stream-tools/docs/custom-features/live-scores/match-score.html
```

### Easier alternative — search by team name

Don't want to hunt for the exact match URL? Leave `MATCH_URL` blank and set `TEAM_FILTER` to part of a team's name instead:

```js
const MATCH_URL   = '';
const TEAM_FILTER = 'Roosters';
const SPORT_SLUG  = 'rugby-league';  // e.g. 'football', 'rugby-union'
```

The overlay automatically finds that team's live match in the given sport (falling back to today's scheduled kickoff if nothing is live yet). `MATCH_URL` always takes priority if both are set — it's the more reliable option when several matches are live at once.

### Customisation

The CSS variables at the top of the `<style>` block control the appearance:

```css
:root {
  --accent-color: #e8c120;   /* Score highlight / live dot */
  --bg-color: rgba(8, 8, 18, 0.88);
  --text-primary: #ffffff;
  --font-family: 'Poppins', sans-serif;
}
```

Other config values near the top of the `<script>` block:

| Setting | Purpose |
|---------|---------|
| `REFRESH_INTERVAL_MS` | How often to poll for updates (default 30 seconds) |
| `HALF_LENGTH_MIN` | Half length in minutes, used for the live-minute estimate (40 for rugby, 45 for football) |

Save the file, then in OBS right-click the browser source → **Properties** → **Refresh cache of current page** to apply changes.

### Troubleshooting

| Problem | Fix |
|---------|-----|
| "Could not read a match ID" | Make sure you copied the full Sofascore URL, including the `#id:` part |
| "No live or scheduled-today match found" | Check the spelling in `TEAM_FILTER`, or use `MATCH_URL` instead |
| Score stuck on "Fetching match data…" | Open the browser console (F12 in a regular browser, or OBS's interact/inspect window) and check for errors |
| Wrong team/score showing | Double-check the pasted `MATCH_URL` points to the correct match |

---

## Match Lineups — `match-lineups.html`

### What It Does

- Two-column layout: home team vs away team
- Starting lineup sorted by jersey number
- Bench players listed separately
- Captain marked with a **C** badge
- **Confirmed** / **Projected** badge — Sofascore sometimes publishes a probable lineup before it's official

### Setup in OBS

1. Go to [sofascore.com](https://www.sofascore.com), open the match, click the **Lineups** tab.
2. Copy the URL from your browser's address bar.
3. Open `match-lineups.html` in a text editor and paste it into `MATCH_URL`.
4. In OBS, click **+** in the **Sources** panel → select **Browser**.
5. Name it something like `Match Lineups`.
6. Tick **Local file** → click **Browse** → select `match-lineups.html`.
7. Set **Width** to `620` and **Height** to roughly `480`–`560` depending on squad size.
8. Click **OK**.

File path in this repo:
```
lumia-stream-tools/docs/custom-features/live-scores/match-lineups.html
```

> **Note:** Lineups are usually only published by Sofascore 30–60 minutes before kickoff — it's normal to see "Lineups not available yet" before then.

### Customisation

Same CSS variable pattern as the score overlay — edit the `:root` block at the top of the `<style>` section. `MATCH_URL`, `TEAM_FILTER`, and `SPORT_SLUG` work the same way as described above.

### Troubleshooting

| Problem | Fix |
|---------|-----|
| "Lineups not available yet for this match" | Normal before ~30–60 minutes pre-kickoff — the overlay will pick it up automatically once published |
| "Could not read a match ID" | Make sure you copied the full Sofascore URL, including the `#id:` part |

---

## How It Works (Technical)

Both overlays fetch match data directly from Sofascore's public API (`api.sofascore.com`), which generally allows direct cross-origin requests from a browser — no proxy or API key needed. A pair of CORS proxies are included as a fallback in case that ever changes. All state lives in the browser tab's memory; refresh the browser source in OBS to reload with the latest config.

---

## Next Steps

- [General Tips & Tricks](../../tips-and-tricks/general-tips.md)
- [Creating your own overlay from scratch](../../overlays/creating-overlays.md)
