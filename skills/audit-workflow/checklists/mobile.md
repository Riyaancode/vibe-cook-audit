# Mobile reviewer checklist (Capacitor, Expo, React Native)

If `expo` or `revenuecat` plugins are installed, load their skills first.

1. Is the app a bundled build or a remote-URL webview? Note App Store guideline risk for thin wrappers.
2. Identifier consistency: bundle id / package name in app config vs deep-link files (`apple-app-site-association`, `assetlinks.json`) vs docs.
3. Native projects present (`ios/`, `android/`) or generated on demand; build config documented.
4. Push: device token registration wired in the app (not just backend); permission prompts; server credentials (APNs key, FCM service account) referenced from secrets; unregister on sign-out.
5. Deep links / universal links: handler wired (`appUrlOpen`, `Linking`); paths match server files.
6. Auth on native: OAuth uses a native redirect scheme with PKCE; session persistence via secure storage.
7. Store requirements: in-app account deletion reachable from settings; privacy manifest; permissions strings; sign in with Apple when other social logins exist.
8. In-app purchases (RevenueCat/StoreKit/Play Billing): entitlement checks server-side; webhooks verified; restore purchases.
9. Offline: what works offline, sync queue, conflict handling, storage bounds.
10. Media capture: camera/mic permissions; upload resumability on cellular; size limits.
11. Env/feature flags that differ per platform; secrets never in the bundle.
