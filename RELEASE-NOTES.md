# FreeLinX 1.0.2 (not released yet)

Graphics, video and a sweep for GNU code that the 1.0 checks could not see.
The package repository already carries everything below: on an installed
system `doas xpkg upgrade` brings it in.

## New

- **AMD graphics acceleration.** Mesa now includes radeonsi (and llvmpipe,
  a much faster software renderer: OpenGL 4.5 instead of 1.1) on LLVM 18.
- **Hardware video decoding (VA-API)** for Intel (intel-media-driver,
  i965), AMD and NVIDIA (Mesa); FreeLinX Web (Firefox) and FFmpeg use it.
  `vainfo` shows what your GPU can decode; `glxinfo` and `glxgears` are the
  real Mesa tools now.
- The live session runs as an ordinary user (`live`) instead of root; the
  installer, Packages and the system tools ask for privileges with doas.
- Kernel and firmware are packages (`linux`, `linux-firmware`): an
  installed system can update them with xpkg.

## Fixed

- **No GNU code anywhere.** GNU ncurses was linked into vim, tmux, htop,
  ncdu, pstree, less, alsamixer, nnn and the curses games, and GNU Wget was
  in the repository. Built with clang, they carried no GCC mark, so the
  checks passed them. Everything now uses NetBSD curses; wget is gone (use
  `curl` or `ftp`); the `ncurses` package is an empty transitional one that
  removes GNU ncurses on upgrade. On an installed 1.0.x, also run
  `doas xpkg install vim tmux htop ncdu pstree tetris` (they came with the
  image, not as packages), or reinstall from the 1.0.2 ISO. The publish
  check now also recognises GNU project code itself, not only GCC and glibc.
- `grep` never matched `$` (end of line); `su`, `newgrp`, `calendar`,
  `tset` and `xstr` rejected their arguments (`su root` printed usage), and
  `su` was not setuid.
- The terminal database works: one `/usr/share/terminfo.cdb` serves every
  program; `tput`, `tset` and `tic` could not read or build it before.
- Programs installed with xpkg into `/bin`, `/sbin` and `/lib` now survive
  a reboot on installed systems.

---

# FreeLinX 1.0.1

A fix release for the first real-hardware reports. Thanks to everyone who
tried 1.0 on their machines.

**Download:** `freelinx-1.0.1-x86_64.iso` below. Already installed 1.0?
`doas xpkg upgrade` brings the new Xorg and GTK and fixes up the input
configuration; for the hot-plug part (new devices while the desktop is
running) reinstall from the 1.0.1 ISO.

## Fixed

- **Mouse and touchpad.** Input devices were picked once, by name, and at
  most two of them: many touchpads and USB mice ("USB OPTICAL MOUSE",
  wireless receivers) never worked, and nothing plugged in later did. Xorg
  now finds every keyboard, mouse and touchpad itself and picks up devices
  plugged in while the desktop runs; libinput drives them, with
  tap-to-click and two-finger scrolling on touchpads.
- **Right-click menu** closed again as soon as the button was released.
- **OpenGL (GLX)** looked for its drivers in a path from the build machine.
- **GTK** looked for its compose/dead-key data in the same wrong place.
- Several configuration directories (PAM, fonts, Mesa, Xorg) were missing
  from the source repository, so a fresh build could lose them.

## Notes

- FreeLinX is 64-bit only: 32-bit CPUs (Core Duo, Celeron M, Atom N2xx)
  cannot start it.

---

# FreeLinX 1.0.0 — first release

FreeLinX is an independent Linux distribution with a NetBSD userland, the
musl C library and an LLVM toolchain. Nothing in the image or the package
repository is built by GCC or linked against glibc: every ELF file is checked
before an image or a package is published.

**Download:** `freelinx-1.0.0-x86_64.iso` below (BIOS + UEFI, ~500 MB).
Verify it with `SHA256SUMS`.

## Highlights

- **Openbox desktop** with a panel (launchers, window list, clock with time
  zone) and a right-click menu. OpenGL acceleration on Intel (iris, crocus,
  i915) and NVIDIA (nouveau); everything else, virtual machines included,
  gets real KMS modes with CPU rendering.
- **FreeLinX Web** — Firefox ESR 153, built for musl with clang: full
  JavaScript, H.264/AAC video (FFmpeg, LGPL build), VP9, AV1.
- **Graphical installer** — language, keyboard, time zone, host name, root
  password, user account, WiFi, disk. Installs a BIOS + UEFI system (Limine)
  with persistent `/usr /etc /var` and a separate `/home`.
- **Packages** (`flxpkg`) and **xpkg 1.0**: ~400 packages from a signed
  repository — the NetBSD userland, developer tools, games and the whole
  desktop stack (Xorg, Mesa, GTK, the browser, Bluetooth, ...), so
  `xpkg upgrade` updates the desktop too.
  - Ed25519-signed indexes, TLS with certificate verification, every
    download checked against the index.
  - Atomic installs (write beside, rename, one database transaction).
  - File-conflict detection, dependency-aware removal, `autoremove`.
  - Edited configuration is never overwritten (`.xpkgnew`).
  - The image's own packages are registered: `xpkg list`, `xpkg verify` and
    `xpkg upgrade` describe the running system.
- **Login screen** (greetd + tuigreet); console logins with getty.
- **Network manager** (wired and WiFi, WPA2/WPA3), `flxwifi` and `flxnet`
  on the command line, OpenNTPD for time.
- **Bluetooth** (`bluetoothctl`).

## Hardware support

| | |
|---|---|
| Storage | NVMe, SATA/AHCI, USB mass storage (UAS), SD/MMC incl. Realtek readers |
| WiFi | Intel (iwlwifi), Qualcomm Atheros (ath9k/10k/11k/12k), Realtek (rtw88/rtw89), MediaTek (MT7921/7922), Broadcom |
| Bluetooth | Intel, Realtek, MediaTek, Qualcomm, Broadcom (USB), UART |
| Audio | Intel SOF / SoundWire (2018+ laptops), HDA codecs, AMD ACP, USB audio |
| Graphics | Intel, NVIDIA (nouveau), AMD (amdgpu/radeon), virtio, simpledrm |
| Input | I2C-HID touchpads, Wacom, Xbox / PlayStation / Switch pads |
| Other | UVC webcams, USB4 / Thunderbolt, amd-pstate |
| File systems | ext4, exFAT, NTFS (ntfs3), FAT, ISO 9660, UDF, squashfs |

Firmware: linux-firmware 20260916 and Sound Open Firmware 2026.09.1,
zstd-compressed.

## Under the hood

| Layer | Component |
|---|---|
| Kernel | Linux 6.6.21 LTS, built with clang/LLD 21.1.8 |
| libc | musl 1.2.5 |
| C++ runtime | LLVM libc++ / libc++abi / libunwind 21.1.8 |
| Userland | NetBSD 10.1; toybox for ps, top, free, pgrep, getty, login |
| Init | runit + mdevd |
| Graphics | Xorg 21.1.24, Mesa 24.0.9, GTK 3.24, cairo, pango, harfbuzz |
| Auth | Linux-PAM 1.7, doas |
| Time | IANA tzdata 2026d |
| Packages | xpkg 1.0 |

Rust programs (greetd, tuigreet) use a from-source Rust standard library, so
rustup's GCC-built musl objects never reach the image.

## Requirements

- x86_64 CPU, BIOS or UEFI.
- RAM: 2 GB minimum (the live system and the installed OS image run from
  RAM), 4 GB recommended.
- Disk for installing: 12 GB or more.

## Known limitations

- No OpenGL acceleration on AMD GCN and newer (radeonsi needs an LLVM build
  of Mesa) or in virtual machines: those draw on the CPU.
- No hardware video decoding (VA-API); video is decoded on the CPU.
- Tested in QEMU/KVM end to end: live boot, install to disk, graphical login,
  browser as a user, packages, WiFi (with a simulated radio), Bluetooth.
  Real-hardware reports for WiFi, Bluetooth, audio and GPUs are very welcome.
- Kernel and firmware are updated with new releases of the image, not
  through xpkg.
- The live session logs in as root.

## Reporting problems

<https://github.com/FreeLinX/FreeLinX/issues> — please include the output of
`uname -a`, `xpkg --version` and, for hardware problems, `dmesg`.
