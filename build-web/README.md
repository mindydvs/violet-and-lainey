# Adventures of Violet & Lainey — deployment notes

This folder is the **clean, deployable copy** of the app. It contains only what the
app actually loads (no `.psd` files, no `elevenlabs videos` working folder).

```
index.html          the whole app (HTML + CSS + JS in one file)
art/                images + videos (incl. art/wake/, art/maze/)
audio/              narration, instructions, word clips (incl. audio/words/)
openingscene.mp4    opening video
welcomescene.mp4    welcome video (after the name entry)
.replit / replit.nix  config so Replit serves it
```

---

## 1. Running it on Replit

1. Create a new Repl → **Import from upload** (or drag this whole folder in).
2. Press **Run**. `.replit` serves the folder on port 8000.
3. Use the Webview URL to test on a phone.

Replit is great for **hosting and sharing the web version**. It does **not** publish
to the App Store — see below.

---

## 2. Getting it into the Apple App Store

Apple only accepts a **native app binary** built with Xcode. A web page/URL cannot be
submitted. The path is:

**Requirements**
- A Mac with **Xcode** (you already have a Mac)
- **Apple Developer Program** membership — $99/year — <https://developer.apple.com/programs/>
- An **App Store Connect** record for the app

**Wrap it with Capacitor** (bundles these files *inside* the app so it works offline):

```bash
cd "path/to/build-web"
npm init -y
npm install @capacitor/core @capacitor/cli @capacitor/ios
npx cap init "Violet and Lainey" com.yourname.violetlainey --web-dir=.
npx cap add ios
npx cap sync ios
npx cap open ios      # opens the project in Xcode
```

Then in Xcode: set the app icon + launch screen, pick your Team (signing),
**Product → Archive**, and **Distribute App → App Store Connect**.

---

## 3. Things Apple will check (worth knowing up front)

- **Guideline 4.2 – Minimum Functionality.** A thin wrapper around a *remote website*
  gets rejected. Bundling the content locally (as Capacitor does above) and behaving
  like a real app is the right side of this line.
- **Kids Category rules** — this app will be reviewed as a kids app:
  - A **privacy policy URL** is required (even if you collect nothing).
  - **No third-party ads or analytics** without proper consent.
  - Any link that leaves the app (support, website) needs a **parental gate**.
  - The child's name is stored in `localStorage` on-device only — nothing is uploaded.
    Say exactly that in the privacy policy.
- **Age rating** — 4+.
- **App icon** (1024×1024, no transparency) + screenshots for each required device size.

---

## 4. Size warning

This build is **~1.0 GB**, of which **~708 MB is video**.

Apple's hard limit is 4 GB, so it is *allowed*, but it's very heavy for a kids app —
slow to download and a big chunk of a family's device storage.

**Recommended before submitting:** re-encode the videos (H.264, ~1080p max, CRF ~26).
Most of these clips are 15–28 MB for a few seconds, which is far more than needed.
Free tool: **HandBrake** (<https://handbrake.fr>). Expect roughly a **3–5×** reduction
with no visible quality loss on a phone — that would bring the app closer to 250–350 MB.

Do **not** delete files that look unused — several are loaded dynamically by name
(e.g. `art/wake/drumawake.mp4`, `art/violetflykitereward.mp4`, `art/laineyXreward.mp4`).
