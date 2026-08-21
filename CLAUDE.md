# Adventures of Violet & Lainey

A toddler storybook + mini-games app, and the picture book it comes from.
Written and illustrated by Mindy Jenkins.

## Where things are

| | |
|---|---|
| **The app** | `build-web/www/index.html` — one file, vanilla HTML/CSS/JS. **This is the source of truth.** |
| `app/index.html` | Legacy copy. Do not edit; do not overwrite. |
| Artwork / audio / video | `build-web/www/art/`, `build-web/www/audio/` — **gitignored** (too large). Not recoverable from git. |
| iOS project | `build-web/ios/` — gitignored *except* `AppDelegate.swift` and `project.pbxproj`, force-added so `cap add ios` can't wipe them |
| Android project | `build-web/android/` — scaffolded, never built (no JDK/SDK on this machine), gitignored |
| Website | repo root is the published site. `index.html` = landing page, `privacy-policy.html` = policy, `getapp/` = assets + a redirect |

## Facts

- Bundle ID `com.mindyjenkins.violetlainey`, App Store ID **6798822460**
- Site: **violetandlainey.com** (GitHub Pages, `CNAME` in repo root). The QR code in the book encodes exactly `https://violetandlainey.com`
- Contact: `Sivadbooks@gmail.com`; `hello@violetandlainey.com` forwards to it (receive-only)
- Book: Canva design `DAHNfTWljT4`, 8.75in square, 42 pages
- Apple Kids Category: no external links, no ads, no in-app purchases, no data collection

## Building

```bash
cd build-web && npx cap sync ios
cd ios/App && xcodebuild archive -scheme App -configuration Release \
  -destination 'generic/platform=iOS' -archivePath ~/Desktop/VioletLainey-N.xcarchive \
  -derivedDataPath ~/Library/Developer/Xcode/vlArchiveDDN -allowProvisioningUpdates
xcodebuild -exportArchive -archivePath ~/Desktop/VioletLainey-N.xcarchive \
  -exportPath ~/Desktop/VioletLainey-buildN -exportOptionsPlist ExportOptions.plist
```

Bump `CURRENT_PROJECT_VERSION` in `project.pbxproj` first. Use a **fresh** `-derivedDataPath`
each time — a stale one loses the Swift Package checkout. `ExportOptions.plist` lives in the
repo root deliberately; it used to be in `/tmp` and got cleaned.

## Things that have bitten before

- **Verify visually, don't assume.** The kite crate bug and the stretched landing-page images
  were both invisible until something was actually rendered and measured.
- Artwork is **WebP**. The two coloring-page images are lossless on purpose — the paint game
  reads them back with `getImageData` and flood-fills against a colour tolerance, so lossy
  artefacts around the outlines could let a fill leak.
- The simulator's recorder only writes a frame when the screen *changes*, so static game
  screens record as a slideshow. Drive interactions to get usable footage.
- iOS bundle lookups are case-sensitive; macOS is not. A wrong-case filename works locally
  and fails on device.
- Sound needs a real device. The simulator can't confirm any of it.

## Status

Build 13 archived (iPad video fill, kite crate fix, WebP artwork). App Store Connect is
filled in and build 13 selected. **Not submitted** — Mindy is testing on her own phone first.

On release day: swap the "Coming soon" span in the root `index.html` for the real App Store
link (the exact replacement is in a comment right above it).
