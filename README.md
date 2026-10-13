# flatpak.i4c.studio

Self-hosted, GPG-signed Flatpak repository for I4C Studio desktop apps,
built by GitHub Actions and served from GitHub Pages at
<https://flatpak.i4c.studio>.

## Install

```sh
flatpak remote-add --user --if-not-exists i4c https://flatpak.i4c.studio/index.flatpakrepo
flatpak install i4c io.github.i4ctime.protonshift
```

Updates then arrive through `flatpak update` like any other remote.

## How it works

- `flatpak/` holds the manifests (offline builds, sources pinned to a
  commit of each app's public repo — same shape as a Flathub manifest).
- [`build.yml`](.github/workflows/build.yml) runs
  [flatter](https://github.com/andyholmes/flatter) in a KDE-runtime
  container: builds every manifest, GPG-signs the OSTree repo, and
  deploys it (plus `index.html` / `index.flatpakrepo`) to GitHub Pages.
- Apps publish on the `stable` branch.
- The signing key lives only in repo Actions secrets
  (`GPG_PRIVATE_KEY`, `GPG_PASSPHRASE`).

## Releasing an app update

1. Re-copy the app's Flathub manifest from its release commit (for
   ProtonShift, `flatpak/flathub/io.github.i4ctime.protonshift.yml`, keeping
   `default-branch: stable`), so new permissions arrive with the code that
   needs them, and check its `commit:` pin is the release commit. Bumping
   only `commit:` ships new code with old permissions.
2. Push to `main` — the workflow rebuilds and republishes the repo.

## Adding an app

Drop its manifest (and any vendored-dependency yaml) into `flatpak/`,
add it to the `files:` list in `build.yml`, and add a card to
`index.html`.
