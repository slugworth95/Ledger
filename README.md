# Ledger

A notes app that installs to your home screen and gets notes out as one
self-contained `.html` file any browser can open.

## What's in here

```
index.html                the whole app — no build step, no dependencies
manifest.webmanifest      makes it installable
sw.js                     offline cache
icon-192.png              home screen icons
icon-512.png
icon-maskable.png
apple-touch-icon.png
```

## Putting it on your homelab

Drop the folder into any static web root. Two conditions matter:

1. **HTTPS, or `localhost`.** Service workers won't register otherwise, so
   without it you lose offline mode and the install prompt. Behind Caddy or
   Traefik with a cert, you're set. A Tailscale MagicDNS name with
   `tailscale cert` works too.
2. **Same origin for all files.** Don't split them across hosts.

### Caddy

```
notes.your.domain {
    root * /srv/ledger
    file_server
}
```

### nginx

```nginx
location /notes/ {
    alias /srv/ledger/;
    try_files $uri $uri/ /notes/index.html;
}
```

### Docker, if you'd rather

```yaml
services:
  ledger:
    image: nginx:alpine
    volumes:
      - ./ledger:/usr/share/nginx/html:ro
    ports:
      - "8088:80"
```

Then put it behind whatever already terminates TLS for you.

## Installing it

Open the site in Chrome on Android and use **Add to home screen**. On iOS,
Safari's share sheet has the same option. It then launches without browser
chrome and opens with no network.

## Where notes live

In this browser's `localStorage`, under the key `ledger:notes:v1`. That means:

- Notes are per-device and per-browser. Nothing syncs on its own.
- Clearing site data for this origin deletes them.
- **Export → Backup file** writes a `.json` of everything. Worth doing
  on a schedule if these notes matter.

If you serve the same URL to a phone and a laptop, each keeps its own set.
Moving notes between them is what the export files are for — the exported
`.html` is both the readable copy and the restore file.

## The exported HTML

Every `.html` export carries the original note text inside it as JSON:

```html
<script type="application/json" id="ledger-data">{"kind":"ledger",...}</script>
```

A browser ignores it and shows the formatted page. Ledger reads it and restores
the note exactly, timestamps and all. So one file covers reading, sharing, and
backup — nothing to convert.

## Syncing, if you want it

The app has no sync and no server. The workable options:

- Export the `.json` backup into a Syncthing or Nextcloud folder, and import it
  on the other device.
- Serve the app from a Tailscale-only host so all your devices reach one URL —
  though each browser still keeps its own notes.

Adding real sync would mean a backend. If you want that, a small
CouchDB/PouchDB or a single-table SQLite API would slot in behind the storage
adapter at the top of the script without touching the rest.

## Changing things

Colors are CSS custom properties in `:root` near the top of `index.html`. The
storage layer is the `Store` object — swap `load` and `save` for API calls and
everything else keeps working. Bump `CACHE` in `sw.js` when you change files, or
the old version keeps serving.
