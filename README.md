# Sealed Notes

Encrypted notes app for Android. Notes are stored encrypted; access is protected by a master password and biometrics. Built for non-rooted devices to protect data from other apps.

## What it protects you from

- Other apps on a non-rooted phone reading your notes.
- Other apps reading files in app-private storage.
- Offline attackers who get the encrypted files without the key.
- Tampering with stored notes (AES-GCM detects modification).
- Path traversal attempts in note IDs and file names.

## What it does not protect you from

- Root access.
- A compromised device or OS.
- Weak master passwords.

## Security

- Threat model: protection from other apps on a non-rooted phone.
- Encryption: AES-256-GCM.
- Keys: Android Keystore.
- Key derivation: PBKDF2, 600,000 iterations.
- Notes: `notes/<id>.enc`, index: `index.enc`.
- Atomic writes via `.tmp` + `rename()`.

## Stack

- Java, Android
- minSdk 23, targetSdk 34, AGP 8.1.1
- Application ID: `com.inkgame348.sealednotes`
- Built with GitHub Actions

## Requirements

- Android 6.0 (API 23) or newer
- Biometrics support on the device
- System PIN fallback: API 30+

## Building from source

Requires JDK 17 and Android SDK 34. Run `./gradlew assembleDebug` for a debug APK, `./gradlew assembleRelease` for release; CI builds on push via GitHub Actions
