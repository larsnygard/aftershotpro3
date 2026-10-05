# Corel AfterShot Pro 3 for Arch Linux

This repository contains an updated Arch Linux `PKGBUILD` for **Corel AfterShot Pro 3 (3.7.0.446)**.

It is intended to make the final Linux version of AfterShot Pro 3 usable on current Arch Linux and Arch-based distributions such as CachyOS.

The package has been tested on **CachyOS with KDE Plasma/Wayland**.

## Background

Corel AfterShot Pro 3 is an older proprietary RAW photo editor. Corel still provides the Linux installer, but the application was built against libraries that are no longer available in current Arch Linux repositories.

The original AUR package also depended on components that have since disappeared from Arch.

This package uses Corel's **System-Qt Debian build** rather than the RPM build. This allows AfterShot to use the current system Qt 5 installation where possible.

The original application is downloaded directly from Corel during the package build and is **not distributed by this repository**.

## Compatibility runtime

Some libraries required by AfterShot and its QtWebKit component have ABI versions that are no longer provided by Arch Linux.

Rather than installing obsolete libraries globally, the PKGBUILD obtains compatible Debian 11 versions and installs the required libraries into a private runtime directory:

```text
/opt/AfterShot3(64-bit)/legacy-lib/
```

This currently provides compatibility versions of:

- Qt5 WebKit
- Qt5 WebKit Widgets
- Qt5 Positioning
- Qt5 Sensors
- ICU 67
- libjpeg 62
- libwebp 6
- libxml2 2

These libraries are only exposed to AfterShot through its launcher and do not replace the corresponding system libraries.

## Wayland / XWayland

AfterShot Pro 3 can start using Qt's native Wayland backend, but the main image view may display severe corruption, flickering or visual noise.

The launcher therefore forces the Qt XCB backend:

```bash
export QT_QPA_PLATFORM=xcb
```

On a Wayland desktop this causes AfterShot to run through **XWayland**, which fixes the image rendering problem.

The rest of the desktop can continue running normally under Wayland.

## Building

Install the standard Arch build tools if necessary:

```bash
sudo pacman -S --needed base-devel git
```

Clone this repository:

```bash
git clone https://github.com/larsnygard/aftershotpro3.git
cd aftershotpro3
```

Build and install:

```bash
makepkg -si
```

`makepkg` downloads the original AfterShot Pro 3 installer from Corel and the required compatibility libraries from Debian.

All downloaded source packages are verified using SHA-256 checksums.

## Running

After installation, AfterShot Pro 3 should appear in the desktop application menu.

It can also be started from a terminal:

```bash
AfterShot3X64
```

The launcher configures the private compatibility runtime automatically.

## Package layout

The application is installed under:

```text
/opt/AfterShot3(64-bit)/
```

Compatibility libraries are installed under:

```text
/opt/AfterShot3(64-bit)/legacy-lib/
```

The launcher is installed as:

```text
/usr/bin/AfterShot3X64
```

Desktop integration, icon and MIME information are installed under the normal `/usr/share` locations.

## Notes about namcap

Because AfterShot is proprietary precompiled software and uses a private compatibility runtime, `namcap` reports several warnings that do not represent missing files in the resulting package.

In particular, libraries under `legacy-lib` may be reported as uninstalled dependencies because they are deliberately kept outside the normal system library path.

The original Corel binaries also contain old build-time RPATH entries and lack some modern ELF hardening features such as PIE and full RELRO. These upstream binaries are intentionally left unmodified.

## License

Corel AfterShot Pro 3 is proprietary software.

This repository contains packaging files only. The AfterShot application itself is downloaded from Corel when the package is built.

See `license.txt` and Corel's licensing terms for the software license.

The Debian compatibility libraries retain their respective upstream licenses.

## Status

Tested with:

- Corel AfterShot Pro 3 3.7.0.446
- x86-64
- CachyOS / Arch Linux
- KDE Plasma
- Wayland desktop with AfterShot running through XWayland

RAW image display, thumbnails and normal application startup have been tested successfully.

## Credits

An existing aftershotpro3 package is available in the Arch User Repository (AUR):

https://aur.archlinux.org/packages/aftershotpro3

That package provided a useful reference for the original Linux packaging and license.txt. This repository uses a new packaging approach based on Corel's System-Qt Debian package, a private compatibility runtime for obsolete libraries, and an XCB launcher for compatibility with modern Wayland desktops.
