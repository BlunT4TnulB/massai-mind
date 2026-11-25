# Icons

The wrapper pulls its icons from the live site so there is only one source of truth:

- `https://massai-itu.ca/icon-512.png` — launcher
- `https://massai-itu.ca/massai-maskable-512.png` — maskable (Android adaptive)

If either 404s, `bubblewrap init` will complain. Both are served by the Massai server and are
already used by the web manifest.
