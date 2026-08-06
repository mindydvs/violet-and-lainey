# Build & Release — Adventures of Violet & Lainey

## Quick facts
- Bundle ID: `com.mindyjenkins.violetlainey`
- Team ID: `973AS6G3Z4`
- Signing profile: **VioletLainey AppStore** (manual, App Store distribution)
- Xcode project: `build-web/ios/App/App.xcodeproj`
- Web source of truth: `build-web/www/`  ← edit `index.html` here

## To release an update
1. Edit `build-web/www/index.html`
2. Bump the version in Xcode (App target → General → Version / Build)
3. `cd build-web && npx cap sync ios`
4. Xcode → destination **Any iOS Device (arm64)** → **Product → Archive**
5. **Distribute App → TestFlight & App Store → Upload**
   (or drag the exported .ipa into the **Transporter** app)

## Signing is MANUAL — do not switch it back to Automatic
Automatic signing tries to create a *development* profile, which requires a
registered device. With zero devices registered it fails with
"Your team has no devices from which to generate a provisioning profile" —
even when archiving for the App Store.

The App target's **Release** config is therefore set to:
```
CODE_SIGN_STYLE            = Manual
CODE_SIGN_IDENTITY         = Apple Distribution
PROVISIONING_PROFILE_SPECIFIER = VioletLainey AppStore
DEVELOPMENT_TEAM           = 973AS6G3Z4
```

Capacitor's template also hardcodes `CODE_SIGN_IDENTITY = "iPhone Developer"`
in Release — that was removed. **If you ever re-run `npx cap add ios`, the iOS
folder is regenerated and all of this is lost.** Re-apply these four settings.

Profile file: `~/Library/MobileDevice/Provisioning Profiles/`
Profile expires: **2027-08-06** — regenerate at developer.apple.com before then.

## Still to do before public release
- Privacy policy URL (required for the Kids Category)
- Screenshots (iPhone; also iPad unless you drop iPad support)
- App Store description, keywords, age rating (4+)
