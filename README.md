# rust-editor beta

Builds of **rust-editor 0.1.0-beta.1** for invited testers. This repository holds only the
builds, the install notes and a signed Flatpak repository. There is no source code here.

**Available now: Linux (x86_64).** macOS and Windows follow once their checks are done.

The editor never uses the network. It has no telemetry and no update check: updates reach you
only through `flatpak update` when you install from the repository below.

## Install on Linux

**Recommended: Flatpak from the beta repository.** It gets updates.

```bash
flatpak remote-add --user --if-not-exists rust-editor-beta https://bastiangrubbe-ship-it.github.io/rust-editor-beta/rust-editor.flatpakrepo
flatpak install --user rust-editor-beta dev.rusteditor.Editor
```

The first install also downloads the GNOME 51 runtime from Flathub, once. To update later:
`flatpak update --user dev.rusteditor.Editor`.

The repository is signed. Its key fingerprint is
`1EDF 6BA4 D65C 6E53 FFFB  1333 7DE3 9B72 CADA 34EE`, and `flatpak` refuses anything not
signed with it.

**Or download a file** from the [releases page](https://github.com/bastiangrubbe-ship-it/rust-editor-beta/releases):
the `.flatpak` bundle (no updates) or the `.AppImage` (one file, uses your system's GTK and
WebKitGTK 4.1, needs glibc 2.35 or newer). Check it against `SHA256SUMS.txt` on the same page.

Full notes: [INSTALL.md](INSTALL.md). Known issues: [KNOWN-ISSUES.md](KNOWN-ISSUES.md).

## Reporting a problem

Tell the person who invited you, rather than opening an issue here. Help → Copy diagnostics puts
the version, your system and the crash-log path on the clipboard, ready to paste.

**This is a beta. Keep a copy of anything important before you edit it.**
