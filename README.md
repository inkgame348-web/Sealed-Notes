# Sealed Notes

An encrypted notebook for Android. Notes are stored in the app's private folder in encrypted form. The encryption key is created and kept in Android Keystore — the system's hardware-backed storage, from which it cannot be extracted.

Built entirely on a phone. This entire project — code, UI, CI/CD pipeline, releases — was written, tested, and shipped from an Android phone (Termux + GitHub mobile). No laptop, no desktop IDE.

---

## Download

Get the latest signed APK from the [Releases](../../releases/latest) page.

1. Open the latest release.
2. Download the `.apk` file from Assets.
3. On your phone, allow installation from unknown sources when prompted.
4. Install and open.

Requires Android 6.0 (API 23) or newer.

---

## Features

### Notes
- Notes with title, content, and attachments (photos, documents, any file)
- Pin important notes to the top
- Multi-select and bulk actions
- Trash bin — deleted notes auto-delete after 30 days
- Restore or permanently delete from Trash
- Empty trash button

### Encryption and privacy
- AES-256-GCM encryption via Android Keystore (hardware-backed)
- Screenshot protection (`FLAG_SECURE`) — screenshots come out black
- Blank preview in the recent-apps switcher
- No internet permission — the app sends data nowhere
- No analytics, no ads, no tracking

### Backups
- Export to file — save all notes as JSON anywhere
- Import from file — restore from a JSON backup
- Backup to cloud — share via Google Drive, Telegram, Gmail, etc.
- Auto-backup — optional, saves notes to a chosen folder after every change
- Backup password — optional encryption for backup files (PBKDF2 + AES-256)
- `hasFragileUserData` — Android can offer to keep app data on uninstall

### Interface
- Light, dark, and system themes
- English and Russian
- Minimalist flat Material icon set
- Smooth animations throughout (card appearance, dialog transitions, activity slides, gear rotation, animated trash lid, selection mode)
- Localized toasts, dialogs, and titles

---

## Tech Stack

- Language: Java 17
- SDK: Android 34 (minSdk 23)
- UI: AndroidX AppCompat, Material Components, RecyclerView, CardView
- Crypto: AES-256-GCM + Android Keystore (notes), PBKDF2 + AES-256 (backups)
- Build: Gradle 8.1.1, AGP 8.1.1
- CI/CD: GitHub Actions (automatic signed APK builds on every push)

---

## Privacy

- No master password, no biometrics — protection via Android sandbox and hardware Keystore
- No internet — the app sends your data nowhere
- No analytics, no ads, no tracking
- No access to other apps' files

### How encryption works

1. On first launch, a random 256-bit AES key is generated inside Android Keystore — the system's secure vault, backed by the phone's TEE (Trusted Execution Environment) on modern devices.
2. All notes are serialized to JSON, encrypted with this key, and written to `notes.enc` in the app's private folder (`/data/data/com.inkgame348.sealednotes/files/`).
3. On read, the file is decrypted with the same key. The key itself never leaves the Keystore — it cannot be extracted, even with root.
4. GCM mode adds authentication: if even one byte of `notes.enc` is modified, decryption fails.

### What it protects against

- Other apps reading your notes
- Someone pulling `notes.enc` via file manager — it's ciphertext
- Thief connecting the phone to a PC — ADB can't read without unlock
- Screenshots and screen recording — `FLAG_SECURE` blocks them
- Recent-apps preview — shown as blank

### What it does NOT protect against

- A thief with your unlocked phone (no master password or biometrics)
- Root-level malware that can dump app memory while it's running
- Exported backups — those are plaintext unless you set a backup password
- Forgotten backup password — there is no way to recover it

---

## Backups and restore

Since the encryption key lives in Android Keystore and dies with the app on uninstall, you must back up your notes separately.

### Option 1: Auto-backup (recommended)

1. Open Settings → Auto-backup.
2. Toggle it on and pick a folder (e.g. `Documents/SealedNotes/`).
3. Optionally set a backup password to encrypt the file.
4. After every change, a fresh backup file is written to the chosen folder.
5. This folder survives app uninstall.

### Option 2: Manual export

1. Open Settings → Export to file.
2. Save the JSON anywhere (Google Drive, Telegram, etc.).

### Restore

1. Open Settings → Import from file.
2. Pick the backup file.
3. If it was password-encrypted — enter the password.
4. All current notes will be replaced with the backup content.

---

## Building from source

APK is built automatically via GitHub Actions on every push to `main`. To build locally, you'll need JDK 17 and the Android SDK. Keystore properties go in `keystore.properties` at the project root (see CI workflow for the format).

```bash
./gradlew assembleRelease
