# Digital Asset Links

Android only removes the browser's address bar from a Trusted Web Activity when it can prove that
the **site** and the **app** share an owner. The proof is a file served over HTTPS:

```
https://massai-itu.ca/.well-known/assetlinks.json
```

## How it is served

The Massai server renders that path from one environment variable, so publishing a fingerprint is an
env change, not a deploy:

```
ANDROID_ASSETLINKS = <the JSON below, minified>
```

Unset, the path returns 404 — which is correct: no wrapper has been published yet.

## What goes in it

1. Take the SHA-256 fingerprint printed by `keytool -list -v`.
2. Replace `REPLACE_WITH_SHA256_FINGERPRINT` in [`assetlinks.json.template`](assetlinks.json.template).
3. Minify to one line and set it as `ANDROID_ASSETLINKS` on the Render service.

## Verify

```bash
curl -s https://massai-itu.ca/.well-known/assetlinks.json | jq .
```

Then install the APK and open it: if the fingerprint matches, there is no address bar. If it does
not, the app still works but shows Chrome's URL bar — the usual symptom of a missing or stale file.
