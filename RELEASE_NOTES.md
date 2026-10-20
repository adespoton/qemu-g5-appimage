# QEMU G5 — Initial Release

**First public release of QEMU G5:** a single-file x86_64 AppImage that boots a
virtual Power Mac G5 (PowerMac7,3) and runs Mac OS X 10.3–10.5 (Panther,
Tiger, Leopard) on any modern Linux desktop.

No install, no system QEMU, no config files — every ROM it needs (G5 OpenBIOS,
VGA, network firmware) is bundled inside.

## Downloads

| File | Size | Notes |
|------|------|-------|
| `qemu-g5-x86_64.AppImage` | ~106 MB | The whole thing: QEMU 11.1.50 + ROMs + bootstrap |
| `SHA256SUMS` | — | Checksums for the above |

```sh
chmod +x qemu-g5-x86_64.AppImage
./qemu-g5-x86_64.AppImage
```

On first run it asks for a Mac OS X install image
(`*.iso` `*.img` `*.dmg` `*.toast` `*.bin` `*.cue`) and a disk size
(default `20G`), then creates the VM in `~/.local/share/qemu-g5/`.
No dialog tools available? Pass the disc image as an argument:
`./qemu-g5-x86_64.AppImage ~/isos/MacOSX_10.4.iso`

## Highlights

- **Complete G5 machine** — `mac99,via=pmu`, 970FX CPU, ati-radeon9800
  graphics, USB keyboard/mouse, user-mode networking, IDE disk; defaults taken
  from Cat_7's proven Emaculation packages
- **Menu integration built in** — `--install` / `--uninstall` create or remove
  a freedesktop menu entry + icons anywhere on Linux;
  `--third-party` cooperates with appimaged / AppImageLauncher
- **Works from the terminal** — flags for everything, extra arguments pass
  straight through to QEMU, `--help` documents all of it
- **NVRAM handled** — automatically clears the guest Startup Disk record when
  you launch with a disc, so installs actually boot from CD
- **Tunable via environment** — `QEMU_G5_MEM`, `QEMU_G5_SMP`, `QEMU_G5_GPU`,
  `QEMU_G5_NET`, `QEMU_G5_DISK`, `QEMU_G5_STATE`… (see README)

## Requirements

- Linux x86_64 with FUSE (`libfuse2t64` on Ubuntu 24.04 / Mint 22,
  `libfuse2` on older systems)
- `qemu-img` (`sudo apt install qemu-utils`) for first-run disk creation
- `zenity` or `kdialog` for the GUI prompts (optional)

## Notes & limits

- Guest install media is **not** included — bring your own legal discs/images
- Panther supports 2 SMP CPUs; Tiger/Leopard up to 16 CPUs / 16 GB RAM
- Linux only (AppImage format); no macOS or Windows host builds

## Credits

- [Andrew_R's "Anylinux" QEMU AppImages](https://www.emaculation.com/forum/viewtopic.php?t=12817)
  (Emaculation) — the base build this release derives from
- Cat_7 and Emaculation for the G5 configuration and NVRAM findings:
  [t=12803](https://www.emaculation.com/forum/viewtopic.php?t=12803),
  [t=12804](https://www.emaculation.com/forum/viewtopic.php?t=12804)
- [Emaculation](https://www.emaculation.com/forum/),
  [QEMU](https://www.qemu.org/), [OpenBIOS](https://www.openbios.org/)

## Checksums

Generate before uploading:

```sh
sha256sum qemu-g5-x86_64.AppImage > SHA256SUMS
```

Verify after downloading:

```sh
sha256sum -c SHA256SUMS
```
