# Enable ADB Root

开启 `ro.debuggable=1`，让设备支持直接使用 `adb root` 的 Magisk 模块。

## ⚠️ 重要说明（务必先读）

**本模块仅适用于 userdebug / eng 构建的设备，对发行版（user / release 构建）无效。**

- `adb root` 是否可用，由 **adbd 二进制本身**（编译期）决定，而不只是 `ro.debuggable` 属性。
- **userdebug / eng 构建**：adbd 支持 root，配合本模块把 `ro.debuggable` 改为 1 后，可直接 `adb root`。
- **user / release（发行版）构建**：adbd 编译时已禁用 root 能力，改属性无效，`adb root` 会报 `adbd cannot run as root in production builds`，只能靠 Magisk 的 `su`（`adb shell` + `su`）。

## 文件说明（release 文件夹）

| 文件 | 说明 |
|------|------|
| `module.prop` | 模块信息（id、名称、版本、作者） |
| `system.prop` | 属性文件，内容为 `ro.debuggable=1` |

## 安装

将模块放到 `/data/adb/modules/enable_adb_root/`，然后重启设备：

```bash
adb push module.prop /data/adb/modules/enable_adb_root/
adb push system.prop /data/adb/modules/enable_adb_root/
adb reboot
```

Magisk 加载模块时会自动通过 `resetprop` 应用 `system.prop` 里的属性。

重启后验证：

```bash
adb root          # 应显示 restarting adbd as root
adb shell id      # 应显示 uid=0(root)
```

## 卸载

删除模块目录后重启即可，属性会自动恢复厂商默认值，无需额外清理：

```bash
adb shell "su -c 'rm -rf /data/adb/modules/enable_adb_root'"
adb reboot
```

## 原理

- Magisk 模块的 `system.prop` 会在启动时被 `resetprop` 逐行应用。
- 本模块仅将 `ro.debuggable` 设为 `1`，解锁 adbd 的 root 模式（前提是设备为 userdebug/eng 构建）。