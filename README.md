# Sealed Notes

Encrypted notes app for Android. All notes are stored encrypted; access is protected by a master password and biometrics. Built for non-rooted devices to protect data from other apps.

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

## Building from source

```bash
./gradlew assembleDebug
./gradlew assembleRelease
```

Also builds via GitHub Actions.

## Requirements

· Android 6.0 (API 23) or newer.
· Biometrics support on the device.
· System PIN fallback: API 30+.

## Limitations

· Does not protect against root access.
· Does not protect against a compromised device.
· No automatic cloud sync.
· Security depends on master password strength and Android Keystore.

## License

MIT License.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED. THE AUTHOR IS NOT LIABLE FOR ANY DAMAGES OR LOSSES ARISING FROM THE USE OF THIS SOFTWARE.
