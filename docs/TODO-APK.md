# To finish the APK — the four steps, kept here so they are not remembered

Deliberately parked. Everything else is ready: the wrapper config, the CI build, and the server
route that serves the Android fingerprint. These are the only things left, and all of them need the
owner's hands because they involve secrets.

- [ ] **1 · Make a signing key** (once, and never lose it — losing it means the app can only be
      abandoned, never updated):

      keytool -genkeypair -v -keystore android.keystore -alias android \
        -keyalg RSA -keysize 2048 -validity 10000 -storetype PKCS12

- [ ] **2 · Read the fingerprint**:

      keytool -list -v -keystore android.keystore -alias android | grep SHA256

- [ ] **3 · Add three repository secrets** (Settings → Secrets → Actions):

      SIGNING_KEYSTORE_BASE64    base64 -w0 android.keystore
      SIGNING_KEYSTORE_PASSWORD  the store password
      SIGNING_KEY_PASSWORD       the same value for PKCS12

- [ ] **4 · Publish the fingerprint**: set `ANDROID_ASSETLINKS` on the Render service to the
      minified content of `assetlinks/assetlinks.json.template` with the SHA-256 filled in.

## Until then

The workflow still runs without secrets — it produces an **unsigned** APK for sideload testing.
Without step 4 the app works but Chrome's address bar stays visible, which is the normal symptom of
an unpublished fingerprint rather than a broken build.

## Why it can wait

The web version at `https://massai-itu.ca/mind` already installs to an Android home screen as a PWA
with a fullscreen window. The APK's only real advantages are a Play Store listing and a launcher icon
that looks less like a shortcut. Neither is worth blocking the builder on.
