# OSCE Timer

Synchronised multi-station exam timer with controller/observer roles and real-time Firebase sync.

## Setup (~5 minutes)

1. Go to [console.firebase.google.com](https://console.firebase.google.com) → create a free project
2. Build → Realtime Database → Create database → **Start in test mode**
3. Project Settings → General → Your apps → add a Web app → copy the `firebaseConfig`
4. Fill in `config.js` with your values (see template)
5. Open `osce-timer.html` in any browser — no server needed

`config.js` is in `.gitignore` and will never be committed to GitHub.

## Recommended Firebase Rules

Paste these in Firebase Console → Realtime Database → Rules tab.  
They lock down access and auto-delete sessions after 4 hours:

```json
{
  "rules": {
    "sessions": {
      "$sessionId": {
        ".read": true,
        ".write": true,
        ".indexOn": ["createdAt"]
      }
    }
  }
}
```

For auto-cleanup of old sessions, add a scheduled Cloud Function or simply leave test mode rules (sessions are small and Firebase free tier is generous).

## Resilience features

| Scenario | What happens |
|---|---|
| Controller closes tab | Session keeps running. Timers continue. Observers see offline banner after 10s. |
| Controller refreshes | Browser remembers their identity — they auto-reclaim control on rejoin. |
| Controller's browser crashes | Same as above — rejoin via the shared link to reclaim. |
| Controller unreachable | Any observer can tap "Take over as controller". |
| Session auto-advance | Breaks and stations auto-advance server-side (no controller needed once started). |

## Files

| File | Purpose |
|---|---|
| `osce-timer.html` | The app — host this via GitHub Pages or Netlify |
| `config.js` | Your Firebase credentials (not committed) |
| `.gitignore` | Keeps `config.js` off GitHub |
