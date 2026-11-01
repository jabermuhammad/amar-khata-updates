# Amar Khata Android release checklist

Use this checklist only after an authorized Production APK build has completed. Never guess or prefill APK URLs, checksums, byte sizes, or version codes. The APK binary belongs in a GitHub Release asset, not in this repository's Git history.

## Before publishing an APK

1. Confirm the Production EAS build completed and download its APK artifact.
2. Verify the artifact's package ID, version name, and Android version code against the app configuration and the APK itself. The current bootstrap configuration is package `com.jabermuhammad1.amarkhatamobile`, version `1.0.1`, Android `versionCode` `2`.
3. Confirm the APK is signed by the same EAS-managed Android signing key that must receive in-place updates. Do not regenerate or replace the key. If the installed build has a different signature, Android cannot update it in place; do not uninstall it without a separate data backup and explicit decision.
4. Confirm the APK is no larger than 150 MiB (`157,286,400` bytes), which is the updater's accepted maximum.

## Publish the APK as a GitHub Release

1. In `jabermuhammad/amar-khata-updates`, create a Release on branch `main` with a tag matching the app version (for the bootstrap build, `v1.0.1`).
2. Attach the exact, unmodified EAS APK artifact to the Release. Do not commit the APK to this repository or alter it after collecting its metadata.
3. After GitHub has published the Release asset, copy its actual HTTPS download link. Do not construct or guess the link.

## Collect metadata from the exact APK

Run these commands against the exact APK attached to the Release (or a byte-identical downloaded copy):

```powershell
$apkPath = '<path to the downloaded release APK>'
Get-FileHash -LiteralPath $apkPath -Algorithm SHA256
(Get-Item -LiteralPath $apkPath).Length
```

Record the actual SHA-256 digest and byte length. Verify they refer to the final Release asset. The size must be a positive integer and must not exceed `157286400`.

## Create and publish `update.json`

Only after the APK Release asset exists, create `update.json` at the repository root. It must be a JSON object with exactly the updater's required data fields:

- `packageId`: exact Android package ID from the APK.
- `version`: actual APK version name.
- `versionCode`: actual positive Android version code from the APK.
- `apkUrl`: actual HTTPS link copied from the GitHub Release asset.
- `sha256`: actual 64-character hexadecimal SHA-256 digest.
- `changelog`: array of human-readable release-note strings.
- `sizeBytes`: exact APK length in bytes.

Validate the finished JSON against [`update.schema.json`](update.schema.json), then review it against the APK metadata. Commit and push `update.json` to `main` only after all values match the published asset. Do not add sample manifests or placeholder values to the live branch.

The app reads the manifest from:

`https://raw.githubusercontent.com/jabermuhammad/amar-khata-updates/main/update.json`

After pushing, open that URL and confirm it returns valid JSON. Verify the APK link, digest, size, package ID, version, and version code match the Release asset exactly.

## Future releases and device verification

- Increase Android `versionCode` for every new APK; it must be greater than the installed app's version code for the app to offer an update. The bootstrap configuration uses code `2`, so the next update must use at least `3`.
- Keep the package ID and EAS-managed signing key unchanged for in-place updates.
- Publish the new Release asset first, then update the manifest with metadata from that exact APK.
- On a test phone, verify the update notice, seven-day “later” dismissal, APK download, exact size and SHA-256 checks, Android's install-unknown-apps permission prompt, and in-place installer flow.
- Confirm the update repository has no APK binaries committed in Git. APKs should exist only as Release assets.
