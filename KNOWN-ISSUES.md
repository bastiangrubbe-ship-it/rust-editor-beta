# Known issues

Ships with the beta. Each entry is a limit that was found, weighed and accepted rather than
fixed, with what the person sees. Reasons and rejected fixes are in `DECISIONS.md` (the entry
named after each item). Newest first.

## Markdown view on Linux and Windows (beta 5, 2026-11-13)

### What it is
On Linux and Windows a Markdown file (`.md`, `.markdown`) has Split and Rendered modes: the editor draws the page
itself (headings, paragraphs, lists with numbers and checkboxes, quotes, code blocks, tables, rules). There is no
webview and nothing is fetched or run. macOS keeps its webview page. HTML, SVG, CSS swatches and PDF still have no
rendered view on Linux and Windows.

### Read-only, and what a click does
Nothing can be typed into the view. A click puts the cursor of the source at the start of the block clicked; a
selection in the source scrolls the view to its block. Text in the view cannot be selected or copied; links are
shown (blue, underlined) and cannot be followed, and their address is not shown.

### What is shown as text instead
Raw HTML in the Markdown is shown as its source text, in grey, never interpreted. An image is shown as
`[image: its description]`; no picture is loaded, also not a local one. Footnotes show as `[name]`. Mathematics is
shown as its source.

### Emphasis is a colour, not a slant
`*emphasis*` is drawn in the theme's string colour (green in the built-in themes), upright: the drawing code for
the interface has no italic face yet. Strong text is bold, struck-out text has a line through it.

### Tables
Columns share the width equally and a cell is one line: longer text ends in an ellipsis. Column alignment from the
source is not applied. A table with more columns than fit shows the first ones and "… +N more columns" in the last
cell drawn.

### Code blocks
Shown in the fixed-width font without syntax colours; a line wider than the column ends in an ellipsis (no
sideways scrolling).

### Very large documents have no rendered view
Above 8 MB of Markdown the pane says the document is too large and shows nothing else; the source view is not
affected. (The view needs several times the text's size in memory.)

### Long documents
Only what the window shows (and a little ahead) is laid out, so the bar on the right edge is an estimate until the
end has been reached once. Jumping to the end (Ctrl+End with the view in focus) lays the rest out over a few
frames. After each edit the whole document is parsed again on a worker thread and laid out again from the top.

### Deep nesting
Quotes and lists nested more than 12 deep stop moving right, and more than 32 deep are shown as plain paragraphs.

### Not tested
Windows (not built since this change). Wayland and NVIDIA. The look in the light theme was checked from a hidden
screenshot only, like the dark one; no person has seen it on a display. Screen readers get the view's name and not
its text.

## Macros (beta 5, 2026-11-13)

### One macro, the last one recorded
"Macro: start recording", "Macro: stop recording" and "Macro: replay" are in the commands; `macro.record` and
`macro.replay` can be given keys under `"keys"` in the settings file (they have none built in). Recording again
replaces the macro. There are no named macros and no "replay N times".

### A replay stops at a question
When a replay brings up "unsaved changes" or "changed on disk" (or a quit is asked for while it runs), it ends
there, says how many of its events were not run, and leaves the question to you. A macro that was recorded
answering such a question therefore does not replay past it.

### A recording is lost if the window closes before it is stopped
Only "stop recording" keeps it.

### The mouse is not recorded
A click while recording is not part of the macro, and stopping says that one was made. A macro that depends on
where a click put the cursor will not do the same thing again.

### A replay does what the keys do now, where the cursor is now
The macro holds keys, typed text and commands, not their effect. It gives the same bytes when the file and the
cursor are the same as at the recording; with other text under the cursor, with another keyboard preset for the
keys it used, or with the Find bar left with other options ticked, it does something else. Find is waited for
(a replay does not press the next key before the matches are in).

### The typed text is in the settings file
A macro is kept as a list under `"macro"` in `settings.json`, readable text, including everything that was typed
while recording. Do not record a password. Recording a new macro replaces it; deleting the `"macro"` entry removes it.

### Limits
A recording stops by itself at 10,000 steps or 256 KB of typed text and keeps what it has; stopping says so. A
macro in the file that is not understood (a step that is not `type:`, `text:`, `key:` or `command:`, or a control
character in its text) is not run at all, and the status bar names the step.

### Text typed through an input method
Composed text (IME, dead keys) is recorded as the text that was committed, not as the keys that composed it.

### Not tested
macOS and Windows (not built since this change). A recording made with real key presses: the tests hand the app
keys as the window would, hidden. Replaying into the page views (HTML, Markdown, PDF), which Linux does not have.

## Key bindings (beta 5, 2026-11-13)

### Keys are changed in the settings file, not by pressing them in a panel
"Keys: show all commands and their keys" lists every command with its id and its keys, and says how to add a line
under `"keys"` in the settings file. Saving the settings file applies the keys at once. There is no panel that
records a key press yet.

### One key per line, no sequences
A binding is one key with its modifiers (`ctrl+shift+d`, `f5`). Two-step keys such as VS Code's Ctrl+K Ctrl+S are
not read; such a line is named as not understood and left out.

### Commands that have no id
"Use theme: NAME" for each installed theme and "Switch to TAB" are in the commands but cannot be given a key (the
default theme, the three presets and tabs 1 to 9 can).

### What cannot be bound
A key without Ctrl, Alt or Cmd (it would stop typing or moving the cursor, also in the panels), except F1 to F24.
The Super (Windows) key on Linux and Windows. Option with a letter on a Mac. On Windows, Ctrl+Alt with a key that
types a character on the layout in use (AltGr) types that character, whatever the file binds.

### The presets are short
`vscode` and `sublime` add those editors' keys only for commands this editor has (the command palette on F1, the
settings file, tabs by number on Alt, splits). Ctrl+K and Ctrl+Shift+K are left alone. Commands those editors have and this one does not (go to line,
duplicate line, a quick-open list) have no key. Emacs and Vim presets are not there.

### A key written twice in the file with the very same spelling
JSON keeps one of the two lines and the app cannot tell; the same key in two spellings (`Ctrl+D`, `ctrl+d`) is
named as a conflict.

### Keys on a layout without Latin letters
A binding names the character the key gives without Shift on the layout in use. On a Cyrillic or Greek layout
`ctrl+s` in the file does not match the key that types `ы`; the built-in keys have the same limit.

### The list of keys is a file
`key-bindings.txt` beside the settings file is written each time the list is shown. Changing it changes nothing.

### Not tested
macOS and Windows (not built since this change; the Mac list of built-in keys is checked against the key tables
in a Linux test only). Real key presses: the tests hand the app a key as the window would, hidden; no person has
pressed a rebound key on a real keyboard.

## Settings file (beta 4, 2026-11-13)

### A setting edited in another program applies at the next start, or with the next change from the palette
The app does not watch `settings.json`. Saved from its tab in the editor, the file applies at once (since the key
bindings, after beta 4). Edited in another program, it applies at the next start, or when a setting is next changed
from the command palette (that reads the file first, so what was edited by hand is kept).

### The file is JSON: no comments, and the app's own layout when it writes
Keys the app does not know keep their value exactly as written and their place, but the line breaks and
indentation between keys are the app's when it writes the file.

### A settings tab with unsaved edits is not refreshed
An open settings tab that has not been typed into shows the file again after a change from the palette. One with
unsaved edits is left alone; saving it then asks, because the file changed on disk.

### A settings file behind a link
If `settings.json` is a symbolic link, changes are written to the file it points to and no `.prev` copy is kept
there (a temporary file appears beside it for a moment). If that file has a mistake, changes from the palette are
refused until it is mended or the link removed.

### Three settings so far
Theme, word wrap, and reopening the last session. Fonts, sizes, tab width and key bindings are not settings yet.

### Not checked
macOS and Windows (not built since this change; the folder lock is not taken on Windows); two app windows
changing settings at the same moment (shown with two writers in one test process only).

## Unsaved text kept across a quit (beta 5, 2026-11-13)

### A quit no longer asks about unsaved changes
With "keep unsaved text when quitting" on (the default), quitting with unsaved changes asks nothing: the text is
kept and is back, still unsaved, at the next start. "Settings: keep unsaved text when quitting" in the commands
turns the question back on. The question also comes back by itself when the text cannot be kept (the copy could not
be written), and whenever the session is not kept ("reopen the last session at start" off).

### The text is stored unencrypted
In `recovery/` in the app-data folder, readable by this user only, like the recovery copies before.

### A file that changed since the quit gets the text as a separate tab
The kept text goes back onto the file's tab only when the file is byte-for-byte what it was at the quit. Otherwise
it opens as "NAME (unsaved copy)", untitled, and the file's own tab shows the file. Nothing compares or merges the
two. The same happens for files over 64 MB (they are not hashed), and for a file with bytes that are not text when
one of those bytes was removed or a replacement character (U+FFFD) was typed or pasted before the quit: the copy
then holds the text as it was on screen.

### Undo history does not come back
One undo on a restored tab gives the file's content; the steps made before the quit are gone.

### The text comes back after the whole session has
With hundreds of tabs the unsaved text appears when the last tab has been restored. A quit before that hands the
text on to the next start; a crash or a kill before that leaves the copies named in the status bar at the next
start (folder `recovery/kept/`), not restored.

### A crash, `kill -9` or power loss still only names the copies
As before (the owner's decision of 2026-11-12): the app never restores those by itself.

### Windows: a launch that ends before its window exists
On Linux and macOS such a launch (a file named on the command line that does not open, no display) hands the kept
text on to the next start. On Windows it leaves the copies, which the next start names in the status bar and keeps
in `recovery/kept/`.

### A second window open at the same time
Each window keeps its own text at its quit and the next start takes all of it. Two windows quitting at the same
moment were not tested.

### Not tested
macOS and Windows (not built since this change): the Windows logout (`WM_ENDSESSION`) and renaming the folder while
its lock is open; a logout through the Linux desktop portal; a really full disk (a folder that refuses writes was
tested instead); two live windows; anything on a real display.

## Session restore (beta 4, 2026-11-13)

### Beta 4: unsaved text and untitled documents do not come back after a quit
In beta 4 the session holds the folder, the panes, the tabs and the carets; a tab with unsaved changes is asked
about at the quit, and an untitled document is not in the list. Later builds keep unsaved text (next section).

### A file opened from the file manager joins the last session
Starting the editor by opening a file brings the last session back with that file in front, rather than a window
with that file alone.

### A file that is not there stays in the list
A tab whose file is missing at a start is named in the status bar and comes back as a tab when the file does. If
its folder exists (the file was deleted or moved) it leaves the list after five starts; if its folder is missing
too (a drive not connected) it stays. "Session: forget what is not there" in the command palette drops such
entries at once. The same holds for the folder in the tree.

### A long session comes back a few tabs at a time
The first file is on screen at once; the other tabs follow over the next moments (47 ms of work for 8 small files
on the test machine; seconds for hundreds of tabs). The list of open files is not written again until they are
all back.

### What is not restored
Scroll positions (the view shows the caret), the source/rendered mode of a tab, and selections other than the
caret. A session with more than four panes keeps all its tabs; those past the fourth pane join the last one.

### Two windows share one session
The window that ends last is the session. A second window is rare (later launches hand their files to the first).

### A session on a network folder that does not answer can hold the start
The launch asks the file system whether the session's first file and folder exist before the window opens; a mount
that hangs holds that question. Not tested.

### A session file kept as `session.json.unread`
A session file that is there but cannot be read (damaged, made by a newer version and too large, not readable) is
renamed to `session.json.unread` when the next state is written, never overwritten. Only one such file is kept.

### Not checked
macOS and Windows (not built since this change); a power cut during a write (the write syncs the file and the
folder, which a killed process cannot test); a full disk.

## Find in folder (beta 3, 2026-11-13)

### What Find in folder does not search
Folders whose name starts with a dot (`.git`, `.config`), `node_modules`, anything behind a symbolic link, files over
64 MB, and files that are not text. The panel's status line counts each kind after a search. There is no setting for
this yet, and `.gitignore` is not read.

### A file in a legacy encoding is searched as the editor opens it
Without a BOM a file is read as UTF-8 and its other bytes are kept as they are, so searching for "é" does not find the
Windows-1252 byte E9. A match that covers such a byte is never replaced ("a match covers bytes that are not valid
text"); matches beside it are.

### Files a folder replace leaves alone, with the reason
A file that changed since the search, a read-only file, a file with a second name (a hard link; not checked on
Windows), a match over bytes that are not valid text, and a replace whose result would open as different text (it
would leave a byte-order mark first in the file, or join two stray bytes into a character). The status line names the
first such file and counts the rest; it does not list them all.

### The undo of a folder replace covers the last replace, until the app quits
"Find in folder: undo the last replace" puts the old text back in each file that still holds exactly what the replace
wrote; a file changed since is left alone and named. An earlier replace cannot be undone, and nothing is kept across
a restart. Tabs the replace edited are undone in the tab (Ctrl+Z), not by this command.

### A replace in open tabs runs on the UI thread
Files on disk are replaced on a worker. Tabs are edited at once, in one step each; with many matches in a very large
open file the window can pause for that step (the same cost as Replace All in the file).

### A pattern that takes very long on a file holds a cancel until that file gives up
A regular expression with look-around or back references is given 100 million steps per search; a file it gives up on
is counted in the status line ("where the pattern took too long"). Esc and a new search stop the walk between matches
and between files, not inside one search for the next match (1.5 s per such file in the test that provokes it).

### Not checked
The panel on a real display, with a real keyboard (Linux hidden runs only); macOS and Windows (not built since:
the Mac is offline); a folder on a network share; a race in which another program swaps a folder for a link in the
instant between the check and the listing (the search could then read, never write, outside the folder).

## Linux packages: .deb and .rpm (beta 2, 2026-11-13)

### The packages do not update themselves
There is no apt or dnf repository (DECISIONS, "Single package files without repositories"). `apt upgrade` and
`dnf upgrade` never see a newer rust-editor: download the next `.deb` or `.rpm` and install it the same way over the
old one. The beta does not check for updates either, so nothing tells you a newer file exists.

### The packages are not signed
Neither file carries a signature, so `apt` and `dnf` cannot check who made it. Compare the SHA-256 checksum from
`SHA256SUMS-linux.txt` before installing. Fedora's default accepts an unsigned file you name on the command line
(`localpkg_gpgcheck` is off). If your system turns that check on (`localpkg_gpgcheck=1` in `/etc/dnf/dnf.conf`),
`sudo dnf install ./file.rpm` stops with "The package is not signed"; install it once with
`sudo dnf install --nogpgcheck ./file.rpm`, or `sudo rpm -i --nosignature ./file.rpm` on a system without `dnf`.
`apt` prints a note that it downloads the file "unsandboxed as root" when the file sits in a folder the `_apt` user
cannot read (your home folder): this is expected for a local file.

### The packages do not pull the desktop's file chooser
`xdg-desktop-portal` is only suggested, not required: a desktop has it, and a minimal system would otherwise get a
hundred extra packages. Without a portal, Open and Save As use the in-app path field (see "File panels" below).

### Removing the package keeps your crash logs and recovery copies
Removal deletes what the package installed. Your own files stay: crash logs and recovery copies in
`~/.local/share/rust-editor/` are yours, and `apt purge` and `dnf remove` do not touch your home folder.

### Not built for Debian or Fedora releases older than glibc 2.35
The binary needs glibc 2.35 or newer (Ubuntu 22.04, Debian 12, Fedora 36 and later); the package says so, and an
older system refuses it. Only x86_64 is built.

## Saving (beta 1, line 1, 2026-11-12)

### Windows: a save can fail for a moment with "insufficient system resources"

Once in 10,029 fuzzed saves on the workstation (Defender on), the in-place save of a read-only file the person chose to
save anyway stopped with "*file*: not saved: … cannot make a copy of the old bytes beside *path* (Insufficient system
resources exist to complete the requested service. (os error 1450)) … Nothing was written". The file left had the length of
the last save (148 bytes; that run did not compare the bytes of a refused save, which the gate now does), and the document
stayed unsaved. It came within seconds of two `ReplaceFileW` failures that are now
retried (DECISIONS, the line-1 entry), and a second run of the same saves met none of the three. The save does not retry
this one.

## File panels on Windows and Linux (M10, 2026-11-12)

### No real panel has been opened on a desktop by any test
The hidden tests reach the real calls only partly: on Windows the common item dialog runs for real, but in session 0
(over SSH), where nobody sees it; on Linux `rfd` talks to a stand-in portal, not GNOME's or KDE's chooser. Whether the
desktop's own chooser appears on the window, starts in the document's folder with its name, and returns the path is
checked by a person (`PERSON-CHECKS.md`, M10). On Windows the window is disabled while the dialog is up (tested in
session 0); on Linux it is not (below).

### With no portal, Save in a review of an untitled document asks in the path field and stops the quit
In the quit, close or Save All review, Save for a document that was never saved asks where in the path field (or a
panel, as on macOS). The review closes, the field asks, and the file is written when the person answers; the quit or
close does not go on by itself afterwards: the person quits again. Several untitled documents are asked about one after
another; Esc on one cancels only that one.

### Linux: a portal call that fails can open zenity, or nothing
The app asks the portal once whether it has a file chooser and keeps a "yes". After that, any failure of the call
itself (the portal refuses `OpenFile`/`SaveFile`, sends a malformed answer, or drops the connection while the chooser is
up) makes `rfd` fall back to `zenity` on its own: with zenity installed, a GTK chooser opens; without it, the request
ends as if cancelled and nothing says why. The in-app path field opens only when the check itself says no.

### Linux: a chooser that never answers leaves its request waiting
`rfd` waits for the portal's answer with no time limit. If the chooser hangs, nothing opens or saves from that request;
the window keeps working, and the next Open or Save As asks again (a second chooser).

### Linux: on GNOME or KDE under Wayland the chooser may not be attached to the window (inferred, not checked)
The window is an XWayland one there, so the portal is given `x11:<id>` as the parent. The portal backends run as
Wayland clients, and from what is known of their code (not tested here) they can only attach a chooser to an X11
parent when they have an X display of their own. If not, the chooser opens as a free window: it works, but it is not
modal to rust-editor and may open behind it. `rfd` can parent through `xdg-foreign` on a Wayland surface, but the
window is not a Wayland surface while it is an XWayland one. The person check on GNOME under Wayland says which.

### Linux: the window stays usable while the chooser is up, and a second request opens a second chooser
The portal's `modal` is a request the desktop may not honour, and an unattached chooser (above) is not modal at all.
rust-editor does not block itself while a chooser is up: a second Ctrl+O or Ctrl+Shift+S opens another chooser, and
each answer is acted on (two Save As answers write two files, each as chosen). On Windows the dialog disables the
window, so this cannot happen there.

### Linux: Open can wait up to 1.5 s on a portal that is slow to start
The first check of the portal runs on its own thread from the moment the window opens. A request that comes before it
has an answer waits up to 1.5 s for it, on the window's thread, then opens the in-app field. A portal whose activation
hangs (D-Bus allows 25 s) makes each Open and Save As wait those 1.5 s until it answers.

### Linux: no hidden test against a real desktop portal
The hidden tests use a stand-in portal that speaks the published `FileChooser` interface. A real
xdg-desktop-portal with its GTK backend is not in the test image, so whether a real portal accepts exactly what `rfd`
sends (the `x11:` parent, the folder as bytes) rests on the spec, `rfd`'s users and the person checks.

### Linux: on a Wayland desktop without XWayland the chooser is not attached to the window
With no X display the window is a Wayland one (M4, "the window is an XWayland window"), and `rfd`'s Wayland parent is
not built in. The chooser then opens as a free window: it works, but it is not modal to rust-editor and may open behind
it.

### Linux: the in-app path field is a typed path, not a browser
With no portal, Open and Save As ask for a path in the window. It starts in the document's folder, completes names
with Tab, expands `~`, checks the path and asks before replacing a file, but it cannot list a folder or browse.
Paths that are not valid UTF-8 cannot be typed into it.

### Windows: the Save As chosen in a panel is written even if a quit review opened meanwhile
While a panel is up the window is disabled, but the OS can still ask it to end the session (logout). The review then
opens behind the panel; if the person then picks a path in the panel, Save As writes it as asked, with the review still
open. Nothing is lost or written without being chosen.

### The panel's own errors go nowhere
`rfd` reports its failures (the portal refused, a COM error) through the `log` crate, which the app does not collect. A
failed panel looks like a cancelled one; the session log records only that the panel closed without a path.
## Packaging and diagnostics (beta 1, 2026-11-12)

### The builds are unsigned

macOS and Windows warn before the first launch; `INSTALL.md` has the two steps past each warning.
The Mac app carries only an ad-hoc signature and is not notarised.

### The status bar: a long notice is cut short

A long notice in the status bar (a save refusal, a recovery note) ends in "…" before the line/column and
encoding fields. Beta 1 and 2 drew it over them. The cut-off part is shown nowhere else.

### Linux: the AppImage uses the system's libraries

The AppImage holds the editor, its fonts and templates, and nothing else. It is built on Ubuntu
22.04 and needs glibc 2.35 or newer, FUSE 3 to mount itself, and the X11 client libraries
(libX11, libXcursor, libXi, libXrandr, libxkbcommon-x11; a desktop session has them, a bare
container may not: the editor then says "Failed to load one of xlib's shared libraries"). It needs no GTK and no
WebKitGTK: the Linux build does not include the web engine the HTML view would use (the HTML view
is off on Linux in this beta). The Flatpak brings its own libraries (the freedesktop
runtime).

### Linux: the Flatpak can read and write the whole file system

The Flatpak asks for `--filesystem=host`, so the sandbox does not limit which files it can open or
save; it limits the network (none) and the rest of the system. Saving through the document portal
alone would lose a file's permissions, extended attributes and hard links (DECISIONS, beta 1
Flatpak). Inside the Flatpak the app-data folder is `~/.var/app/dev.rusteditor.Editor/data/`, so
its crash logs and recovery copies are there, not in `~/.local/share/rust-editor/`. Files under
`/usr`, `/etc` and the other system folders a Flatpak cannot see are not reachable from it.

### Windows: the program has the default icon, and there is no installer

The zip holds `rust-editor.exe` and its `resources` folder. There is no `.msi`, Start menu entry
or file association (see "the zip install has no file association" below), and the exe shows
Windows' generic program icon.

### The command palette is the Help menu

Help: About rust-editor and Help: Copy diagnostics are commands in the palette (the Commands
button). Windows and Linux have no menu bar. On macOS the app menu's own "About rust-editor"
shows the standard About panel too.

### A crash alert needs a helper program on Linux

When the app crashes in a window a person sees, the alert comes from `zenity`, `kdialog` or
`xmessage`, whichever is installed; with none of them there is no alert, only the crash log, the
line on stderr, and the notice at the next launch. A crash on a thread other than the main one
shows no alert: the log is written, and the status bar names it while the window stays up.

### No graphics driver: the message needs the same helper, and a terminal otherwise

A system with no Vulkan driver and no EGL (Linux), no Direct3D 12 or Vulkan (Windows) gets "rust-editor cannot
start", with what to install, and a log in the crash-log folder; the app then ends with status 1. Beta 1 and 2
crashed there. On Linux the alert comes from `zenity`, `kdialog` or `xmessage`; with none of them installed the
message is on stderr only, so a start from the application menu shows nothing. Checked on Linux with the
drivers hidden (`ci/linux/no-gpu.sh`, with a stand-in for `zenity`); on Windows and macOS only the code path with
a stand-in was built, not run. A launch that has no display at all (no X server) still stops with an internal error.

### Only panics leave a crash log

The crash log comes from Rust's panic hook. The system ending the app for lack of memory, a stack
overflow, a fault inside a graphics driver or WebKit, and a panic in the PDF child process or in
Linux's clipboard keeper (neither installs the hook) leave no log and no notice at the next launch.
The recovery copies still cover unsaved work older than about two seconds.

### Windows crash logs have no function names

The release build carries no debug symbols, so a Windows log's backtrace lists `<unknown>` frames
(and mislabels some). Its "Where" line (source file, line and column of the panic) and the
message are what can be used. macOS and Linux logs name the functions.

### The no-network check polls on macOS and Windows

`scripts/net-watch.sh` (`lsof -i`, every 50 ms) and `scripts/drive-suite.ps1`
(`Get-NetTCPConnection`, `Get-NetUDPEndpoint`, about once a second, `netstat -ano` every tenth
poll) see a socket only while it is open, and only the app's own: a connection opened and closed
between two polls, or a name lookup done for the app by the system's resolver (mDNSResponder,
Dnscache), would not show. Linux's `strace -f -e trace=network` sees every call. Process Monitor on
Windows would too; it was not run.

### Crash logs are kept until the person deletes them

Each crash writes one file of a few kilobytes to the `crashes` folder in the app-data folder.
Nothing removes old logs.

## Windows and Linux on day one (M4, 2026-11-12)

### A hard end can lose the last two seconds of typing

Unsaved work is copied to the app-data folder about two seconds after each change, and the next launch names what was
kept and where (DECISIONS, the M4 limits entry). A logout that sends SIGTERM (Linux), and Windows' logout question,
write the copies at once. A kill, a power cut, the X server going away, or Windows ending the session after "Sign out
anyway" without asking again, lose what was typed in the last two seconds or so. The copies are the buffer's text in
UTF-8, not the file's own encoding, and they stay in `recovery/kept/` until the person deletes them.

### Linux: a logout on KDE Plasma, or on a desktop without xdg-desktop-portal, may not wait for the review

The hold is the desktop portal's logout inhibitor. GNOME honours it; KDE's portal is not known to (person check), and a
desktop with no portal at all (some tiling window managers) logs out without asking. Little is lost there: the session's
SIGTERM writes the recovery copies, and the next launch says where they are; if the X server goes away before
the signal comes (unverified on KDE), the last two seconds or so of typing can be lost. The person does not get to choose Save or
Discard before the logout, only to recover afterwards.

### Linux: copied text after quitting is served by a small keeper process

When rust-editor quits while the clipboard (or the PRIMARY selection) still holds its text, `rust-editor
--clipboard-keeper` takes it over and serves it until another program copies, then ends. It shows in the process list
meanwhile. It ends when its X server goes away (a log-out), since beta 3; beta 1 and 2 left it running, a
small idle process that ends at the next restart or with `pkill -f -- --clipboard-keeper`.

### When the system clipboard cannot be reached, copy and paste stay inside rust-editor without saying so

No display, or another program holding the Windows clipboard open past arboard's retries: Copy keeps the text inside the
app and Paste gives it back there; other programs do not see it. The session log records it (`clipboard_error`), the
status bar does not.

### Windows: the zip install has no file association

Double-click on a document does not choose rust-editor until the person picks it once with Open with > Choose another
app. The install notes say so. Linux needs the desktop file (`packaging/linux/rust-editor.desktop`), which the AppImage
installs.

### One window per user and login session

A second launch hands its files to the running window of the same user in the same login session (Windows) or on the
same display (Linux). Another user, or the same user in a second session (Remote Desktop, a second X login), gets a
window of its own. Linux without `$XDG_RUNTIME_DIR` (rare outside a desktop session) opens a window per launch. A
running window that does not answer within 10 s (hung, or still starting under a slow scan) makes the launch open its
own window.

### Linux: the window is an XWayland window on a Wayland desktop

The window is X11 on every desktop (DECISIONS, M4). On a Wayland desktop with fractional scaling (125 %, 150 %) XWayland
may draw it scaled up and soft; input methods go through XIM; and a window handed a file by a second launch may only
flash instead of coming forward (focus-stealing prevention). On a Wayland desktop with no XWayland at all (some
minimal compositors) the app opens a Wayland window instead and says so on stderr: there the clipboard stays inside
rust-editor and a logout does not wait.

### A file handed over may open twice, or not at all, in rare timing

- A launch that waits more than 10 s for the running window opens its own window; if the running one answers after
  that, the file opens in both. The changed-on-disk check guards a save from either.
- A launch answered in the last instant before the running window quits (after it stopped taking files but before the
  answer went) gets "OK", exits, and the file opens nowhere.
- Linux: two launches in the same instant, with no window running, can both end up with a window of their own.

### Copy that fails for a moment pastes the older text

When the system clipboard is briefly held by another program, Copy keeps the text inside rust-editor and a Paste then
brings back what the system clipboard held before, without saying so. arboard retries before giving up, so this needs
another program to hold the clipboard for a while.

### Linux: the logout monitor is asked for once

The portal's session monitor is made the first time anything is unsaved. If the portal is not up then, this run opens
no review on logout (the hold, when it was taken, still keeps the session).

### Recovery copies on macOS, and a quit with a very large unsaved file

The copies apply on macOS too: a force-quit or a crash leaves them, and the next launch names them. Every quit request
writes the copies first, so Cmd-Q (or a logout) with a document of hundreds of megabytes unsaved waits for that copy
before the review opens.

### Linux: a very large selection is copied for PRIMARY on every change

Select All in a file of tens of megabytes, then Shift+arrow: each key copies the whole selection for the PRIMARY
selection, which can cost tens of milliseconds a key. A recovery copy that fails to write (a full disk, a file held by
another program on Windows) is tried again only at the next change.

### Two quick middle-clicks at one spot select a word

The middle-click moves the caret as a click does, so a second one within the double-click time (or right after a left
click) selects the word under the pointer and the paste replaces it. One undo puts it back.

### Rare: a launch in the same instant can lose its recovery folder

A second rust-editor started in the instant between another's making its recovery folder and locking it can take that
folder for a leftover; the first then keeps no copies that run, without saying so. A document titled `*.part` or
`*.recovery.json` is not taken for a copy when the folder is looked at after a hard end.

### Windows: `rust-editor.exe --help` prints nothing in a console

The exe is a GUI program, so a console it is started from gets no output (redirecting to a file still works). Testers
start it from Explorer, where there was nothing to see anyway.

### Linux: if the portal is not up when the first edit is made, that run holds no logout

The hold is asked for when the count of unsaved documents goes from none to some. If the portal fails then (still
starting at login), it is asked again only the next time the count goes from none to some.

### Linux: Paste can wait up to 4 s

With another program holding the clipboard and not answering, arboard waits for it before giving up; the window does not
draw meanwhile.

### A real locked screen was never tested by an agent

The hidden test refuses every frame for two seconds and checks that edits survive and drawing comes back; the probe that
reads the lock state is checked against logind (a stand-in) and Windows' lock screen. Locking the owner's machines was not
allowed, so the real lock is a person check.

## Typing on Windows and Linux (M2, 2026-11-12)

### Alt with a letter types nothing on Windows and Linux

Alt+A, Alt+E and the like insert nothing, as in Notepad and GTK editors; Alt with an arrow does not move by word (Ctrl
does). On macOS Option still types its characters. AltGr is not Alt: AltGr characters type on both OSes.

### Ctrl+Alt with a character from the layout is text on Windows

On a layout with AltGr (Danish, German, French), left Ctrl + left Alt + E types `€` exactly as AltGr+E does, because
Windows treats the two the same. Notepad does too. A Ctrl+Alt shortcut on such a key can't be reached there.

### A dead key then a key it can't go on: Windows keeps both, Linux loses both

`^` then `n` gives `^n` on Windows. On Linux the X input method cancels the sequence, and both the accent and the next
key are lost. That next key can be a letter, a digit, an AltGr character, another dead key, Enter, Tab, Delete, Home
or an arrow (red team, round 1). Programs using the same X input method do the same. A GtkTextView keeps the accent
(`a´b`), and for a printable key keeps both (`a´2b`). The harness types every such letter pair and expects what the
OS's own tables say: Linux dk 89, de 77 and fr 69 pairs insert nothing. Space after a dead key gives the accent alone on
both OSes.

### A dead key then Ctrl+letter types a letter on Linux, and the shortcut does not run

With the X input method, `´` then Ctrl+S types `ś` and does not save. Ctrl+Z, Ctrl+A and Ctrl+V do the same (`ź`, `á`,
`ǘ`); `^` then Ctrl+S on German and French gives `ŝ`. Reproduced on dk, de and fr (red team, round 1). A GtkTextView
with its own input method gives `a^b`. The pending accent is not drawn, so a tester who brushes `´` (beside Backspace
on Danish and German) or `^` (beside P on French) and presses Ctrl+S sees nothing saved. A stray letter is then in the
file at the next save. Not fixed for the beta: the fix needs the editor to read the input method's compose state.

### Windows: Alt with numpad digits types nothing; a dead key then Backspace or Enter (read from code)

Alt+0233 types nothing: winit drops the character that comes after Alt is released. A dead key then Backspace deletes
the character before the caret, where Notepad only cancels the accent, and a dead key then Enter or Tab loses the
accent. Read from winit's code, not reproduced on the workstation.

### Off macOS a few panel keys and shortcuts cannot be reached

The save review's Discard (Cmd+Backspace on macOS) has no keyboard key: Ctrl+Backspace deletes a word. Ctrl+1–9
cannot be pressed on French AZERTY, where the digits need Shift. On German and Danish Windows, Ctrl with an AltGr key
(Ctrl+AltGr+ß for `\`) types the character, because winit drops Ctrl while AltGr is down. The changed-on-disk panel's
Ctrl+Enter ("Save anyway") works.

### Ctrl+Q does not quit on Windows

Windows quits with Alt+F4 or the window's close button, as Notepad does. Ctrl+Q quits on Linux.

### What the typed-keys harness does not cover

Keys one at a time only: no overlap, auto-repeat, focus change or Caps Lock, no dead key followed by a non-letter or a
Ctrl chord, no Alt codes or numpad, and no Ctrl shortcut beyond S, Z, Y and Backspace. The red team tried rollover,
held Shift and AltGr, Caps Lock, auto-repeat, the numpad and fast composes by hand on Linux, and they held.

- **macOS:** not run. A hidden window never receives key events, and other layouts would change the person's input
  sources. Typing on macOS rests on the step-2 harness and person checks.
- **Windows:** the hidden run posts key messages into the editor's own message queue, with the modifier state set on
  its thread. The hardware input queue that a keyboard (or `SendInput`) goes through is skipped. It types only the
  layout the session already uses (Danish on the workstation). US, German and French need `--sendinput` at a free
  desktop with the layout chosen (`docs/PERSON-CHECKS.md`).
- **Linux:** X11 on Xvfb only, with only Xlib's own input method (no `XMODIFIERS`). An ibus or fcitx session, and
  XWayland under a Wayland desktop, are person checks. A tester whose `LANG` names a locale their system lacks gets Xlib's
  Latin-1 compose table: some accents then fail to compose, as in other X programs on that system.

## Saving (round 3 red team, 2026-11-12)

### A save that loses a race with a program moving names keeps its folder and says where each version is (R3-8)

macOS and Linux. When another program keeps renaming or swapping names in the file's folder at the moment a save swaps
the new version in, the save puts back whatever came out of the swap; if the name does not hold the new version again
within about a fifth of a second (48 tries), it stops there and moves nothing more. The save review then shows
"*file*: not saved: another file took the place of *path* while it was being saved, and it could not be put back where it
was. Your new version is …; the file as it was before the save is …", where each "…" is found by looking at which file
each name holds: "at *path*", "in the folder `…/.re-save-<16 hex>`, as `<name>.re-tmp`", or "no longer at that name or in
the folder: the other program moved it". When the folder also holds the other program's file, the message says so; when
the other program moved the folder itself, it says that too. The document stays unsaved (amber) in the window, so a
second Save, or Save As, still has the person's version. The `.re-save-<16 hex>` folder beside the file is kept and never
emptied or removed by the editor; every later open of a file in that folder names it (the cut-off-save notice below). The
person moves what they want back by hand in the Finder or Files and removes the folder. Measured only under a program
swapping the two names in a tight loop: on a loaded Mac the swap-race unit test gave up in 8 of 400 runs (each message
right about where both versions were), on Linux `r3_8` in 2 of 900 rounds. Windows replaces with `ReplaceFileW` and has
no swap-back.

### Another program's write during an in-place write is lost (R3-4)

The look again after the copy (round 2, N2) covers the time until the first new byte. The in-place write itself is not
covered: a write by another program that lands while the new bytes go in is written over, or left in a part the new
version no longer has, and the person sees "Saved". Measured by the round-3 red team on a 24 MiB file, release builds: an
ordinary write lost when made within about 3–4 ms before the save returned on macOS and about 7 ms on Windows; a store
through a shared memory map within about 8–14 ms. Larger files widen this in proportion. Only files written in place
are affected (hard-linked files, other owners' files, network volumes, FAT and exFAT, bind mounts); every other save
swaps a new file in.

### The notice about a cut-off save cannot tell a real copy from a planted one (R3-3)

The notice names every real folder called `.re-save-<16 hex>` beside the file, newest first by the folder's own
modification time (three by their path, then how many more), and says that anyone who may change the folder could have
made one. Links, pipes and files with such a name are not named: a save makes only folders. A planted folder that is
newer than the real one comes first. The notice stays in the status bar, after any later message, while the file is open
in the focused pane, and goes when the file is closed; opening it again looks again.

## Saving (round 2 red team, 2026-11-12)

### A killed in-place save can leave a mixed file; the copy is named, and the person restores it by hand (F2; passive since round 2)

Files that cannot be replaced by a new file are written in place: hard-linked files, files of
another owner, files on network volumes, FAT and exFAT (USB sticks), bind mounts. If the editor is
killed, crashes or loses power in the middle of such a write, the file holds part of the new
version. A whole copy of the version before the save is kept, synced before the first new byte
went in, in a hidden folder beside the file: `.re-save-<16 hex>/<name>.re-old`. The editor never
restores, removes or reads that copy, on its own or through a button (the owner's decision after
round 2; `DECISIONS.md`). When a file is opened, the status bar says that a save of a file in that
folder may have been cut off and names the folder (or the folders, newest first), so the person can look in the Finder, Explorer
or Files, put `<name>.re-old` back by hand and remove the folder. Until then other programs see the
mixed bytes. After a power cut, whether the copy survived depends on the volume honouring `fsync`
on the copy and its folders; FAT and exFAT give weaker guarantees.

What the notice cannot tell, since it looks at names and the folder's own metadata only (nothing
inside is opened, so a planted folder, link or pipe can do nothing):

- which file in the folder the copy belongs to: every file opened beside such a folder gets the
  notice;
- a cut-off save from a finished one killed just before its clean-up, or from a swap save killed
  midway (which leaves only its temp file, and a whole version at the name);
- a save still running in another editor window, whose folder looks the same for that moment;
- a folder another program or user made with such a name (round 3, R3-3, above): the notice says
  so.

Such folders stay until the person removes them; the notice comes back at every open until then.
A save's own clean-up of the folder it made removes only what it made, through the folder's held
handle.

### Windows: a version put back after the replace loses its object ID (round 2, N13)

When another program wrote the file between the last check and `ReplaceFileW` (or linked it), the
save puts that program's version back at the name and says "changed on disk". `ReplaceFileW` gave
the file's object ID to the new version, and the put-back cannot give it back (setting an object
ID or a short name needs the restore privilege), so a shortcut or a distributed link-tracking
entry that followed the old object ID loses it; the short (8.3) name may come back different.
Nothing in the file's bytes, attributes or access list changes.

### macOS (and in principle Linux): a hard link made in the instant of the swap can keep the old version (r2_9)

A `link()` by another program that looked the name up just before the swap and adds its new name
just after it gives that name the old version. The save syncs the folder before it counts the
file's links, which an ordinary link finishes within; one held up longer than that sync (a heavily
loaded machine) is not seen. Measured on a loaded Mac: 1 run in 6 of 80 saves before, 0 in 8
after.

## Saving (M7 red team, 2026-11-12)

### `trusted.*` attributes are dropped when a non-root user saves (Linux; F6)

A `trusted.*` extended attribute that root put on a file (overlayfs upper layers, Gluster bricks
and similar use them) cannot be listed by any other user, so the editor cannot see it. When such
a user saves the file and the save puts a new file in its place, the attribute is gone. The
person sees "Saved"; nothing says an attribute was lost. `user.*`, `security.*` and ACLs are
kept. Files written in place (hard-linked, someone else's, network volumes) keep it.

### Writes that land during or after the last check are lost (F1 remainder; DECISIONS M7, limit a; round 2, N2 and N14)

The last check before a save writes reads the whole file and compares its digest with what the
document read (in place: with the copy just made). That read takes about 40–85 ms per 100 MB
(round 2 red team, measured). A write into a part of the file the read has already passed is not
seen by the digest. In place, the length and modification time are read again after the digest,
so an ordinary write in that read is still caught; a store through a memory map, or a write that
puts the modification time back, is not. A write after the check (in the moment before the new
version takes the name or the first new byte goes in, through a handle the other program already
holds, or through a memory map after the save) is lost too: it lands in the old version, which is
no longer at the name, or is written over. The person sees "Saved". Only mandatory locking would
close this, and no editor that saves by rename has it.

### A file this user cannot read is checked by its full `stat` only (F1 remainder)

The bytes of a file this user cannot read (no read permission, after the person answered
"save anyway" or chose to replace it) cannot be compared. Such a file counts as unchanged only
when its whole `stat`, change time included, is as it was; a write through a memory map to it
between the person's answer and the save would not be seen.

### No way through "changed on disk" for an unreadable file on a volume whose times are not trusted (DECISIONS M7, limit e)

On Linux overlayfs, FUSE and network volumes, on Windows, and on macOS volumes other than local
APFS and HFS+, a file this user cannot read that changed since it was opened is asked about again
after every "save anyway": there are no bytes to compare and no change time to trust. The person
sees "Changed on disk" each time. Save As, or making the file readable, gets the text out.

### Saves that are refused with a reason instead of done (F4, F5, F7, F9, F10)

- **The name now leads to another file** (a symbolic link was put at the name, or on any folder on
  the way to it, since the file was opened; F4, round 2 N3; and when the file is gone and a folder
  on the way now is a link or a junction, round 3 R3-2): "… now leads to another file, X …
  Nothing was written". There is no "save anyway": it would write into X. Save As writes elsewhere.
- **Another file with other names was put at the name** (a hard link to another file of the
  person's; round 2 N4): "… is no longer the file you opened … that file also has 1 other name
  beside it: victim.txt … Nothing was written". No "save anyway"; Save As writes elsewhere.
- **The file is held by another program** (a write lease, as Samba and knfsd take; Linux; round 2
  N8): "… is in use by another program … save again in a moment". Nothing was written.
- **A link to a file that no longer exists** (F10): "… is a link to X, which no longer exists. Nothing
  was written"; through a chain of links, "… leads through the link Y to X, which no longer
  exists". Save As writes elsewhere.
- **The person's own file, readable and writable by no one (mode 000) on Linux** (F5): its inode
  flags cannot all be read, so a new file could not be promised to keep them: "cannot read all the
  file flags … make it readable to you (`chmod u+r`) to save it". On macOS the same file is refused
  because its extended attributes cannot be listed.
- **A setgid file whose group this user is not in** (F7): a write clears the bit and only a member
  of the group could set it again: "cannot keep the setgid bit of X … only a member of its group G
  could set it again. Nothing was written".
- **Linux without `/proc`** (a chroot or sandbox; F9): "/proc is not mounted here … Nothing was
  written". New files can still be saved.

### Not tested live

NFS, SMB, CIFS, Ceph and 9P on Linux, and SMB, NFS, WebDAV and macFUSE on macOS (no share was
available; seams stand in); `security.selinux`; power loss as opposed to a process kill.
