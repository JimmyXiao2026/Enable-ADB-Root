# Enable ADB Root

A Magisk module that sets `ro.debuggable=1` to enable direct `adb root` on your device.

## ⚠️ Important (read first)

**This module ONLY works on userdebug / eng builds. It has NO effect on release (user) builds.**

- Whether `adb root` works is decided by the **adbd binary itself** (at compile time), not just the `ro.debuggable` property.
- **userdebug / eng builds**: adbd supports root. Combined with this module (`ro.debuggable=1`), you can directly run `adb root`.
- **user / release builds**: adbd has root disabled at compile time. Changing the property does nothing; `adb root` fails with `adbd cannot run as root in production builds`. Only `adb shell` + `su` (via Magisk) works.

## Files (release folder)

| File | Description |
|------|-------------|
| `module.prop` | Module metadata (id, name, version, author) |
| `system.prop` | Property file, content: `ro.debuggable=1` |

## Install

Place the module into `/data/adb/modules/enable_adb_root/` and reboot:

```bash
adb push module.prop /data/adb/modules/enable_adb_root/
adb push system.prop /data/adb/modules/enable_adb_root/
adb reboot
```

Magisk automatically applies the properties in `system.prop` via `resetprop` when the module is loaded.

Verify after reboot:

```bash
adb root          # should print: restarting adbd as root
adb shell id      # should show: uid=0(root)
```

## Uninstall

Remove the module directory and reboot; the property reverts to the vendor default automatically:

```bash
adb shell "su -c 'rm -rf /data/adb/modules/enable_adb_root'"
adb reboot
```

## How it works

- Magisk applies each line of a module's `system.prop` via `resetprop` at boot.
- This module only sets `ro.debuggable=1`, unlocking adbd's root mode (only if the device is a userdebug/eng build).