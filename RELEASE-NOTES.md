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
