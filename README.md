# Amar Khata Android Updates

This public repository is the official update metadata source for [Amar Khata](https://github.com/jabermuhammad/amar-khata), a Bengali-first Android notes and shopping-list app.

The app fetches its manifest from:

`https://raw.githubusercontent.com/jabermuhammad/amar-khata-updates/main/update.json`

The manifest is not published yet because the first Production APK has not been built. Until a real APK and its verified metadata are available, keep `update.json` absent. The app is designed to hide update notices when the manifest is missing or invalid.

## Repository contents

- `update.json` — the live update manifest, to be added only after a real APK has been published as a GitHub Release.
- `update.schema.json` — JSON Schema matching the updater in the main app repository.
- `RELEASE_CHECKLIST.md` — release and verification procedure.
- `.gitignore` — prevents APKs, local credentials, logs, and generated files from being committed to this repository.

APK binaries belong in GitHub Releases, not in the Git history. The app source and Firebase Rules remain in the separate [main source repository](https://github.com/jabermuhammad/amar-khata). Do not put Firebase credentials, signing keys, passwords, or user data in this repository.

## Manifest contract

The runtime manifest is a JSON object with these required fields:

| Field | Expected value/format |
| --- | --- |
| `packageId` | `com.jabermuhammad1.amarkhatamobile` |
| `version` | APK version name, two to four numeric dot-separated parts |
| `versionCode` | Positive safe integer; must be greater than the installed app's code to trigger an update |
| `apkUrl` | HTTPS URL of the exact APK asset attached to a GitHub Release |
| `sha256` | 64 hexadecimal characters, calculated from that exact APK |
| `changelog` | Array of release-note strings |
| `sizeBytes` | Exact positive byte length of that APK; maximum accepted size is 157,286,400 bytes (150 MiB) |

The app validates the package ID, version fields, HTTPS URL, digest, changelog type, and size. It verifies the downloaded APK's byte length and SHA-256 before handing it to Android's package installer. Android may ask the user to allow installs from Amar Khata and confirm installation.

## Release process

Follow [`RELEASE_CHECKLIST.md`](RELEASE_CHECKLIST.md) for each release. In short: build and verify the APK, publish that exact file as a GitHub Release asset, compute its SHA-256 and byte size, create `update.json` from those real values, validate it against `update.schema.json`, then commit and push the manifest to `main`.

Never publish guessed or placeholder release metadata. Do not commit the APK binary. Keep the same EAS-managed signing key across releases so Android accepts in-place updates.

## Current status

- Update repository branch: `main`.
- No APK Release, version tag, or runtime `update.json` is published yet.
- Bootstrap app configuration is version `1.0.1`, Android `versionCode` `2`, package ID `com.jabermuhammad1.amarkhatamobile`.
- A bootstrap manifest for code `2` can notify earlier compatible installs, but it will not notify an app already running code `2`. The next update must use a higher `versionCode`.
