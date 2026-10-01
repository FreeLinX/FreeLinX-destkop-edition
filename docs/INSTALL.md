# Installing FreeLinX

## 1. Boot the live system

Write the ISO to a USB stick (or use it as a VM's CD) and boot it. The boot
menu offers:

- **FreeLinX 1.0 Live Desktop** — the live desktop.
- **Live Desktop (basic graphics)** — for a machine whose screen stays black:
  skips the GPU drivers and uses the firmware framebuffer.
- **Text Console** — no desktop; `flxinstall` installs from here.
- **Rescue Shell** — a root shell.

The live desktop logs in automatically as the user `live` (no password;
privileged tools ask `doas` themselves). Nothing is written to the disks
until you run the installer, and the `live` user does not exist on the
installed system.

## 2. Run the installer

Click the installer icon in the panel (or run `doas flxinstall` in a
terminal). It asks, in order:

1. language, keyboard layout, time zone (e.g. `Asia/Baku`)
2. host name and the **root password** (required)
3. your user account (recommended; it may use `doas`)
4. WiFi network (optional — skip on Ethernet)
5. desktop or console
6. the target disk — **everything on it is erased**

The installed system boots on BIOS and UEFI machines (Limine). `/usr`, `/etc`,
`/var`, `/root`, `/bin`, `/sbin` and `/lib` are kept on the disk; `/home` is a
separate partition. The kernel and firmware update with xpkg like any other
package (`linux`, `linux-firmware`).

## 3. First boot

Log in on the login screen with the account you created. Then:

```sh
doas xpkg upgrade          # fetch the latest packages
```

or open **Packages** from the panel.

## Tips

- **Time zone**: `flx.tz=Area/City` on the kernel command line, or *Time
  Zone...* in the Network menu.
- **Network**: the WiFi icon in the panel (Network Manager), or `flxwifi` /
  `flxnet` in a terminal.
- **Bluetooth**: `bluetoothctl` (Network menu).
- **Graphics**: `flx.xdriver=fbdev|kms|glamor` on the kernel command line
  overrides the automatic choice.
- **Services**: runit; `sv status /var/service/*`.
