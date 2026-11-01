# NooDS automatic upstream sync and snapshot releases

Official upstream: [Hydr8gon/NooDS](https://github.com/Hydr8gon/NooDS) (`master`).

## Repository branch policy

- `master`: existing default branch. Keeps local CI automation and documentation, not force-reset to upstream.
- `upstream-master`: a clean, exact upstream mirror, advanced **only by fast-forward**, never force pushed.
- Other branches or Releases are not overwritten or deleted.

Because GitHub only schedules workflows from the default branch, the workflow is committed to `master` while actual source builds check out the exact SHA of `upstream-master`.

## Schedule

Workflow: [`.github/workflows/upstream-sync-release.yml`](../.github/workflows/upstream-sync-release.yml)

GitHub checks approximately every **10 minutes** (scheduler timings are not guaranteed). Each run resolves the latest official `master` SHA. If the source changed, it is mirrored to `upstream-master`. A new or manually retried SHA without a Release can build Windows, Linux and macOS.

Unchanged SHA + scheduled run => skips costly builds even if the previous build failed. Use **Actions → Sync NooDS upstream and publish desktop snapshots → Run workflow** to retry manually.

## Release policy

A GitHub pre-release named `noods-upstream-<first twelve SHA characters>` is published only after **all three** desktop builds succeed. Package checksums are recorded in `SHA256SUMS.txt`.

- Windows x64: ZIP with compiled `noods.exe`, upstream LICENSE and README.
- Linux x64: native `.flatpak` using the official Flatpak build recipe.
- macOS: DMG using upstream Nix+wxWidgets 3.3.2+bundle script.

All three builds pin the same source SHA; new upstream commits do not change the source mid-build. No commercial ROM, BIOS or firmware files are included. These are unofficial builds: compilation does not prove runtime compatibility.

## Legacy GitHub Actions workflow

The existing `.github/workflows/autobuild.yml` is retained for historical/optional multi-platform building, but changed to **manual-only** with its destructive rolling release/delete step disabled. This prevents accidental replacement or deletion of pre-existing Releases and avoids triggering 8-platform compilations for each local CI configuration edit.

The original upstream includes additional Android, iOS, Switch, Wii U and PS Vita build recipes. The scheduled snapshot workflow initially prioritizes the three desktop operating systems for release reliability.

Built-in `GITHUB_TOKEN` with `contents: write` on sync/release jobs is used; no personal access token is required when the repository permits Actions writes.
