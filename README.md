[README.md](https://github.com/user-attachments/files/35441539/README.md)
# LGA WHS & Pidgeon Cup Tracker

Single-file site (`index.html`) for GitHub Pages. A 4-digit PIN unlocks the app, and the shared data lives in Firebase (free Spark plan).

## 1. Put it on GitHub Pages
1. Create a repo and add `index.html` to the root.
2. **Settings → Pages**: Source = *Deploy from a branch*, Branch = `main`, folder = `/ (root)`.
3. Your site appears at `https://<username>.github.io/<repo>/`.

## 2. Create the Firebase project
1. https://console.firebase.google.com → **Add project**.
2. **Build → Firestore Database → Create database** (production mode).
3. **Build → Authentication → Get started → Sign-in method → Email/Password → Enable** (leave "Email link" off).
4. **Authentication → Users → Add user**:
   - Email: `lga@legends.club` (any address; it doesn't need to be real, but it must match `SHARED_EMAIL` in `index.html`)
   - Password: your 4-digit PIN followed by `-lga`, e.g. PIN `4821` → password `4821-lga`
5. **Authentication → Settings → Authorized domains → Add domain**: `<username>.github.io`
6. **Project settings (gear) → Your apps → Web (`</>`)**: register an app, copy the config values into `FIREBASE_CONFIG` in `index.html`.

## 3. Firestore rules
**Firestore Database → Rules**, replace with, then **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /lga/state {
      allow read, write: if request.auth != null
                         && request.auth.token.email == 'lga@legends.club';
    }
  }
}
```
(If you changed `SHARED_EMAIL`, use the same address here.)

## 4. Test
Open the site, enter the PIN. A wrong PIN says "Incorrect PIN". On two devices, save a round on one and the other updates live ("Live Multi-User Sync" badge).

## Changing the PIN
Authentication → Users → delete the shared user and add it again with the new `PIN-lga` password. Everyone is asked for the new PIN. (`PIN_SUFFIX` in `index.html` must match the `-lga` part.)

## Notes
- The live data is protected by Firebase: without the PIN, nobody can read or write it, even by calling the database directly.
- The page itself and its built-in default data (roster, past results) are still visible in the page source. The PIN only protects the shared live data.
- 4 digits is 10,000 combinations. Firebase slows repeated wrong attempts but doesn't stop a determined guesser. Use a longer code (e.g. 6 digits) for more safety.
- The admin passcode inside the app is a casual lock only.
- Keep downloading **Backup** files before and after the Pidgeon Cup.
