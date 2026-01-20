# Evil Portal Switch Helper (WiFi Pineapple Pager)

This helper adds a safe and minimal way to switch between installed Evil Portal portals without editing `etc/init.d/evilportal` or restarting the device.

---

## Overview

Evil Portal normally loads assets from a single portal directory.  
This helper introduces a symlink-based approach:

/root/portals/current -> /root/portals/<portal-name>

The Evil Portal service then references `/root/portals/current` instead of a fixed portal.

A new `switch` subcommand is added to the Evil Portal init script, allowing runtime switching with the `switch_evil_portal` payload.

---

### What This Does

✔ Adds `switch <portal>` to `/etc/init.d/evilportal`  
✔ Uses atomic symlink updates (`ln -sfn`)  
✔ Requires no service restart  
✔ Leaves Evil Portal logic untouched  

---

### Directory Structure

```text
evilportal-switch
├── patches
│   └── evilportal-init-switch.patch
├── payloads
│   └── switch_evil_portal
│       └── payload.sh
└── README.md
```

---

## Requirements

- WiFi Pineapple Pager (OpenWrt 24.10.1)
- **evil_portal** installed using `install_evil_portal` payload
- alternate portals in `/root/portals/`. I am using a copy from [GitHub](https://github.com/kleo/evilportals.git). Ensure that your portals fit the format of the Default portal.
- `patch` installed via `opkg`

---

## Installation

### Step 1 – Apply Init Script Patch

Clone this repo:

```bash
git clone https://github.com/knight-scott/evilportal-switch.git /tmp/evilportal-switch
```

Apply the patch to `/etc/init.d/evilportal`:

```bash
cd /tmp/evilportal-switch
opkg install patch
patch /etc/init.d/evilportal < patches/evilportal-init-switch.patch 
```

---

### Step 2 – Ensure `current` Symlink Exists

```bash
ln -sfn /root/portals/Default /root/portals/current
```

This only needs to be done once.

---

### Step 3 – Install Payload

Copy the payload directory to:

```bash
mv payloads/switch_evil_portal /root/payloads/user/evil_portal/switch_evil_portal
```

---

## Usage (From Pager)

1. Launch the payload **Switch Evil Portal**
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
- Helper & payload by 0x4B

This contribution is intended as an optional enhancement.

---

## Resources

- [WiFi Pineapple Docs](https://docs.hak5.org/)
- [OpenWrt Documentation](https://openwrt.org/docs/start)
- [Hak5 Forums](https://forums.hak5.org/)
- [nftables Wiki](https://wiki.nftables.org/)

---
