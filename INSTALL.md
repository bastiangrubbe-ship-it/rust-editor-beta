# Installing the rust-editor beta

Thank you for testing. This is beta `0.1.0-beta.5`. The builds are not signed by Apple or
Microsoft, so each OS warns once before it first opens. The steps below get you past that warning
in two steps or fewer.

The beta makes no network connections. It does not check for updates and sends nothing: if it
crashes, you send the crash log yourself (see "If something goes wrong" below).

Read `KNOWN-ISSUES.md` (next to the downloads) before you start. It lists what the beta does not
do yet.

## 0. Check the download (optional, 1 minute)

Each download has a SHA-256 checksum in `SHA256SUMS-<os>.txt`. Compare it with yours:

- macOS: `shasum -a 256 ~/Downloads/rust-editor-0.1.0-beta.5-macos-universal.dmg`
- Windows (PowerShell): `Get-FileHash $HOME\Downloads\rust-editor-0.1.0-beta.5-windows-x86_64.zip`
- Linux: `sha256sum ~/Downloads/rust-editor-0.1.0-beta.5-linux-x86_64.AppImage` (or `.flatpak`, `.deb`, `.rpm`)

The long number it prints must match the line in the checksum file. If it doesn't, don't open the
download. Tell us instead.

## macOS (12 or later, Apple silicon or Intel)

Install: open `rust-editor-0.1.0-beta.5-macos-universal.dmg`, then drag **rust-editor** onto
**Applications**.

The first time you open it, macOS says it cannot check the app for malicious software:

1. Double-click rust-editor in Applications. When macOS says it can't open it, click **Done**
   (or **OK**).
2. Open **System Settings → Privacy & Security**, scroll down to the line saying rust-editor
   was blocked, click **Open Anyway**, and confirm with your password or Touch ID.

After that it opens like any other app. If you prefer the Terminal, this one command replaces
both steps: `xattr -dr com.apple.quarantine /Applications/rust-editor.app`

## Windows (10 or 11, 64-bit)

Install: right-click `rust-editor-0.1.0-beta.5-windows-x86_64.zip`, choose **Extract All…**, and
extract it anywhere you like, for example your Documents folder. Keep the extracted folder as it
is: `rust-editor.exe` needs the `resources` folder beside it.

The first time you open `rust-editor.exe`, Windows shows "Windows protected your PC":

1. Click **More info**.
2. Click **Run anyway**.

To open a document by double-clicking it, right-click the document once and choose **Open with →
Choose another app → More apps → Look for another app on this PC**, then pick `rust-editor.exe`.

## Linux (x86_64): .deb, .rpm, AppImage or Flatpak

Four downloads, the same program. Pick one.

- **.deb** (Debian, Ubuntu and relatives) and **.rpm** (Fedora and relatives): a normal install into the system, with
  a menu entry and "Open with" for text files. No sandbox: it uses your system's graphics driver and libraries,
  which the package pulls in. No automatic updates.

- **Flatpak** (from the beta repository, or the file `rust-editor-0.1.0-beta.5-linux-x86_64.flatpak`):
  runs on any distribution that has
  Flatpak (Fedora, Ubuntu with Flatpak added, Debian, Arch …), with every library it needs. It runs
  without network access, and it can open and save files anywhere you can (it asks for your whole
  file system so that a save can keep a file's permissions, attributes and links).
- **AppImage** (`rust-editor-0.1.0-beta.5-linux-x86_64.AppImage`): one file, nothing to install, but
  it uses your system's libraries.

### .deb (Debian 12, Ubuntu 22.04 and later)

Needs glibc 2.35 or newer. Install the file you downloaded (it pulls in the libraries it needs, X11, xkbcommon,
Vulkan, EGL and Mesa's drivers, from your distribution):

    sudo apt install ./rust-editor-0.1.0-beta.5-linux-x86_64.deb

Start it from your application menu ("rust-editor"), with `rust-editor`, or from a file's **Open with** menu.
To remove it: `sudo apt purge rust-editor`. Your own crash logs and recovery copies in
`~/.local/share/rust-editor/` stay; delete that folder if you want them gone.

### .rpm (Fedora 41 and later)

    sudo dnf install ./rust-editor-0.1.0-beta.5-linux-x86_64.rpm

Same menu entry and commands. To remove it: `sudo dnf remove rust-editor`.
The file is not signed. Fedora accepts an unsigned file you name yourself. If your `dnf` is set to check local
packages (`localpkg_gpgcheck=1`) it stops with "The package is not signed"; then use
`sudo dnf install --nogpgcheck ./rust-editor-0.1.0-beta.5-linux-x86_64.rpm`.

### Updating the .deb or .rpm

There is no repository, so `apt upgrade` and `dnf upgrade` do not find a new beta. Download the next file and install
it with the same command: it replaces the old one and keeps your settings.

### Flatpak from the beta repository (gets updates)

The repository is at `https://bastiangrubbe-ship-it.github.io/rust-editor-beta/`.

1. Add the repository once. It brings its signing key, so `flatpak` refuses anything it did not sign:
   `flatpak remote-add --user --if-not-exists rust-editor-beta https://bastiangrubbe-ship-it.github.io/rust-editor-beta/rust-editor.flatpakrepo`
2. Install: `flatpak install --user rust-editor-beta dev.rusteditor.Editor`. The first install also
   downloads the freedesktop runtime (26.08) from Flathub, several hundred MB, once (if your system has never used
   Flathub, `flatpak` offers to add it; say yes).

To update to a newer beta: `flatpak update --user dev.rusteditor.Editor` (a plain `flatpak update`,
or your software centre's updates, do it too).

### Flatpak from the file

1. If your system has never installed from Flathub, add it once:
   `flatpak remote-add --user --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo`
2. Install the file: `flatpak install --user ./rust-editor-0.1.0-beta.5-linux-x86_64.flatpak`
   (or double-click it to open it in your software centre). The first install also downloads the
   freedesktop runtime (26.08) from Flathub, several hundred MB, once.

A file install does not update itself: install the next file the same way.

Either way, start it from your application menu ("rust-editor"), or with `flatpak run dev.rusteditor.Editor`.
To remove it: `flatpak uninstall --user dev.rusteditor.Editor`.

### AppImage

Needs: glibc 2.35 or newer (Ubuntu 22.04, Debian 12, Fedora 36 and later) and FUSE 3
(`fusermount3`, on most desktops), and the X11 client libraries every X11 or XWayland desktop has
(libX11, libXcursor, libXi, libXrandr, libxkbcommon-x11). No GTK or WebKitGTK is needed.

There is no signature warning on Linux, but the file must be made executable before it runs:

1. In Files, right-click it, choose **Properties**, and turn on **Executable as Program**
   (or run `chmod +x rust-editor-0.1.0-beta.5-linux-x86_64.AppImage`).
2. Double-click it (or run `./rust-editor-0.1.0-beta.5-linux-x86_64.AppImage`).

If it says FUSE is missing, run it as
`./rust-editor-0.1.0-beta.5-linux-x86_64.AppImage --appimage-extract-and-run` instead.

## Check the version

Open the command palette (the **Commands** button at the top of the window, or ⌘⇧P on a Mac)
and choose **Help: About rust-editor**. It should say `Version 0.1.0-beta.5`.

## If something goes wrong

If rust-editor stops because of an internal error, it writes a crash log and tells you where it
is, and the next time it opens it shows the log's place again (also under **Commands → Help:
About rust-editor**). A crash of another kind (the system ending it for lack of memory, a fault
in a graphics driver) leaves no log; tell us what you were doing instead. The logs are in:

- macOS: `~/Library/Application Support/rust-editor/crashes/`
- Windows: `%LOCALAPPDATA%\rust-editor\crashes\`
- Linux: `~/.local/share/rust-editor/crashes/` (or `$XDG_DATA_HOME/rust-editor/crashes/`); for
  the Flatpak, `~/.var/app/dev.rusteditor.Editor/data/rust-editor/crashes/`

A crash log holds no text from your documents. Its message line can name a file or a line
number, and its "Package" line names the folder rust-editor runs from (which can include your
user name), so read it before you send it if that matters to you.

To report a problem, open the command palette, choose **Help: Copy diagnostics** (it copies the
version, your OS and the crash-log paths), paste that into your message, and attach the newest
crash log if there is one. Unsaved changes made more than about two seconds before a crash are
kept as recovery copies; the next launch tells you where they are.
