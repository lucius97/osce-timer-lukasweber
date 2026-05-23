# OSCE Timer

A real-time synchronised multi-station exam timer for OSCEs. One device acts as controller; any number of observers follow along on any browser, any device.

---

## Quick start

1. **Firebase setup** (free, ~5 min) — see [Firebase setup](#firebase-setup) below
2. Fill in `config.js` with your Firebase credentials
3. Host `osce-timer.html` via [GitHub Pages](https://pages.github.com) or [Netlify Drop](https://app.netlify.com/drop)
4. Open the URL on the controller device, tap **New session**, configure and go

---

## Files

| File | Commit to GitHub? | Purpose |
|---|---|---|
| `osce-timer.html` | ✅ Yes | The whole app |
| `config.js` | ❌ No — gitignored | Your Firebase credentials |
| `.gitignore` | ✅ Yes | Keeps `config.js` off GitHub |
| `session-start.mp3` | Optional | Plays at block start (after Prep) |
| `session-end.mp3` | Optional | Plays at session end |
| `prep-start.mp3` | Optional | Plays at start of Prep |
| `warning.mp3` | Optional | Plays at 1-minute station warning |

Sound files are optional — built-in tones play as fallback if files are missing. Place them in the same folder as `osce-timer.html`.

---

## Phase sequence

Each station cycle runs: **Change → Prep → Station** → repeat

| Phase | Purpose | Default duration |
|---|---|---|
| Change | Candidates move between stations | 60 s (configurable) |
| Prep | Read the brief, get ready | 30 s (configurable) |
| Station | The exam itself | 8 min (configurable) |

---

## Controller features

- Configure stations, durations, and warning threshold before starting
- **Begin session** button — session waits until explicitly started
- **Pause / Resume** — syncs across all devices instantly
- **← Roll back** — step back one phase if something goes wrong
- **Skip phase →** — advance immediately without waiting
- **Retime** slider — reset current phase to any duration from now
- Observer count badge — see how many devices are connected
- Sound settings panel — configure or test sound files, enable/disable

## Observer features

- Join by tapping a session from the live list, or entering the 6-digit code
- Direct link (`yoursite.html#XXXXXX`) auto-joins on open
- **Enable sound** button — required on iOS/iPad to unlock audio
- **← Leave session** to return home without ending the session

---

## Resilience

| Scenario | Behaviour |
|---|---|
| Controller closes tab | Session keeps running; observers see offline banner after ~10s |
| Controller refreshes | Browser remembers their role — auto-reclaims on rejoin |
| Controller disappears | Any observer can tap **Take over as controller** |
| Everyone disconnects | Session freezes; first device to rejoin fast-forwards to current time |
| No observers online | Controller device still auto-advances phases |

---

## Firebase setup

1. Go to [console.firebase.google.com](https://console.firebase.google.com) → **Add project**
2. **Build → Realtime Database → Create database** → choose a region → **Start in test mode**
3. **Project Settings** (gear icon) → **General** → scroll to **Your apps** → click **</>** (Web) → register app → copy the `firebaseConfig` object
4. Paste those values into `config.js`

### Recommended database rules

In Firebase Console → Realtime Database → Rules tab:

```json
{
  "rules": {
    "sessions": {
      "$sessionId": {
        ".read": true,
        ".write": true
      }
    },
    "sessionIndex": {
      ".read": true,
      "$sessionId": {
        ".write": true
      }
    }
  }
}
```

Test mode rules expire after 30 days — replace them with the rules above before then.

---

## Sound notes

Sounds are delivered via Firebase (`soundCue`) so all devices receive them reliably, even if they joined mid-session. The controller writes the cue; every connected device plays it.

On **iOS/iPad**, tap the **Enable sound** button that appears when you join as an observer. This is required by Safari's audio policy — without it, sounds won't play.

On **desktop**, audio unlocks automatically on first interaction with the page.

---

## Known limitations

- Requires an internet connection — all sync goes through Firebase
- Firebase free tier (Spark plan) allows 100 simultaneous connections and 1 GB storage — more than enough for any OSCE
- Sound files must be served from the same origin as the HTML (same GitHub Pages repo or same Netlify site)
