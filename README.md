# Dolphin Fresh (snap)

[![Get it from the Snap Store](https://snapcraft.io/static/images/badges/en/snap-store-black.svg)](https://snapcraft.io/dolphin-fresh)

Unofficial snap of [Dolphin](https://dolphin-emu.org), the open-source GameCube
and Wii emulator. It is built from source at the release tag of
[dolphin-emu/dolphin](https://github.com/dolphin-emu/dolphin) pinned in
`snapcraft.yaml`, with SDL 3 built from source alongside it. `snapcraft.yaml`
targets amd64 and arm64; the Store currently carries amd64 builds only. Not
affiliated with or endorsed by the Dolphin Emulator Project.

    sudo snap install dolphin-fresh
    sudo snap connect dolphin-fresh:joystick          # gamepads
    sudo snap connect dolphin-fresh:removable-media   # games on /media, /mnt
    sudo snap connect dolphin-fresh:bluez             # optional, Wii Remotes over Bluetooth
    sudo snap connect dolphin-fresh:raw-usb           # optional, GameCube adapter, Bluetooth passthrough
    sudo snap connect dolphin-fresh:hardware-observe  # optional
    sudo snap connect dolphin-fresh:mount-observe     # optional

## Automatic updates

`.github/workflows/build-and-publish.yml` runs every day at 09:00 UTC, on
manual dispatch from the Actions tab, and on pushes to `main` that change
`snapcraft.yaml`, `command-chain/` or the workflow itself:

1. `check-upstream` reads the newest tag of `dolphin-emu/dolphin`
   (`git ls-remote`) and compares it with the `version:` pinned in
   `snapcraft.yaml`. When upstream is ahead it posts a notice in the run
   summary; it does not bump the version by itself.
2. `build-and-publish` builds the pinned tag and uploads it to `edge`.
3. `promote-to-stable` promotes `edge` to `stable`.

A new Dolphin release reaches the snap once `version:` in `snapcraft.yaml` is
updated and pushed to `main`.

Required repository secret: `SNAPCRAFT_STORE_CREDENTIALS`
(`snapcraft export-login --snaps=dolphin-fresh --channels=edge,stable creds.txt`).

## Notes

- `HOME` and the XDG directories point into `~/snap/dolphin-fresh/common`:
  config in `.config/dolphin-emu`, the user directory (saves, NAND, shader
  cache) in `.local/share/dolphin-emu`. They survive snap updates; data left in
  the older `~/snap/dolphin-fresh/current` location is moved there on start.
  Because of this, "Home" in Dolphin's file dialogs is that directory; your
  real home folder is reachable through its full path.
- Games are reachable in your real `$HOME` (non-hidden paths) and, after
  connecting `removable-media`, in `/media` and `/mnt`.
- The `hidraw` plug is declared, but desktop systems have no generic hidraw
  slot to connect it to, so devices that need raw HID access may not work.
  Wii Remotes work through `bluez`, USB adapters through `raw-usb`.
- No firmware, system files or games are included. Dump your own discs and,
  if you need them, your own Wii NAND and GameCube IPL.
- Bugs specific to this packaging belong in this repository's issue tracker,
  not upstream.

## License

Packaging files in this repository: GPL-3.0 (see `LICENSE`).
Dolphin itself: GPL-2.0-or-later, Copyright the Dolphin Emulator Project.
SDL 3: Zlib.
