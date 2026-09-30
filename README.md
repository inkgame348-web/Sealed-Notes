# Sealed Notes

Sealed Notes is an encrypted notes manager for Android. It stores all data locally on the device, encrypted with AES-256-GCM. There are no servers, no cloud sync, and no analytics.

## What it does

Sealed Notes lets you keep private notes that cannot be read by other apps, other people using the same device, or anyone who gets hold of the phone without your unlock credentials.

You create, edit, delete, and pin notes. Deleted notes go to a trash and can be restored. Files can be attached to any note. There is a global undo that reverses the last action, and a local undo inside the note editor that reverses text changes.

Auto-lock closes the vault after a configurable period of inactivity. Light and dark themes are supported, as are Russian and English languages.

## How it works

### Encryption

All note content is encrypted with AES-256-GCM. The data key that encrypts notes is generated once and stored on the device, wrapped by the Android Keystore. The Keystore holds hardware-backed keys, so the raw data key never leaves the device in plaintext.

Each note is stored as a separate encrypted file. An encrypted index contains only titles and short previews for the list screen — the full text of a note is loaded from disk only when the note is opened.

Attachments are stored in a separate folder and are also encrypted.

The master password, if set, is derived with PBKDF2-HMAC-SHA256 using 600,000 iterations. Backups use the same derivation, and each backup file contains its own salt and IV.

### Locking and unlocking

On first launch you enable biometric unlock. Optionally, you can add a master password as a backup unlock method, and to make encrypted backups portable to other devices.

The vault unlocks with a biometric prompt (fingerprint, face, or device PIN/pattern depending on the device). If the master password is set, you can also unlock with it — either as a fallback if biometrics fail, or by choice.

When the app is locked — by auto-lock timeout, by pressing the lock button, or automatically when you leave the app in the background — the decryption key is wiped from memory.

### Auto-lock

Auto-lock closes the vault after a configurable period of inactivity. When you return, you have to unlock again.

### Trash

Deleted notes are not removed immediately. They are moved to a trash, where they stay until you delete them permanently or empty the trash manually. From the trash you can restore a note back to the main list.

### Undo

There are two kinds of undo:

- **Global undo** — a button in the top bar of the main screen. It reverses the last structural action: deleting a note, restoring it, pinning or unpinning, or deleting it permanently from the trash. The undo history holds up to 500 actions. Even notes permanently deleted from the trash can be recovered through undo, because their encrypted file is moved to an internal holding area instead of being physically deleted.

- **Local undo** — a button inside the note editor. It reverses changes to the note's text, step by step, about two characters at a time.

### Automatic backup

Auto-backup saves an encrypted copy of all notes to a folder you choose, every 12 hours. The folder can be local or cloud-synced through another app (Google Drive, Dropbox, Nextcloud, etc.). Old backups are rotated, keeping the 14 most recent copies.

Backups are encrypted with a separate password, set when you enable auto-backup. That password is not the same as the master password — it is chosen independently, and you are asked to confirm it, to prevent typos.

After each backup, the file is read back and decrypted to verify integrity. If verification fails, the copy is deleted and the previous backup remains.

You can also run a backup on demand without waiting for the schedule.

### Export and import

Notes can be exported manually as a single encrypted file or as plain JSON. Import supports both formats, and merges them with existing notes by timestamp. Encrypted exports use a password you choose, with confirmation.

### Storage

Everything is stored in the private directory of the app:

- `notes/<id>.enc` — individual notes
- `index.enc` — list index (titles and previews)
- `journal.enc` — undo history
- `journal/<id>.enc` — notes held back for undo
- `attachments/<noteId>/` — attachments

All content is encrypted. The index contains only titles and short previews.

## Requirements

Android 6.0 (API 23) or newer. Biometric hardware is recommended but not required — the app works with a master password alone.

## Install

Download the latest APK from the Releases page and install it on your device. Enable installation from unknown sources if prompted.

## Build

```bash
git clone https://github.com/inkgame348/sealednotes.git
cd sealednotes
./gradlew assembleRelease
