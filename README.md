# rust-editor beta

Builds of **rust-editor 0.1.0-beta.4** for invited testers. This repository holds only the
builds, the install notes and a signed Flatpak repository. There is no source code here.

**Available now: Linux (x86_64).** macOS and Windows follow once their checks are done.

The editor never uses the network. It has no telemetry and no update check: updates reach you
only through `flatpak update` when you install from the repository below, or when you download
the next file yourself.

## Install on Linux

Pick one. All four are the same program.

### Small, uses your system: `.deb`, `.rpm` or AppImage

Download the file from the [releases page](https://github.com/bastiangrubbe-ship-it/rust-editor-beta/releases)
and check it against `SHA256SUMS.txt` on the same page.

```bash
sudo apt install ./rust-editor-0.1.0-beta.4-linux-x86_64.deb    # Debian 12, Ubuntu 22.04 and later
sudo dnf install ./rust-editor-0.1.0-beta.4-linux-x86_64.rpm    # Fedora 41 and later
```

The package is about 7 MB and uses your system's graphics driver and libraries. It adds a menu
entry and "Open with" for text files. No GTK, WebKit or GNOME libraries are needed.

The `.AppImage` is one file with nothing to install: make it executable and run it. It needs
glibc 2.35 or newer.

These do not update themselves: download the next beta and install it the same way.

### Sandboxed, updates itself: Flatpak from the beta repository

```bash
flatpak remote-add --user --if-not-exists rust-editor-beta https://bastiangrubbe-ship-it.github.io/rust-editor-beta/rust-editor.flatpakrepo
flatpak install --user rust-editor-beta dev.rusteditor.Editor
```

The first install also downloads the freedesktop runtime (26.08) from Flathub, once. To fetch
its English texts only, run this before installing:

```bash
flatpak config --user --set languages en
```

To update later: `flatpak update --user dev.rusteditor.Editor`.

**Coming from an earlier beta?** `flatpak update` moves you to beta 4. Beta 4 brings your tabs, splits and
folder back at the next start, and adds a settings file ("Settings: open the settings file" in the command
palette, Ctrl+Shift+P). Beta 3 added Find in folder (Ctrl+Shift+F).
If you came from beta 1, `flatpak uninstall --unused` afterwards removes the old GNOME runtime it needed.

The repository is signed. Its key fingerprint is
`1EDF 6BA4 D65C 6E53 FFFB  1333 7DE3 9B72 CADA 34EE`, and `flatpak` refuses anything not
signed with it. The `.flatpak` file on the releases page is the same build without updates.

Full notes: [INSTALL.md](INSTALL.md). Known issues: [KNOWN-ISSUES.md](KNOWN-ISSUES.md).

## Reporting a problem

Tell the person who invited you, rather than opening an issue here. Help → Copy diagnostics puts
the version, your system and the crash-log path on the clipboard, ready to paste.

If Ctrl+O does nothing, start `rust-editor` from a terminal, press Ctrl+O, and send what the
terminal prints along with the diagnostics.

**This is a beta. Keep a copy of anything important before you edit it.**
