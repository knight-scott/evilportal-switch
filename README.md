# Evil Portal Switch Helper (WiFi Pineapple Pager)

This helper adds a safe and minimal way to switch between installed Evil Portal portals without editing `etc/init.d/evilportal` or restarting the device. 

> **Note:** This function is currently proposed in a [pull request](https://github.com/hak5/wifipineapplepager-payloads/pull/213) to the official [`wifipineapplepager-payloads`](https://github.com/hak5/wifipineapplepager-payloads.git) repository. Until the PR is merged (or if it is declined), this repo provides an optional, user-installable alternative.

---

## Overview

Evil Portal normally loads assets from a single portal directory.  
This helper introduces a symlink-based approach:

`/root/portals/current -> /root/portals/<portal-name>`

The Evil Portal service then references `/root/portals/current` instead of a fixed portal.

A new `switch` subcommand is added to the Evil Portal init script, allowing runtime switching with the `switch_evil_portal` payload.

The `install_evil_portal` & `default_portal` payloads are adjusted for symlink creation and usage

---

### What This Does

✔ Updates `install_evil_portal/payload.sh` with helper to switch portals
✔ Adds `switch_evil_portal` payload  
✔ Uses atomic symlink updates (`ln -sfn`)
✔ Updates `default_portal` to use symlink
✔ Requires no service restart  
✔ Leaves Evil Portal logic untouched

---

### Directory Structure

```text
evilportal-switch
├── payloads
│   └── default_portal
│   │   └── payload.sh
│   └── install_evil_portal
│   │   └── payload.sh
│   └── switch_evil_portal
│       └── payload.sh
└── README.md
```

---

## Requirements

- WiFi Pineapple Pager (OpenWrt 24.10.1)
- **evil_portal** installed. Payloads such as `start`, `stop`, `enable` and `disable` should be installed from the official repository.
- alternate portals in `/root/portals/`. I am using a copy from [GitHub](https://github.com/kleo/evilportals.git). Ensure that your portals fit the format of the Default portal.

---

## Installation

### Step 1 – Clone Repository

Clone this repo to a workstation computer or to the `/tmp` directory of the WiFi Pineapple Pager directly:

```bash
git clone https://github.com/knight-scott/evilportal-switch.git /tmp/evilportal-switch
```

- If cloning to your computer first, you can use `scp` to transfer the files. See the [documentation](https://docs.hak5.org/wifi-pineapple-pager/payloads/installing-payloads/) for more information.

Move payloads to the appropriate `evil_portal` directory:

```bash
mv /tmp/evilportal-switch/payloads/default_portal/payload.sh /root/payloads/user/evil_portal/default_portal/
mv /tmp/evilportal-switch/payloads/install_evil_portal/payload.sh /root/payloads/user/evil_portal/install_evil_portal/
mv /tmp/evilportal-switch/payloads/switch_evil_portal /root/payloads/user/evil_portal/
rm -rf /tmp/evilportal-switch/
```

### Step 2 – Run Install Payload

From the Evil Portal payload directory, launch the `install_evil_portal` payload.

## Portal Switch Usage (From Pager)

1. Launch the payload `switch_evil_portal`
2. Select a portal number using the NUMBER_PICKER
3. The symlink updates instantly
4. Captive portal content changes immediately

## Usage (From Command Line)

```bash
/etc/init.d/evilportal switch NEW_PORTAL
```

### Example

```bash
/etc/init.d/evilportal switch google-login
```

---

## Logging

Portal switches are logged via syslog:

```bash
logread | grep evilportal
```

---

## Compatibility Notes

- Requires Evil Portal to reference `/root/portals/current`
- Compatible with isolated or bridged network configurations
- Works without restarting Evil Portal services

---

## Author

- Evil Portal originally developed by newbi3 for WiFi Pineapple Mark VII
- Adapted for WiFi Pineapple Pager by PentestPlaybook
- Swittch Helper & payload by 0x4B

This contribution is intended as an optional enhancement.

---

## Resources

- [WiFi Pineapple Docs](https://docs.hak5.org/)
- [OpenWrt Documentation](https://openwrt.org/docs/start)
- [Hak5 Forums](https://forums.hak5.org/)
- [nftables Wiki](https://wiki.nftables.org/)

---
