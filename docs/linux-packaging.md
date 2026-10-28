# Linux packaging and support

## Supported Linux distribution

The supported baseline for the amd64 Debian package is **Ubuntu 24.04 LTS**.
Build and installation are checked with the `linux-deb` job on `ubuntu-24.04`.
The package is not advertised as compatible with Ubuntu 22.04 or Debian 12:
their runtime dependencies (including the Ubuntu 24.04 t64 transition) differ.
The Linux graphical app still needs a working graphical desktop/session; WSL2
requires WSLg or a comparable display server.

## Build

```bash
sudo apt-get update
sudo apt-get install -y build-essential curl git pkg-config libssl-dev \
  libsqlite3-dev libdbus-1-dev libglib2.0-dev libgtk-3-dev \
  libayatana-appindicator3-dev libjavascriptcoregtk-4.1-dev \
  libwebkit2gtk-4.1-dev libsoup-3.0-dev libz3-dev
cd apps/codex-plus-manager
npm ci
npm run vite:build
cd ../..
cargo build --release -p codex-plus-manager
bash scripts/installer/linux/build-deb.sh --package-only
```

The script reads the default version from
`apps/codex-plus-manager/package.json` using its absolute path, independent
of the calling directory. To supply a release version explicitly, pass it
after `--package-only`.

```bash
sudo apt install ./dist/linux/codex-plus-plus_*.deb
dpkg -V codex-plus-plus
ldd /usr/bin/codex-plus-plus
```

The package contains exactly one executable, `/usr/bin/codex-plus-plus`.
The Linux release job uploads `.deb` and `.tar.gz` independently of the
Windows/macOS update metadata job. Linux packaging failure must not block
`latest.json` for existing platforms.

The CI build/installation check is not a substitute for a manual GUI launch
test in a real desktop session.
