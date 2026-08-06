# Changelog

## v1.0.0

A notes app that installs to your home screen and gets notes out as one
self-contained `.html` file any browser can open.

### What you get

- **One file, no build step.** `index.html` is the entire app — no bundler, no
  dependencies, no npm install.
- **Installable and offline.** A service worker caches the shell, so it opens
  with no network. Add it to your home screen and it runs like an app.
- **Exports that outlive the app.** Every `.html` export carries the original
  note text inside it as JSON. A browser ignores that and renders the formatted
  page; Ledger reads it back and restores the note exactly, timestamps and all.
  One file covers reading, sharing, and backup.
- **Editor.** Bold, italic, strikethrough, inline code, quotes, bulleted and
  numbered lists, checklists, and a preview mode.
- **Notes list.** Search, duplicate, delete, and it reopens whatever you had
  open last.
- **Light and dark themes.**
- **Optional Google Drive.** Auto-save and export to a Drive folder, and import
  back from it. Entirely opt-in — the app has no server of its own.
- **Import** from `.html`, `.md`, `.txt`, and `.json`, including as a registered
  file handler once installed.

### Running it

Any static web root works. Two conditions matter: serve over HTTPS or
`localhost` (service workers refuse otherwise, so you lose offline mode and the
install prompt), and keep all files on the same origin.

Docker:

```
docker build -t ctwilk95/ledger:latest .
docker run -d -p 8088:80 --name ledger ctwilk95/ledger:latest
```

Then open http://localhost:8088. See the README for Caddy and nginx configs.

### Notes

- Storage is local to each browser. There is no sync — see the README for the
  Syncthing/Nextcloud workarounds and where a backend would slot in.
- Bump `CACHE` in `sw.js` whenever you change files, or the old version keeps
  getting served.
