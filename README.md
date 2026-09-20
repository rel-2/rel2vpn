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
- [Your account on rel2.com](#your-account-on-rel2com)
  - [Profile, residence and picture](#profile-residence-and-picture)
  - [Security: password and two-factor authentication](#security-password-and-two-factor-authentication)
  - [Subscription](#subscription) · [Usage](#usage) · [Privacy: your data](#privacy-your-data)
  - [Routes you build, and sharing them](#routes-you-build-and-sharing-them)
  - [Device configurations: GenConfig and your keys](#device-configurations-genconfig-and-your-keys)
- [Anchor mode — your home as an exit](#anchor-mode--your-own-exit)
  - [What an Anchor is](#what-an-anchor-is)
  - [Set one up with the app](#set-one-up-with-the-app) · [with the CLI, no app](#set-one-up-with-the-cli-no-app)
  - [Use it: routes and sharing](#use-it-routes-and-sharing)
  - [How it runs, and what keeps it safe](#how-it-runs-and-what-keeps-it-safe)
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
- **Your home as an exit** — make a machine you own an **Anchor** and keep
  leaving the internet from home wherever you travel; share it with the people
  you name. Works from the app or from one CLI command on a Pi or a VDS.
- **Keys made on your side** — device keys are generated in your app, browser or
  service; the server holds only the public half and can never impersonate you.
- **Two-factor sign-in, and your data in your hands** — TOTP with recovery codes,
  a download of everything we hold as one JSON Lines file, and one-click account
  deletion.

---

## Quick start

**Desktop:** download for your system below, open it, **sign in**, press
**Connect**. You will be asked for your administrator password once, so the app
can install the tunnel service. That is the whole setup.

**An Anchor on a headless box (VDS, Raspberry Pi):** unpack `wgclient` for the
machine and run `sudo ./wgclient anchor-setup --name "Pi at home"` — see
[Anchor mode](#anchor-mode--your-own-exit).

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
| CLI `wgclient` — Linux x64 / ARM64 / ARMv7 (32-bit Raspberry Pi OS), macOS Intel/Apple, Windows x64/ARM64, OpenWrt (mipsle) | see the [release assets](../../releases/latest) (`wgclient_<os>_<arch>.tar.gz` / `.zip`) |
| Android · iPhone / iPad | in preparation — the same app on a phone (sign in, Home, Routes, Devices, Account); until the store listings exist, any WireGuard or AmneziaVPN app works with a configuration from **Devices → GenConfig** on rel2.com |

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

- **Sign in** with the email and password of your rel2.com account. If you turned
  on two-factor authentication, the app then asks for the code from your
  authenticator app (or a recovery code).
- **Create an account** — on the sign-in screen choose *Create account*. New
  accounts start as **clients**; pick a plan on [rel2.com](https://rel2.com)
  (Settings → Subscription) to unlock routes. If email is enabled on the server
  you will receive a verification message. On the website, sign-up also asks for
  a **username** (your public handle, `@name` — people share routes with you by
  it; suggested from your name, editable, checked live) and your **country of
  residence** (and state or province where privacy law is regional) — the latter
  decides which privacy law applies to you and nothing else; see
  [Your account on rel2.com](#your-account-on-rel2com).
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
**Devices** view you can add, rename, or remove devices, and press **GenConfig**
on one to get its configuration (WireGuard `.conf`, AmneziaWG `.conf`, or the
AmneziaVPN key with its QR codes) for a standard client, a phone, or a router.
**Every GenConfig is a fresh key**: the key pair is generated in the app on the
spot, only the public half is sent to your account, and the previous configuration
of that device stops working the moment the node learns the new key — so save the
file or scan the code right away. A device can instead **keep its key on the
server** (a switch when you add it, or later in Edit): then **Config** shows its
current configuration again any time with the same key, and a route change
never needs a new key — for a device you cannot easily reach. Turning the switch
on makes a new key (install that configuration once); turning it off makes the
server forget the key while the device keeps working. The machine the app runs
on needs no GenConfig: its own key is made and kept by the tunnel service — or,
for an installation from before September 2026, kept on the server; either
works, and the switch can be changed for it too (it reconnects by itself).
Removing a device frees a slot
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

## Your account on rel2.com

Everything about the account lives on the website under the avatar menu:
**Profile** opens Settings on its first tab; **Settings** has the rest. The
desktop app's own Settings are about this computer only.

### Profile, residence and picture

Your name, your **username** (the public `@handle` other people see and share
routes with — letters, digits and hyphens, 2–32 characters, unique; change it
here any time, the old one becomes free), the language of emails, your
**profile picture** (upload any PNG, JPEG or GIF; it is cropped square and
scaled down), and your **country of residence** — plus the state or province where privacy law is regional (United
States, Canada). Residence is asked at sign-up and can be changed here at any
time; it decides which privacy law we cite for you (GDPR in the EU/EEA, the UK
GDPR, California's CCPA and the other US state acts, PIPEDA and Quebec's Law 25,
and so on) and is used for nothing else. Accounts created through a plan checkout
start without one and are reminded until it is set.

### Security: password and two-factor authentication

Change your password here. **Two-factor authentication** (TOTP — Google
Authenticator, Aegis, 1Password, Authy and the like) adds a 6-digit code to every
sign-in on the website, in the desktop app and in the CLI. Turning it on shows a
QR code and the secret; the first valid code enables it and hands you **eight
recovery codes**, shown once — keep them offline; each signs you in once if the
phone is gone. You can regenerate the codes (with a current code) and turn the
feature off (password plus a code). Lost both the phone and the codes? Write to
support@rel2.com from your account email; after checking who you are an
administrator removes the second factor and you turn it on again. Every sign-in
sends you an email with the IP address it came from; a **security log** of
sign-ups, sign-ins, failed attempts, password and profile changes, device changes
and exports is kept for 400 days and included in your data export.
Two-factor authentication is required before a machine can become an
[Anchor](#anchor-mode--your-own-exit).

### Subscription

Clients see their plan, whether it is active, and the renewal date. **Cancel
subscription** stops the renewal only: the service keeps working until the end
of the period you paid for (or the trial), the tab shows "Active until" and a
**Renew** button. Renew inside that leftover period switches the subscription
straight back to renewing, nothing charged; after the period ends, Renew is a
regular Stripe checkout for your previous plan (the free trial is used up, so the
card is charged right away) and your devices reconnect as they were. **Manage
billing** opens the Stripe portal for cards and invoices; a full **Refund** is
offered for 24 hours after a charge. Packages granted by hand by the operator
have no expiry and nothing to renew.

### Usage

Download and upload over the last 30 days, devices used against your limit, how
many are connected right now, your traffic allowance bar, a per-day chart and a
per-device table — and **Recent connections**: the connection log we keep for
security and abuse handling (device, the public IP you connected from, the
route with its entry and exit, the exit IP, start, length, traffic; brief
reconnects joined into one row). It is kept 180 days and never contains the
sites you visit.

### Privacy: your data

Shows the privacy law that applies to your residence and the rights everyone
gets regardless: access, portability, correction, erasure, and no sale of data.
**Download my data** gives you one JSON Lines file — one record per line —
with your account and residence, sign-in history, the security log, subscription
and invoices, your routes, devices with their public keys, Anchors with their
public-IP history, daily traffic counters and the connection log. Passwords,
private keys, preshared keys and session tokens are never in it. **Delete my
account** removes the account with its devices, configurations, routes, Anchors
and logs immediately and for good — type `delete` to confirm; a subscription
that still renews must be cancelled first, so nothing is charged afterwards.
Invoices stay with the payment provider as tax law requires.

### Routes you build, and sharing them

Multi-hop and Max plans can build their own routes (Routes → Add): a Direct
route through one node, a Double with separate entry and exit, chains, multi-hop
graphs, or the automatic kind that picks and heals itself. Your routes are
private to your account — and you can **Share** any of them by the **username**
of another rel2 account (type the first letters, pick from the list — like
GitHub): the invitee sees it in their app, their traffic leaves through your
route, you see the invitees (by username; nobody's email is shown to anybody)
and can withdraw at any time. An invitee needs no plan: a **guest** account (no
package) can use routes shared with it and gets two devices for them.

### Device configurations: GenConfig and your keys

Device keys are generated on your side — in your browser when you press
**GenConfig** on the website, in the desktop app, or by the tunnel service for
the machine it runs on. Our servers receive only the public key, so nobody at
rel2 can impersonate your device. Because the server never holds the private
key, "download the configuration again" means a **new key**: every GenConfig
shows a fresh configuration once, and the previous one stops working.

That is the default, and the choice is yours per device: **Keep the key on the
server** (a checkbox when you add a device, and in its Edit dialog) makes the
server generate and keep the key instead. Then **Configuration** shows the
current configuration again at any time — same key, nothing changes — and
switching such a device to a route with a different entry server only needs the
configuration re-installed, not a new key: the right choice for a device far
away that you set up once and may need to move between routes later. Turning
the switch on makes a new key right then (install that configuration once);
turning it off deletes the key from our side while the device keeps working,
and its next configuration comes from GenConfig. The data export marks devices
whose key we hold. Devices created before September 2026 simply have the switch
on, and stay that way — nothing to do; the same goes for the machine of an app
or CLI installed before then.

---

## Anchor mode — your own exit

### What an Anchor is

rel² is not only a VPN — it also connects you to your own machines. A computer
you own — the desktop at home, a NAS, a Raspberry Pi, a VPS abroad, a GL.iNet
router — becomes an **Anchor**: a *custom exit* that belongs to you. Wherever
you travel, your phone and laptop keep leaving the internet from that machine,
with its address and its country: local streaming and services that are "for
residents only", banking that wants the IP it knows. You can let named people —
family abroad, a friend — use it too, by invitation. Nothing is offered to
strangers and nothing is sold; this is your connection, for your people.

Plans: **Multi-hop** runs one Anchor for your own devices; **Max** runs two and
shares them with up to five invited people. Two-factor authentication must be on
for the account, and the Anchor machine must not run another VPN. A machine is
either connected to rel2 or an Anchor — never both.

### Set one up with the app

The app's Home has a mode selector, like a router's: **🌐 Client** (connect
this computer to routes) or **⚓ Anchor** (make it a custom exit). On the
machine that should be the exit choose **Anchor**, give it a name (for example
*Home desktop*) and press **Become my Anchor**. The service enrols it with your
account (a few seconds; the screen shows *Enrolling…*, then the state or the
reason it failed). From then on Home *is* the Anchor: its name, the public IP,
how many entry nodes are linked, live connections and traffic — the route list
and Connect are gone, and the tray says *Anchor mode*. **Details** (or
**⋮ → ⚓ Anchor**) shows each link, what the LAN guard refused, and **Remove
from this machine**. Choose **Client** to switch back (a confirmation screen,
then the routes are back); the machine's own internet is unchanged in either
mode. Each link shows *linked* when the Anchor talks to the entry node directly
over UDP, or *linked · relay* when only the TLS fallback gets through — the
relay works everywhere but is much slower; see Troubleshooting.

### Set one up with the CLI, no app

For a VDS you rent, a Raspberry Pi or any headless Linux (systemd) or macOS box,
one command does everything:

```sh
# pick the build: linux_amd64 (VDS), linux_arm64 (Pi 4/5 with a 64-bit OS), linux_armv7 (older Pi, 32-bit OS), darwin_arm64 / darwin_amd64 (Mac)
curl -fsSL https://github.com/rel-2/rel2vpn/releases/latest/download/wgclient_linux_arm64.tar.gz | tar -xz
sudo ./wgclient anchor-setup --name "Pi in Vilnius"     # installs the service, signs in (email, password, 2FA code), enrols, waits for the first sync
wgclient anchor                                          # the state: links, public IP, counters — any time
```

`anchor-setup` is safe to re-run; it skips what is done. For provisioning
without a terminal, pass `--email you@example.com` and pipe the password on
standard input. The service starts with the machine and needs no app
afterwards. To update a headless box later, download the new build and run
`sudo ./wgclient install` again — settings and the Anchor's identity are kept.
`wgclient anchor-off` / `anchor-on` is the switch. OpenWrt routers (mipsle) have
no service manager the installer supports: run `wgclient run` in the foreground
under your own init there.

### Use it: routes and sharing

On the website the Anchor appears on the **Devices** page in the
**⚓ Anchors — custom exits** card (the **?** next to the title explains it):
online or off, its public IP, how many routes exit through it, **LAN access**,
turn on/off, rename, remove. Then build a route with it: **Routes → Add →
Double**, any rel2 node as the entry, and your Anchor under *⚓ Your Anchors* as
the exit. Put your phone or laptop on that route like on any other, and it
leaves the internet from the Anchor. **Share** the route with people by their
**username** — they use their own account (a guest account needs no plan), you
see that they are connected, and you can withdraw the invitation any time. The
route shows *anchor offline* while the Anchor machine is off or asleep, and
comes back by itself.

**LAN access** is off by default: nothing that goes through the Anchor can
reach the network the Anchor is on. Turn it on and *your own devices* on that
route can — invited people never can. Use a machine that is always on (a Pi, a
NAS, a router, a VPS) rather than a laptop; the Anchor's upload speed is the
ceiling for everyone using it.

### How it runs, and what keeps it safe

- **The service is the Anchor.** Everything runs in the tunnel service: it starts
  with the machine, syncs with rel2.com every minute and keeps a link to the entry
  node of every route that exits through it, whether or not the app is open.
  At enrolment it receives its **own** account session (renewed by every sync;
  signing out of the app does not touch it). If that session is ever revoked (you
  changed your password) or expired (the machine was off for a month), opening the
  app's Anchor screen — or running `wgclient anchor-on` — hands it a fresh sign-in
  and it renews itself. The enrolment lives in `conf/anchor.json` in the service's
  store.
- **No ports to open.** The Anchor dials *out* to the rel2 node with a certificate
  of its own (a separate certificate authority — an Anchor can never pass as a
  rel2 node, nor the other way round). The link itself runs two ways at once:
  **direct** — plain WireGuard over UDP to the entry node's link port (the
  Anchor sends first, so the home router's NAT lets the answers back in) — and
  **relay**, the same packets inside the TLS connection, for networks where UDP
  is filtered. Handshakes go out on both; whichever answers carries the traffic,
  and the link moves between them without dropping. Traffic arriving for the
  internet is handled by a user-space network stack inside the service: nothing
  is added to your machine's routing table or firewall, and nothing on the
  machine is reachable through the link.
- **The guard.** Private addresses (your home network), loopback, link-local,
  multicast and outgoing mail (port 25) are refused; LAN access only opens
  private addresses, only for your own devices. Refusals are counted on the
  Anchor screen.
- **Other VPNs.** Anchor mode refuses to start, and pauses, while another VPN
  interface is active on the machine — it would swallow or leak the traffic.
- **What is logged.** The rel2 servers keep the Anchor's public IP and its history
  (a home address changes; an abuse notice cites the one that was current), when
  it last checked in, the invitations you send, and sessions through it in the
  connection log with the Anchor as the exit. Never the sites anyone visits.
- **Responsibility.** Traffic of your devices and of the people you invite leaves
  from your connection and is attributed to it by third parties. Invite people you
  know, keep to your internet provider's terms and local law, and remember you can
  withdraw an invitation or turn the Anchor off at any moment. Section 9 of the
  Terms of Use spells this out.

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
one line of standard input, for provisioning). If two-factor authentication is on
for the account, the 6-digit code from your authenticator app — or one of your
recovery codes — is asked for next. The session is saved on this machine, so
later commands do not ask again. `--url` points at a self-hosted control plane
(default `https://rel2.com`).

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

#### `wgclient anchor`
Anchor mode state: name and id, whether the mode is on and running, the public IP
the rel2 node sees, the account's switch, the last sync, the certificate's expiry,
each link (node, up/down, traffic, the routes it carries) and the counters —
live connections and flows refused by the LAN/port guard. See
[Anchor mode](#anchor-mode--your-own-exit).

#### `wgclient anchor-setup [--name <name>] [--email <email>] [--url <url>] [--no-install]`
Make this Linux or macOS machine an Anchor in one command (needs root): installs
the service if it is missing, signs in if the machine holds no session (email,
password, two-factor code; `--email` plus the password on standard input for
provisioning), enrols the machine with your account, turns Anchor mode on and
waits for the first sync. Re-running skips what is done.

```bash
sudo wgclient anchor-setup --name "Pi in Vilnius"
echo 'my-password' | sudo wgclient anchor-setup --name vds-1 --email you@example.com
```

#### `wgclient anchor-enrol [--name <name>]`
Only the enrolment: register this machine as an Anchor of the account you are
signed in as and turn Anchor mode on (the service must be installed and running).

#### `wgclient anchor-on` / `wgclient anchor-off`
The switch. `anchor-on` refuses while the machine is connected to a route
(disconnect first — a machine is either connected or an Anchor); `anchor-off`
lets it connect to rel2 again. Both also hand the service your current sign-in,
which renews the Anchor's own session when it had expired.

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

**It also comes back by itself.** If the tunnel dies while you are connected —
the link drops, you move to another network, or the computer **sleeps and wakes**
— the client re-establishes it automatically. Waking from sleep and the network
returning are detected as events (on Windows through the system's power and
network notifications), so recovery is prompt rather than waiting on a timer; if
no such signal arrives it still notices a dead tunnel within about two minutes.
It first restores your normal routing so the machine stays online, then reconnects
(retrying 5 s, 10 s, 20 s, 30 s, then every minute), re-resolving the server and
re-pinning it to whatever network you are on now. While this is happening the app
says **Reconnecting…** and the tray shows the same — you are *not* on the VPN
until it says **Connected** again. Only an explicit **Disconnect** (or
`wgclient down`) stops it.

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

Under `conf/` it holds `device.key` (this machine's WireGuard keypair — generated
here by the service; only the public key is ever sent to the server, which
therefore cannot impersonate your device), `tunnel.conf` (the active
WireGuard/AmneziaWG configuration, including that private key), `route.name`
(the current route), a `down` marker when you have disconnected, `hosts.cache` (last-known server addresses, so a
reconnect works even if DNS is briefly unavailable), `conn.log` (the recent
connection log you see in the app), and — on an Anchor — `anchor.json` (the
Anchor's identity key and certificate, its link key, and its own account session
for the sync loop). These are owned by the service account
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
- **Antivirus flags the download (e.g. Defender "Wacatac!ml")** — this is a known
  false positive that some antivirus engines raise for new, unsigned VPN software
  (it creates a network adapter and edits routing). The apps carry no malware; every
  release ships a `SHA256SUMS` you can verify. If your antivirus blocks the download,
  allow the file in its protection history, or add the folder as an exclusion, then
  verify the checksum before running. Code signing to remove the warning is in
  progress.
- **The app says the tunnel service is not installed** — click **Install** (or
  **Update** if you just upgraded). On Windows, make sure you allow the UAC prompt.
- **Windows: no administrator prompt appears when you click Install** — click it
  again. The app has two ways of asking Windows for elevation; if the first cannot
  start the prompt it falls back to the other, and after two such failures it
  switches to the working one for good.
- **It says Reconnecting…** — the network dropped the tunnel and the client is
  bringing it back; you have normal internet meanwhile but are not on the VPN.
  **Reconnect now** forces an attempt; **Stop reconnecting** (or `wgclient down`)
  makes it stay off.
- **Connected but no internet** — disconnect and reconnect; the client restores
  normal routing on disconnect. If a machine is left without internet after a
  crash, a reboot clears any leftover VPN routes.
- **Linux app will not start** — install the GTK 4 / WebKitGTK 6.0 runtime
  libraries listed under [Install — Linux](#install--linux).
- **CLI: `up` says "install the service"** — run `sudo wgclient install` once, or
  `sudo wgclient run` to run the tunnel in the foreground without a service.
- **Lost the authenticator phone** — sign in with one of your recovery codes and
  set two-factor authentication up again in Settings → Security. Lost the codes
  too? Write to support@rel2.com from your account email.
- **Anchor: "another VPN is active"** — disconnect or uninstall the other VPN
  (Tailscale, WireGuard, OpenVPN, a commercial app…) on the Anchor machine; the
  service pauses until it is gone.
- **Anchor: "the account session expired or was revoked"** — you changed your
  password, or the machine was off for over a month. Open the app's Anchor screen,
  or run `wgclient anchor-on`, once: it hands the service a fresh sign-in.
- **Anchor: "could not renew the Anchor's session: … function not found
  user.anchor.session"** (older apps) — the rel2 server was older than the app;
  the Anchor kept working through the app's sign-in. Current apps log this
  silently and retry in the background; once the server is current the service
  gets a session of its own.
- **Anchor: "turn on two-factor authentication first" / "your plan has no Anchor"**
  — Anchors need 2FA on the account and the Multi-hop (one) or Max (two) plan.
- **Slow through my Anchor** — open the Anchor screen in the app (or run
  `wgclient anchor`): a link that says *linked · relay* is going through the TLS
  fallback, which is several times slower than the direct UDP path. Something
  between the Anchor and the entry node blocks UDP (a strict firewall, a
  corporate network, a router that filters "unknown" UDP): allow outgoing UDP
  from the Anchor machine. The direct path also needs a current rel2 node (the
  node tells the Anchor its UDP port at registration). Beyond that, the Anchor's
  own *upload* speed is the ceiling for everyone using it.
- **A route through my Anchor says "anchor offline"** — the Anchor machine is off,
  asleep, or without internet; `wgclient anchor` on it shows the links. Nothing
  leaks meanwhile: traffic for that route waits instead of leaving elsewhere.
- **Windows: "anchor mode is not available in this build"** — the service is older
  than the app; update it (the app offers **Update** when versions differ).
- Still stuck? Email **support@rel2.com** with your OS and, for the CLI, the output
  of `wgclient status` (and `wgclient anchor` on an Anchor).

---

[rel2.com](https://rel2.com) · [Releases](../../releases) · support@rel2.com

© Karagatan LLC. rel² VPN is a product of Karagatan LLC.
