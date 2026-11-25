# Massai Mind

*Call it **The Mind** once you are inside it.*

A standalone visualiser for **Massai's mind** — the same sphere that sits behind the app's front
door, with nothing else attached to it.

- **Live:** https://massai-itu.ca/mind
- **Install it as an app:** open that URL in Chrome or Safari → *Add to Home Screen* / *Install*.
  It runs fullscreen, keeps the screen awake (toggle in the settings panel), and needs no account.
- **What it watches:** `GET https://massai-itu.ca/api/mind/feed` — capability counts only. No names,
  no sessions, no text. When nothing is happening it says `SIMULATED` rather than pretending.

This repo exists for one reason: **shipping it as an Android app** without forking the visualiser.
The visualiser itself lives in the Massai monorepo; this is only a wrapper.

## The Android wrapper

A **Trusted Web Activity** (TWA): a real Play-Store-shaped app that renders the live site in a
Chrome-backed activity, with no browser chrome. That means one codebase — the site — and an APK that
tracks it automatically.

```
app id      ca.massai.mind
host        massai-itu.ca
start path  /mind
display     fullscreen
```

### Build it

1. **Generate a signing key** (once, keep it forever — losing it means you cannot update the app):

   ```bash
   keytool -genkeypair -v -keystore android.keystore -alias android \
     -keyalg RSA -keysize 2048 -validity 10000 -storetype PKCS12
   ```

2. **Find its fingerprint** — this is what proves to Android that your site and your app are the
   same owner:

   ```bash
   keytool -list -v -keystore android.keystore -alias android | grep SHA256
   ```

3. **Publish the fingerprint** at `https://massai-itu.ca/.well-known/assetlinks.json` (see
   [`assetlinks/`](assetlinks/)) — the Massai server serves that file from the
   `ANDROID_ASSETLINKS` environment variable.

4. **Add three repository secrets** (Settings → Secrets → Actions):

   | Secret | Value |
   |---|---|
   | `SIGNING_KEYSTORE_BASE64` | `base64 -w0 android.keystore` |
   | `SIGNING_KEYSTORE_PASSWORD` | the store password |
   | `SIGNING_KEY_PASSWORD` | the key password (same, if PKCS12) |

5. **Run the workflow** — Actions → *Build APK* → *Run workflow*. The signed APK lands as a build
   artifact; sideload it with `adb install`, or subscribe to the Play track later.

### Why CI and not a laptop

Building an APK needs a JDK and the Android SDK (~1.5 GB). The workflow installs both on a clean
runner, so nobody needs a working Android toolchain locally, and the build is reproducible.

## Honest limits

- **iOS has no equivalent.** Apple does not permit web-based screensavers; the ceiling on iOS is
  *Add to Home Screen*, which this already supports.
- **A TWA needs the site to stay online.** It is a window onto `massai-itu.ca/mind`, not an offline
  bundle. If you want a truly offline app, that is a different build (bundling the renderer), and it
  would fork the visualiser — deliberately not done here.
- **Play Store** listing costs a one-time $25 and needs a privacy policy URL. Sideloading is free.

## Licence

The wrapper configuration here is yours to use freely. The Massai name and the visualiser remain
part of the Massai project.
