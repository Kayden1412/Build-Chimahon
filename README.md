# build-chimahon

CI-only project for building a **test APK** of
[`Kayden1412/chimahon`](https://github.com/Kayden1412/chimahon) on GitHub Actions.
Nothing is cloned or built locally by this repository.

At workflow time it:

1. Clones `Kayden1412/chimahon` (default ref: `main`) with submodules.
2. Applies `patches/0001-rename-test-package.patch`.
3. Fetches the native dictionary renderer dependencies.
4. Builds the APK.
5. Uploads the APK as a workflow artifact.

## Package rename

| | Package |
|---|---|
| Original Chimahon release | `app.chimahon` |
| This test build (`assembleDebug`) | `app.chimahon.test.dev` |
| This test build (`assemblePreview`) | `app.chimahon.test.beta` |

The launcher label is changed from **Chimahon** to **Chimahon Test**.
The Google Drive OAuth redirect scheme is renamed too, so it cannot collide
with the original app.

Both variants are signed with the CI debug key, so they install alongside the
original Chimahon without replacing or updating it.

## Run the workflow

1. On GitHub open **Actions** -> **Build Chimahon test APK** -> **Run workflow**.
2. Pick the source ref and task:
   - `assembleDebug` (default) builds the fast, debuggable APK.
   - `assemblePreview` builds the release-like preview APK.
3. When the run finishes, download the `chimahon-test-<task>-<run>` artifact
   and install the APK from it.

The current `Kayden1412/chimahon` `main` contains both fixes:

- `60e6fee` - native dictionary renderer font crash
- `ea71c0f` - anime library pager crash

To test an older source revision, set **ref** to a branch, tag, or commit SHA.

## Notes

- No repository secrets are required for `assembleDebug` or `assemblePreview`.
- The first run downloads the native dependencies and can take a while. Later
  runs restore them from the Actions cache.
- If the source files change so the patch no longer applies, the workflow fails
  at the **Apply test-package patch** step instead of silently building the
  original package name.
