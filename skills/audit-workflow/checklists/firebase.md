# Firebase reviewer checklist

If the `firebase` plugin is installed, load its security-rules guidance first.

1. Firestore/RTDB rules: no `allow read, write: if true`; per-document ownership; validation of writes (field types, sizes); rules deployed match repo.
2. Storage rules: path-based ownership; size and content-type limits.
3. Cloud Functions: auth checks on callable and HTTP functions; admin SDK usage guarded; webhook signature verification; idempotent triggers.
4. Config exposure: client config is public by design, but check API key restrictions are documented; no service account JSON in the repo.
5. Auth: providers configured; custom claims for roles set server-side only; email verification requirements.
6. Push (FCM): token registration and revocation; server credentials from secrets.
7. Indexes: `firestore.indexes.json` present for composite queries; unbounded queries.
8. Emulator config and seed for local dev.
