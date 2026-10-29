# Glass

**English** | [中文](README.zh-CN.md)

**A terminal built for SSH and AI coding agents.** The core is written in Rust and the UI is drawn
straight on the GPU: it starts fast, scrolls and streams output smoothly, and typing has no lag.
Paste a screenshot into Claude Code or Codex, locally or on an ssh host, and it lands in the prompt
as an image attachment.

Runs on macOS, Windows and Linux. The UI is in English by default; switch it to Chinese in Settings.

> The source code is not public yet and will be open-sourced later. This repository only hosts the
> installers and collects feedback.

![Paste a screenshot and it lands in Claude Code on the server](images/paste.png)

## Download

Get the latest version from [Releases](https://github.com/devfchen/glass-terminal-releases/releases):

| Platform | File | Notes |
|---|---|---|
| macOS 11+ (Apple silicon: M1 / M2 / M3 / M4) | `glass-<version>-macos-arm64.dmg` | Open it and drag Glass into Applications. Notarized by Apple, so it opens with a double-click |
| macOS 11+ (Intel) | `glass-<version>-macos-x86_64.dmg` | Same as above. Not sure which one: Apple menu → "About This Mac"; "Chip: Apple …" takes the one above, "Processor: Intel …" takes this one |
| Windows 10 1809+ / 11 (x64) | `glass-<version>-windows-x64.zip` | Unzip to a single `glass.exe` and run it; no runtime to install. The exe is not code-signed: the first time SmartScreen shows up, click "More info → Run anyway" |
| Windows 11 ARM64 | `glass-<version>-windows-arm64.zip` | Same as above |
| Linux x86_64 (glibc 2.35+, X11 / Wayland) | `glass-<version>-linux-x86_64.tar.gz` | Unpack and install into `~/.local` as the `README.md` inside says; needs a Vulkan driver (Mesa or NVIDIA) |

`SHA256SUMS.txt` holds the checksums of the files.

## Why Glass

![Drop files onto the command line or the file panel of an ssh tab](images/drop-files.png)

![Tabs running Claude Code and Codex on several hosts](images/tabs.png)

![Remote tabs keep running on the server and come back after sleep, a dropped connection or a restart](images/remote-tabs.png)

- **Fast**: written in Rust, drawn on the GPU with GPUI (the framework of the Zed editor), with
  alacritty_terminal as the terminal core. Heavy output and fast scrolling stay smooth, and the
  installers are only two or three MB.
- **Pasting just works**: screenshots, images copied in a browser, files copied in Finder / Explorer
  work as-is in local tabs; in ssh tabs they are uploaded to the host and the remote path is filled
  in. When Claude Code / Codex is in front they show up as `[Image #1]` attachments. Nothing to
  install on the host: it goes through the system's own OpenSSH.
- **Drop files to upload**: drag files into an ssh tab and they go to the terminal's current
  directory, keeping their names, and their remote paths are typed in at the prompt; a name already
  taken gets a new one instead of being overwritten.
- **Sessions that survive**: tabs opened from the Remote menu (the cloud icon) run in tmux on the
  host, so sleep, a dropped Wi-Fi or quitting Glass doesn't stop them; they reconnect on their own
  with the screen and history intact. The Remote menu lists every window on every host with its
  folder and what it is doing (Claude Code's conversation title); one click brings it back. Quit and reopen Glass: windows, tabs, folders and scrollback come back.
- **File panel** (⌘B / Alt+B): browse the remote files in a sidebar, drop files in to upload,
  right-click to download. It follows the terminal's `cd`, shows progress and can be cancelled.
- **Keyboard tuned for AI agents**: kitty keyboard protocol, so Shift+Enter makes a new line and
  Enter submits in Claude Code / Codex; ⌘ / Ctrl-click opens links, including links that programs
  print across several lines.
- **Passwords work, passwordless in one click**: for hosts that only take a password, pasting
  images, dropping files and the file panel ask for it in a dialog and can remember it. Click
  "Passwordless" in the Glass menu to create or reuse a key, install it on the host and add the host
  to the "Remote" menu, so the next time it is one click away.
- **Colour schemes**: built-in Light and Dark (the same colours as the Windows Terminal default),
  plus your own schemes, tuned with a colour picker; the UI turns dark or light with the scheme.
- **Tabs and windows that work**: on Windows / Linux the tabs sit in the title bar (like Windows
  Terminal) and show the current directory; a new tab follows the current connection; tabs can be
  dragged out into their own window and back in; on the mac the tab bar height is adjustable. The
  session manager manages the hosts in `~/.ssh/config`. Chinese input methods (Microsoft Pinyin,
  Sogou and others) compose and pick candidates right at the cursor.

## Requirements

- **macOS**: 11 or later, using the system's own ssh.
- **Where passwords are kept**: passwords you chose to remember are kept in Glass's own
  `passwords.toml` (in the same folder as the settings file). On macOS / Linux only your account can
  read it and it is not encrypted; on Windows it is encrypted with the system's DPAPI. Removing the
  host or the file removes the password.
- **Windows**: 10 1809 or later. Remote tabs and image pasting use the system's own OpenSSH client
  (if it was removed, add "OpenSSH Client" back under "Settings > Optional features"). Hosts that
  only take a password ask for it in a dialog, or use "Passwordless" in the Glass menu to set up key
  login in one click; if your private key has a passphrase, set the "OpenSSH Authentication Agent"
  service to automatic and run `ssh-add` once. Don't run Glass as administrator, or it won't receive
  files dragged from Explorer.
- **Linux**: glibc 2.35 or later (Ubuntu 22.04, Debian 12 and newer), a Vulkan driver, `ssh`.
- **Remote hosts**: nothing to install, a POSIX `sh` is enough (Linux, macOS, BSD); the file panel
  needs SFTP enabled on the host (OpenSSH enables it by default); Remote tabs need tmux 3.0 or newer.

## Feedback

Problems and feature requests are welcome in
[Issues](https://github.com/devfchen/glass-terminal-releases/issues); the OS version, the Glass
version ("About Glass") and a screenshot help track things down faster.

## Third-party components

Glass is built on these open-source projects, used under their own licenses:

| Component | Used for | License |
|---|---|---|
| [GPUI](https://github.com/zed-industries/zed) (Zed Industries) | UI framework, GPU rendering | Apache-2.0, **modified**: Windows input method fixes, moving the window while dragging a tab out, a layout fix for font changes within a line, no pointer flicker when the mac window title changes |
| [alacritty_terminal](https://github.com/alacritty/alacritty) (Alacritty) | Terminal core | Apache-2.0, **modified**: no crash when the kitty keyboard mode stack is full (same as the upstream fix) |
| [vte](https://github.com/alacritty/vte) | Escape sequence parsing | Apache-2.0 / MIT |
| [ConPTY / OpenConsole](https://github.com/microsoft/terminal) (Microsoft, Windows only) | Pseudo terminal | MIT, unmodified |
| About 600 other Rust crates | Image decoding, fonts, serialization, platform APIs and more | MIT, Apache-2.0, BSD, ISC, Zlib, MPL-2.0 and others |

The full list with the license text of every component is in
[THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md), and every installer ships a copy (on the mac in
`Glass.app/Contents/Resources/`, on Windows and Linux in the archive). Glass connects to remote hosts
through the system's own OpenSSH programs; it neither includes nor links OpenSSH.

© 2026 Feng Chen
