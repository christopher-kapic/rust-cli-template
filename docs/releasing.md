# Releasing

Releases are automated by [`dist`](https://opensource.axo.dev/cargo-dist/)
(cargo-dist). You bump a version and push a tag; CI builds every platform,
generates installers + checksums, and publishes a GitHub Release. Updating your
Homebrew tap is a manual copy step by default (and can be automated — see
`tap/README.md`).

## What gets built

From `dist-workspace.toml`:

| Platform | Target triple |
|----------|---------------|
| Linux x86_64 | `x86_64-unknown-linux-gnu` |
| Linux ARM64 | `aarch64-unknown-linux-gnu` |
| macOS Intel | `x86_64-apple-darwin` |
| macOS Apple Silicon | `aarch64-apple-darwin` |
| Windows x64 | `x86_64-pc-windows-msvc` |

Installers generated: **shell** (`curl … | sh`), **PowerShell** (`irm … | iex`),
and a **Homebrew formula** (`mycli.rb`), attached to the release as an artifact.
You copy that formula into your tap repo to publish it — see `tap/README.md`.

## Cutting a release

```sh
# 1. Bump the version in Cargo.toml (e.g. 0.1.0 -> 0.1.1).
# 2. Commit it.
git commit -am "release: v0.1.1"
# 3. Tag and push. The tag triggers release.yml.
git tag v0.1.1
git push && git push --tags
```

`release.yml` (generated — do not hand-edit) does the rest. Watch it in the
repo's Actions tab; when it's green, the GitHub Release has all the artifacts.

## One-time setup

1. **Repo must be public** for `curl | sh` and `brew install` to work without
   auth tokens.
2. **Homebrew tap:** create the tap repo — see `tap/README.md`. No token is
   needed for the default manual publish flow; one is only required if you opt
   into automatic publishing.
3. **Verify the workflow** before your first real release with a dry run on a
   branch: `dist plan`.

### Publishing the Homebrew formula

By default the formula is generated as a release artifact and you copy it into
your tap repo yourself — a few seconds, no extra secrets, and the tap commit is
plainly yours. The steps (and how to automate it instead) are in
`tap/README.md`.

## Changing release behavior

Never edit `.github/workflows/release.yml` by hand — it's regenerated and your
edits will be lost. Instead edit `dist-workspace.toml`, then:

```sh
dist init      # interactive; or
dist generate  # re-emit release.yml from the config
```

Commit the regenerated workflow. CI has no "release drift" check, so regenerate
deliberately whenever you change `dist-workspace.toml`.

## Optional: crates.io and cargo-binstall

Not enabled by default. To also publish to crates.io so `cargo install` /
`cargo binstall` work:

1. Add a `CARGO_REGISTRY_TOKEN` secret.
2. Add `"cargo:"` to a publish step (see dist docs on `publish-jobs`), or run
   `cargo publish` in a small added job. `dist`'s artifacts already carry the
   metadata `cargo binstall` needs.
