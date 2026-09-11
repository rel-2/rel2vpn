# rel² VPN

**Stay connected worldwide.**

rel² VPN is a WireGuard-based VPN with apps for **macOS, Windows and Linux** and a
headless **command-line client** for servers and routers. You sign in with your
account, pick a route, and connect. Every route speaks three transports —
standard **WireGuard**, obfuscated **AmneziaWG**, and a **TLS tunnel** — and the
client tries them in order until one gets through, so it keeps working on hotel
Wi‑Fi, mobile networks, and countries that block plain VPNs.

**Website & account: [rel2.com](https://rel2.com)** · **Support: support@rel2.com**

> This repository hosts the **official release binaries** (see
> [Releases](../../releases)) and this manual. The application source is private.
> Phones (iOS / Android) and routers do **not** need anything from here — add the
> device in your account and scan its QR code or import its `.conf` into the
> standard WireGuard or AmneziaVPN app.

---

## Contents

- [What you get](#what-you-get)
- [Quick start](#quick-start)
- [Downloads](#downloads)
- [The desktop app](#the-desktop-app)
  - [Install — macOS](#install--macos) · [Windows](#install--windows) · [Linux](#install--linux)
  - [Create an account or sign in](#create-an-account-or-sign-in)
  - [Connect and switch routes](#connect-and-switch-routes)
  - [Devices](#devices)
  - [Settings](#settings)
  - [The tunnel service: install, re-install, uninstall](#the-tunnel-service-install-re-install-uninstall)
- [The command-line client — wgclient](#the-command-line-client--wgclient)
  - [Set it up](#set-it-up)
  - [Command reference](#command-reference)
  - [Offline / manual configuration](#offline--manual-configuration)
- [How it works](#how-it-works)
  - [The virtual network interface](#the-virtual-network-interface)
  - [The always-connect transport ladder](#the-always-connect-transport-ladder)
  - [The privileged service and the loopback control channel](#the-privileged-service-and-the-loopback-control-channel)
  - [Full-tunnel routing](#full-tunnel-routing)
  - [Where your data is stored](#where-your-data-is-stored)
- [Verify what you run](#verify-what-you-run)
- [Troubleshooting](#troubleshooting)

---

## What you get

- **One app, every desktop** — the same experience on macOS, Windows and Linux.
- **Always connects** — WireGuard, AmneziaWG and TLS are tried automatically; if
  one is blocked, the next gets through.
- **Choose where you appear online** — direct routes, double routes (enter in one
  country, exit in another), chains of three or more servers, and multi-hop
  graphs. Higher plans can build their own routes.
- **Account-first** — routes and per-device configuration come from your account.
  There are no files to copy by hand; sign in and connect.
- **No privileges after setup** — the tunnel runs in a small background service.
  The app and CLI drive it and never need admin rights once it is installed.
- **Survives the window closing** — close the app and the tunnel keeps running.
- **A real CLI** — `wgclient` does everything the app does, for servers, headless
  boxes and OpenWrt / GL.iNet routers.

---

## Quick start

**Desktop:** download for your system below, open it, **sign in**, press
**Connect**. You will be asked for your administrator password once, so the app
can install the tunnel service. That is the whole setup.

**Command line:**

```bash
sudo wgclient install                # install the tunnel service (once)
wgclient login you@example.com       # sign in (password is prompted)
wgclient routes                      # see what your plan allows
wgclient up "US → Lithuania"         # connect this machine to a route
wgclient status                      # transport, handshake, traffic
wgclient down                        # disconnect
```

---

## Downloads

Latest release, always at these links:

| Platform | Download |
|---|---|
| macOS (Apple silicon + Intel, one universal app) | [rel2-vpn_macos_universal.zip](../../releases/latest/download/rel2-vpn_macos_universal.zip) |
| Windows 10 / 11 — x64 | [rel2-vpn_windows_amd64.zip](../../releases/latest/download/rel2-vpn_windows_amd64.zip) |
| Windows 11 — ARM64 | [rel2-vpn_windows_arm64.zip](../../releases/latest/download/rel2-vpn_windows_arm64.zip) |
| Linux — x64 | [rel2-vpn_linux_amd64.tar.gz](../../releases/latest/download/rel2-vpn_linux_amd64.tar.gz) |
| Linux — ARM64 | [rel2-vpn_linux_arm64.tar.gz](../../releases/latest/download/rel2-vpn_linux_arm64.tar.gz) |
| CLI `wgclient` — Linux x64/ARM64, macOS Intel/Apple, Windows x64/ARM64, OpenWrt (mipsle) | see the [release assets](../../releases/latest) (`wgclient_<os>_<arch>.tar.gz` / `.zip`) |

Every release ships a `SHA256SUMS` file — see [Verify what you run](#verify-what-you-run).

---

## The desktop app

The app lives in the **menu bar (macOS)** or **system tray (Windows / Linux)**.
Its window signs you in, lists the routes your plan allows, and manages your
devices and settings; the tray icon toggles **Connect / Disconnect** and shows the
live state. The tunnel itself runs in a small **system service** the app installs
the first time you connect (it asks for administrator rights once). After that the
app never needs privileges, and the connection keeps running even if you quit the
window.

### Install — macOS

1. Unzip and drag **rel2 VPN.app** to *Applications*, then open it.
2. The build is not yet notarized with Apple, so the first launch is blocked with
   *"cannot be opened"*. Open **System Settings → Privacy & Security**, scroll
   down, and click **Open Anyway** once. (Or in Terminal:
   `xattr -d com.apple.quarantine "/Applications/rel2 VPN.app"`.)
3. Sign in, press **Connect**. macOS asks for your password once so the app can
   install the tunnel service.

### Install — Windows

1. Unzip and keep **rel2 VPN.exe** somewhere permanent (for example
   `C:\Program Files\rel2 VPN\`, or your user folder) — it is portable, there is
   no installer.
2. Run it. SmartScreen may show *"Windows protected your PC"* for an unknown
   publisher: click **More info → Run anyway**.
3. Sign in, press **Connect**. A **UAC** prompt installs the tunnel service. The
   Windows network driver (wintun) is bundled inside the app, so nothing else is
   needed.

### Install — Linux

```bash
tar xzf rel2-vpn_linux_amd64.tar.gz
sudo install -m 755 rel2-vpn /usr/local/bin/rel2-vpn
rel2-vpn
```

The app uses **GTK 4 + WebKitGTK 6.0**. Install the runtime libraries if they are
missing:

- Debian / Ubuntu: `sudo apt install libwebkitgtk-6.0-4 libgtk-4-1`
- Fedora: `sudo dnf install webkitgtk6.0 gtk4`
- Arch: `sudo pacman -S webkitgtk-6.0 gtk4`

On the first **Connect**, the tunnel service is installed through `pkexec` (a
graphical password prompt), or with `sudo` if you started the app from a terminal.

### Create an account or sign in

- **Sign in** with the email and password of your rel2.com account.
- **Create an account** — on the sign-in screen choose *Create account*. New
  accounts start as **clients**; pick a plan on [rel2.com](https://rel2.com) or in
  the app's Account tab to unlock routes. If email is enabled on the server you
  will receive a verification message.
- Forgot your password? Use **Forgot password** on [rel2.com](https://rel2.com).

Your session is stored on this computer so you stay signed in between launches.

### Connect and switch routes

- The **Routes** view shows the routes your plan allows, grouped by tier. Press
  **Connect** on one, or use **Quick connect** on the home screen.
- The tray icon and the home screen show the live state: **Connecting**,
  **Connected** (with the transport and traffic), or **Not connected**.
- **Switching routes** is one click. Switching to a route that enters at the *same*
  server is live — the connection keeps working and simply starts exiting through
  the new path. Switching to a route with a *different* entry server re-establishes
  the tunnel.

### Devices

Each machine is a **device** on your account. The app creates one for this machine
automatically the first time you connect (named after its hostname) and reuses it
afterwards — one machine is one device, however many routes you use. In the
**Devices** view you can add, rename, or remove devices, and download a device's
configuration (WireGuard `.conf`, AmneziaWG `.conf`, or the AmneziaVPN key) to use
it in a standard client, on a phone, or on a router. Removing a device frees a slot
under your plan's device limit.

### Settings

- **DNS inside the tunnel** — use the route's default resolver, a preset
  (Cloudflare, Quad9, Google, AdGuard), or your own list.
- **Launch at login** — start the app automatically when you sign in to the
  computer (a login item on macOS, an autostart entry on Linux).
- **Reconnect on start** — bring the last route back up when the service starts.
- **Backup phrase** — the recovery phrase for the tunnel service on this machine.
- **Account** — your email, current plan, and **Sign out**. Clients can open the
  website to change plan.

### The tunnel service: install, re-install, uninstall

The tunnel runs as a background **system service** so it can create the virtual
network interface and change routing — things that require administrator rights.
The app manages it for you:

- **Install** — happens automatically on your first **Connect**, behind one
  administrator prompt. You can also trigger it from the *"tunnel service is not
  installed"* notice.
- **Re-install / update** — when you install a newer app, it offers to **Update**
  the service (it replaces and restarts the service in place; your routes, devices
  and settings are kept). This is also the fix if the service ever stops
  responding.
- **Uninstall** — remove the service from **Settings**. Your account session stays
  on the machine unless you also sign out.

You never edit service files by hand; the equivalent CLI commands are
[`wgclient install`](#command-reference) and `wgclient uninstall`.

---

## The command-line client — wgclient

`wgclient` is the full client without a UI: a single static binary with no
dependencies, for servers, headless boxes, and OpenWrt / GL.iNet routers (the
AX1800 family uses the `linux_mipsle` build). It is **account-first** — it talks to
the same account API the app and website use, so routes and per-device
configuration come from your account; there are no files to shuffle.

The desktop app already contains this daemon, so on a machine with the app you do
**not** also install `wgclient` — they are the same tunnel.

### Set it up

```bash
tar xzf wgclient_linux_amd64.tar.gz
sudo install -m 755 wgclient /usr/local/bin/wgclient

sudo wgclient install                # register the tunnel service (systemd / launchd / Windows SCM)
wgclient login you@example.com       # sign in; the password is prompted
wgclient routes                      # what your plan allows
wgclient up "US → Lithuania"         # create this machine's device and connect
wgclient status                      # transport, handshake, traffic
```

`install` needs administrator / root once. After that, `login`, `up`, `down`,
`status`, `routes` and `devices` run as your normal user — they drive the service
over a local channel and never need `sudo`.

### Command reference

Run `wgclient <command> --help` for the exact options on your version.

#### `wgclient login <email> [--url <url>]`
Sign in to your rel2 VPN account. The password is read from the terminal (or from
one line of standard input, for provisioning). The session is saved on this
machine, so later commands do not ask again. `--url` points at a self-hosted
control plane (default `https://rel2.com`).

```bash
wgclient login you@example.com
echo 'my-password' | wgclient login you@example.com   # non-interactive
```

#### `wgclient logout`
Sign out and forget the stored session on this machine.

#### `wgclient routes`
List the routes your plan can use — name, kind (direct / double / chain / multi-hop)
and exit location.

#### `wgclient devices`
List the devices on your account and their status, and show which one is *this*
machine.

#### `wgclient up <route> [--device <name>] [--transport auto|udp|tcp]`
Connect this machine to a route. It finds or creates this machine's device (named
after the hostname, or `--device`), pulls its configuration from your account, and
hands it to the tunnel service — no `sudo` once the service is installed.
`--transport` forces a carrier instead of the automatic ladder: `udp` (WireGuard /
AmneziaWG) or `tcp` (the TLS stream fallback).

```bash
wgclient up "US → Lithuania"
wgclient up "Direct — Las Vegas" --device my-laptop --transport tcp
```

#### `wgclient down`
Disconnect the tunnel and remember the choice (it stays down across reboots until
you connect again).

#### `wgclient status`
Show the running tunnel: version, interface name, active transport, route,
endpoint, your tunnel address, the time since the last handshake, and traffic
counters. When disconnected it prints the last route so you can reconnect quickly.

#### `wgclient install [--no-start] [--idle] [--token-from <file>]`
Install the tunnel as a **system service** (needs administrator / root). The
service is what holds the virtual interface and routing; installing it lets every
other command run unprivileged. `--no-start` installs without starting; `--idle`
starts the service disconnected. `--token-from` is used by the desktop app to share
its control token with the service.

#### `wgclient uninstall [--purge]`
Remove the system service (needs administrator / root). `--purge` also deletes the
stored tunnel configuration and state.

#### `wgclient authorize [--token-to <file>] [--all-users]`
On a shared computer, authorize another user to talk to the already-installed
service (needs administrator / root). `--token-to` writes the service's control
token to a file you name (owned by that user); `--all-users` makes it readable by
every user of the machine.

#### `wgclient import <source>`
Save a tunnel configuration by hand — the **offline fallback** when you cannot sign
in. `<source>` is a WireGuard/AmneziaWG `.conf` file, a `vpn://…` AmneziaVPN key, or
`-` to read from standard input. Prefer `login` + `up`, which keep the config in
sync with your account.

#### `wgclient run`
Run the tunnel in the **foreground** (needs root / `sudo`; Ctrl-C disconnects).
Normally the installed service does this for you; `run` is for a machine where you
do not want a permanent service.

#### `wgclient version`
Print the client version and build.

> On Windows there is also an internal `wgclient service` command — it is how the
> Windows Service Manager starts the daemon and is not meant to be run by hand.

### Offline / manual configuration

You can run the tunnel entirely from a configuration file, without signing in —
useful for provisioning or for a router that only takes a `.conf`:

```bash
sudo wgclient install
wgclient import /path/to/route.conf     # or: wgclient import - < route.conf
wgclient status
```

A configuration imported this way does not auto-update from your account; re-import
it when your route or keys change.

---

## How it works

### The virtual network interface

When you connect, the client creates a **virtual network interface** (a TUN
device) — a network card that exists in software. The operating system routes your
traffic into it; the client encrypts each packet with WireGuard (or the
obfuscated AmneziaWG variant) and sends the encrypted result to the entry server;
replies come back the same way and are decrypted. The virtual interface is what
lets the OS treat "the VPN" like any other network and send some or all of your
traffic through it.

The interface is named per platform:

| OS | Interface | Technology |
|---|---|---|
| macOS | `utun` (a `utunN` is assigned) | in-process WireGuard/AmneziaWG over the system `utun` driver |
| Linux | `wgc0` | in-process WireGuard/AmneziaWG over `/dev/net/tun` |
| Windows | `wgc0` ("WireGuard Tunnel") | in-process WireGuard/AmneziaWG over the bundled **wintun** driver |

The WireGuard/AmneziaWG engine runs in **user space** inside the client, so no
kernel module or extra driver needs to be installed (on Windows the signed wintun
driver ships inside the app).

### The always-connect transport ladder

Every route can carry traffic three ways, and the client tries them in order until
one completes a handshake:

1. **WireGuard over UDP** — the fast default.
2. **AmneziaWG over UDP** — WireGuard wrapped in traffic obfuscation, so
   deep-packet-inspection equipment does not recognise it as a VPN.
3. **TLS over TCP** — a tunnel that looks like ordinary encrypted web traffic, for
   networks that block or throttle UDP.

If a network blocks one, the client falls back to the next automatically. You can
pin a specific carrier with `wgclient up --transport …` or in the app.

### The privileged service and the loopback control channel

Creating the virtual interface and changing the system routing table needs
administrator / root. Rather than run the whole app with those privileges, the
client splits in two:

- a **service** that holds the interface and routing (installed once), and
- the **app / CLI**, which runs as your normal user and tells the service what to
  do over a **local-only control channel** (loopback `127.0.0.1`, authenticated by
  a token created at install). Nothing on this channel is exposed to the network.

That is why, after the one-time install, connecting and switching routes never ask
for a password. The service is a **systemd** unit on Linux, a **LaunchDaemon** on
macOS, and a **Service Control Manager** service (running as LocalSystem) on
Windows.

### Full-tunnel routing

To send *all* your traffic through the VPN, the client adds two routes
(`0.0.0.0/1` and `128.0.0.0/1`) that take precedence over the normal default route
without deleting it, and it **pins the VPN server's own address to your real
network card** so the encrypted packets can still leave the machine. On Windows it
additionally binds the tunnel's socket to the physical adapter. When you
disconnect — or if no transport can connect — those routes are removed so your
normal internet is restored.

### Where your data is stored

The client keeps two small stores, and never sends either to anyone but your
control plane:

**Your account session** (unprivileged, per user):

| OS | Location |
|---|---|
| macOS / Linux | `~/.wgclient/` |
| Windows | `%USERPROFILE%\.wgclient\` |

It holds `conf/wgclient.yaml` (the control-plane URL, your sign-in token, your
email, plan and role, the current device id and route name, and your settings such
as DNS) and `conf/service.token` (the local control token). Files are private to
your user (`0600`).

**The tunnel service** (privileged):

| OS | Location |
|---|---|
| Linux / macOS | `/var/lib/wgclient/` (recorded in `/etc/wgclient/home`) |
| Windows | `%ProgramData%\wgclient\` |

Under `conf/` it holds `tunnel.conf` (the active WireGuard/AmneziaWG configuration,
including this device's private key), `route.name` (the current route), a `down`
marker when you have disconnected, `hosts.cache` (last-known server addresses, so a
reconnect works even if DNS is briefly unavailable), and `conn.log` (the recent
connection log you see in the app). These are owned by the service account
(root / LocalSystem) and readable only by it.

`wgclient uninstall` removes the service and its binary; add `--purge` to also
delete the tunnel store. Signing out (`wgclient logout` or **Sign out** in the app)
clears the account session.

---

## Verify what you run

Every release ships a `SHA256SUMS` file covering all downloads. Check a download
against it before running it:

```bash
sha256sum -c SHA256SUMS --ignore-missing              # Linux
shasum -a 256 -c SHA256SUMS 2>/dev/null               # macOS
certutil -hashfile rel2-vpn_windows_amd64.zip SHA256  # Windows — compare with SHA256SUMS
```

A checksum that does not match is a file you do not run. The apps are not yet
code-signed for Apple notarization or Windows Authenticode, which is why the first
launch asks you to confirm an unknown publisher — verifying the checksum is the way
to be sure of what you downloaded.

---

## Troubleshooting

- **"Cannot be opened" (macOS) / "Windows protected your PC"** — the build is not
  code-signed yet. Use *Open Anyway* (macOS) or *More info → Run anyway* (Windows);
  see the install steps above.
- **The app says the tunnel service is not installed** — click **Install** (or
  **Update** if you just upgraded). On Windows, make sure you allow the UAC prompt.
- **Connected but no internet** — disconnect and reconnect; the client restores
  normal routing on disconnect. If a machine is left without internet after a
  crash, a reboot clears any leftover VPN routes.
- **Linux app will not start** — install the GTK 4 / WebKitGTK 6.0 runtime
  libraries listed under [Install — Linux](#install--linux).
- **CLI: `up` says "install the service"** — run `sudo wgclient install` once, or
  `sudo wgclient run` to run the tunnel in the foreground without a service.
- Still stuck? Email **support@rel2.com** with your OS and, for the CLI, the output
  of `wgclient status`.

---

[rel2.com](https://rel2.com) · [Releases](../../releases) · support@rel2.com

© Karagatan LLC. rel² VPN is a product of Karagatan LLC.
