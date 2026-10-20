# QEMU G5

A self-contained x86_64 **AppImage** that boots a virtual Power Mac G5
(PowerMac7,3) with QEMU — ready to install or run Mac OS X 10.3–10.5 (Panther
through Leopard) on any modern Linux desktop.

One file. No install, no system QEMU, no config files. The complete ROM set
(OpenBIOS G5 + all VGA/network firmware) is bundled inside.

## Quick start

1. Download **`qemu-g5-x86_64.AppImage`** from the
   [latest release](../../releases/latest).
2. Make it executable and run it:

   ```sh
   chmod +x qemu-g5-x86_64.AppImage
   ./qemu-g5-x86_64.AppImage
   ```

3. **First run** opens a file picker — select a Mac OS X install disc image
   (`*.iso`, `*.img`, `*.dmg`, `*.toast`, `*.bin`, `*.cue` are recognised) and
   enter a disk size (default `20G`). The VM disk and NVRAM are created in
   `~/.local/share/qemu-g5/` and reused on every later launch.

No dialog tool? Pass the disc image as the first argument instead:

```sh
./qemu-g5-x86_64.AppImage ~/isos/MacOSX_10.4.iso
```

## Add it to your application menu

```sh
./qemu-g5-x86_64.AppImage --install      # freedesktop: desktop entry + icons
./qemu-g5-x86_64.AppImage --uninstall    # undo it
./qemu-g5-x86_64.AppImage --third-party  # use appimaged / AppImageLauncher if present
```

`--install` writes `~/.local/share/applications/qemu-g5.desktop` (with an
absolute `Exec` path to wherever this AppImage lives) and installs the icon in
the `hicolor` theme and `~/.local/share/pixmaps/`. Works on any
freedesktop-compliant desktop: Cinnamon, GNOME, KDE, XFCE, MATE, …

Prefer a third-party integrator? `--third-party` detects `appimaged` (and
copies the AppImage into its watched `~/Applications/` folder) or
[AppImageLauncher](https://github.com/TheAssassin/AppImageLauncher).

## Usage

```
./qemu-g5-x86_64.AppImage [flags] [disc-image] [extra QEMU args...]
```

| Flag / argument      | Effect                                              |
|----------------------|-----------------------------------------------------|
| *(none)*             | Boot the VM from its disk                           |
| `disc-image`         | Boot/install from that disc (removes `nvram.img` first so the CD wins over Startup Disk) |
| `--install`          | Create the desktop menu entry (see above)           |
| `--uninstall`        | Remove the desktop menu entry                       |
| `--third-party`      | Integrate via appimaged / AppImageLauncher          |
| `-h`, `--help`       | Show all options                                    |
| *anything else*      | Passed straight through to `qemu-system-ppc64`      |

## Configuration

Environment variables (no config file needed):

| Variable        | Default          | Notes                                             |
|-----------------|------------------|---------------------------------------------------|
| `QEMU_G5_MEM`   | `2048`           | MB, max `16384`                                   |
| `QEMU_G5_SMP`   | `2`              | CPUs, max `16` (Tiger/Leopard; Panther supports 2)|
| `QEMU_G5_CPU`   | `970fx`          | The CPU model PowerMac7,3 used                    |
| `QEMU_G5_GPU`   | `ati-radeon9800` | or `std` / `none`                                 |
| `QEMU_G5_NET`   | `slirp`          | set to `none` to disable networking                |
| `QEMU_G5_DISK`  | `$STATE/disk.qcow2` | Alternative VM disk                            |
| `QEMU_G5_STATE` | `~/.local/share/qemu-g5` | Where disk + NVRAM live                   |
| `QEMU_G5_RAW`   | *(unset)*        | `1` = skip the wrapper, arguments go raw to QEMU  |

Example — 4 CPUs, 8 GB RAM, no network:

```sh
QEMU_G5_SMP=4 QEMU_G5_MEM=8192 QEMU_G5_NET=none ./qemu-g5-x86_64.AppImage
```

## Requirements

* Linux x86_64 with FUSE (`libfuse2t64` on Ubuntu 24.04 / Linux Mint 22,
  `libfuse2` on older releases)
* `qemu-img` for first-run disk creation (`sudo apt install qemu-utils`)
* `zenity` or `kdialog` for the GUI picker/prompts (optional — without them,
  pass the disc image as an argument)

## Good to know

* **NVRAM / Startup Disk:** Mac OS X records its boot choice in NVRAM, which
  overrides QEMU's `-boot` order. When you launch with a disc image, the
  wrapper deletes `nvram.img` once so the disc actually boots; later launches
  boot from the disk as the guest configured.
* **CPU counts:** Panther supports 2 CPUs, Tiger/Leopard up to 16.
* **Graphics:** the default `ati-radeon9800` matches what Mac OS X can drive
  out of the box; use `QEMU_G5_GPU=std` for plain VGA or `none` for headless.
* **Install media is not included** — bring your own legally obtained discs or
  images (`.toast` from Toast Titanium discs works fine).

## What's inside

* `g5` — small bash bootstrap: defaults, first-run dialogs, NVRAM handling,
  menu-install flags; then execs QEMU
* `bin/qemu-system-ppc64` — QEMU 11.1.50 (`v11.1.0-rc2-1511-g70668cf600`)
* `share/qemu/` — the complete QEMU ROM set **plus** the G5 OpenBIOS ELF
* `qemu-g5.desktop` + `qemu-g5.svg` — menu entry and icon (also bundled for
  `--install`)

The AppImage is a classic squashfs type-2 image (AppImageKit runtime). To
rebuild after changing `g5`:

```sh
./qemu-g5-x86_64.AppImage --appimage-extract
# edit squashfs-root/g5, then:
mksquashfs squashfs-root new.squashfs -comp gzip -noappend
head -c 193728 qemu-g5-x86_64.AppImage > runtime
cat runtime new.squashfs > qemu-g5-x86_64.AppImage && chmod +x qemu-g5-x86_64.AppImage
```

## Credits

* **[Andrew_R's "Anylinux" QEMU AppImages](https://www.emaculation.com/forum/viewtopic.php?t=12817)**
  on the Emaculation forums — the base build this project is derived from.
* **Cat_7** and the Emaculation community for the G5 machine configuration,
  device mix and NVRAM findings:
  [macOS G5 package thread](https://www.emaculation.com/forum/viewtopic.php?t=12803),
  [Windows G5 package thread](https://www.emaculation.com/forum/viewtopic.php?t=12804).
* **[Emaculation](https://www.emaculation.com/forum/)** — the forum where this
  setup was proven, end to end.
* [QEMU](https://www.qemu.org/) and [OpenBIOS](https://www.openbios.org/) —
  the actual engines.

## License

The wrapper scripts and desktop files in this repository are MIT-licensed.
The AppImage bundles QEMU (GPL-2.0-or-later) and OpenBIOS (GPL-2.0); see
[qemu.org](https://www.qemu.org/) and [openbios.org](https://www.openbios.org/)
for their source code and licenses. Mac OS X is © Apple Inc.; no Apple software
is included or permitted to be included.
