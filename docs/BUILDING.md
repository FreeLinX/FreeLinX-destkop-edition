# Building FreeLinX

FreeLinX is built on a Linux host (the build machine may use any tools; what
it produces may not contain GCC or glibc output). Clone the repositories side
by side:

```
FreeLinX/
├── toolchain/     clang/LLD 21 and the musl sysroot
├── Desktop-test/  the desktop image
├── ports/         NetBSD userland and other ports
├── xpkg/          package manager
└── kernel/, src/, ...
```

## The image

```sh
cd Desktop-test
stack/build-stack.sh      # musl sysroot + X11/Xorg/Mesa/GTK stack + userland
stack/build-rust.sh       # Linux-PAM, greetd, tuigreet (Rust std built from source)
stack/build-firefox.sh    # FreeLinX Web (about 2 hours on 8 cores)
stack/build-kernel.sh     # Linux 6.6 with clang
stack/build-firmware.sh   # curated, zstd-compressed firmware
stack/install-stack.sh    # copy the stack into src/rootfs, check it
stack/package-stack.sh    # the stack as xpkg packages
./build-image.sh          # initramfs; registers the packages; refuses GNU code
sh iso/buildiso.sh        # hybrid BIOS/UEFI ISO
```

## The package repository

```sh
cd xpkg
tools/publish-repo.sh             # gather ports/packages + stack packages,
                                  # check every archive, sign the index
tools/publish-repo.sh --upload    # and mirror it to Hugging Face
```

## The no-GNU rule

`Desktop-test/check-nognu.sh` inspects every ELF file for:

- a `.comment` naming a GCC compiler (GCC-built objects linked in),
- an interpreter other than musl's,
- DT_NEEDED on glibc or GCC runtime libraries,
- GLIBC symbol versions.

`build-image.sh` and `publish-repo.sh` both stop on any finding.
