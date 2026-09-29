# Packages

FreeLinX installs software with **xpkg**. On the desktop, **Packages** does the
same with a window: search, filter by installed / updates, install, remove,
upgrade.

```
xpkg search <word>          find packages by name or description
xpkg show <name>            details from the repository
xpkg install <name>...      install, with everything it needs
xpkg reinstall <name>...    install again
xpkg remove <name>...       remove (refused while another package needs it)
xpkg autoremove             remove dependencies nothing needs any more
xpkg update                 refresh the package indexes
xpkg upgrade [name...]      refresh, then upgrade everything (or the names)
xpkg outdated               what upgrade would change
xpkg list [-e]              installed packages (-e: only ones you asked for)
xpkg info <name>            an installed package
xpkg files <name>           its files
xpkg owns <path>            which package a file belongs to
xpkg verify [name...]       check installed files against the database
xpkg clean                  delete downloaded package files
xpkg repo list|add|remove   repositories
```

Changing the system needs root: `doas xpkg ...`. Useful options: `-n`
(show what would happen), `-f` (override conflict and dependency checks),
`-v` (list files).

## What xpkg guarantees

- **Only signed software.** The repository index is signed (Ed25519) and
  lists the size and SHA-256 of every package; xpkg checks both before
  installing anything, over TLS with certificate verification. The FreeLinX
  key is `/etc/xpkg/keys/freelinx.pub`.
- **No half-installed packages.** Files are written beside their target and
  renamed into place, and the database changes in one transaction.
- **Your configuration stays yours.** If you edited a file in `/etc`, an
  upgrade saves the new version as `<file>.xpkgnew` instead of overwriting
  it, and removing a package leaves your edited file.
- **No silent overwrites.** A package cannot replace another package's files
  unless you force it.

## The repository

About 400 packages: the NetBSD 10.1 command line, developer tools (git,
vim, tmux, ...), games, and the whole desktop (Xorg, Mesa, GTK, FreeLinX
Web, Bluetooth, ...). It is served from
`https://huggingface.co/datasets/FreeLinX/packages/resolve/main`.

Every package in it passed the same check as the image: no GCC-built code, no
glibc.

## Making a package

```sh
xpkg-create create --name hello --version 1.0-1 --description "Says hello" \
    --depends musl --stage ./stage --output hello-1.0-1.xpkg
doas xpkg install ./hello-1.0-1.xpkg
```

`./stage` holds the files as they should appear under `/`. See the
[xpkg repository](https://github.com/FreeLinX/xpkg) for the format, the
repository tools and signing.
