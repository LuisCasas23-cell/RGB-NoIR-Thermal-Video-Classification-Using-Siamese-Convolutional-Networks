# OV5647 Module Installation Notes

## Target Environment

```text
NVIDIA Jetson Nano
JetPack 4.6.1
L4T R32.7.1
Kernel 4.9.253-tegra
aarch64
```

A kernel module compiled for one kernel version must not be assumed to be compatible with another.

## Install the Compiled Module

The compiled module was:

```text
kernel/nvidia/drivers/media/i2c/ov5647.ko
```

Recorded `vermagic`:

```text
4.9.253-tegra SMP preempt mod_unload modversions aarch64
```

Installation:

```bash
sudo cp ov5647.ko /lib/modules/4.9.253-tegra/extra/ov5647.ko
sudo depmod -a
```

## Automatic Module Loading

File:

```text
/etc/modules-load.d/ov5647.conf
```

Content:

```text
ov5647
```

A repository copy is provided in:

```text
drivers/ov5647_noir/config/ov5647.conf
```

## Custom DTB

The custom test DTB was installed as:

```text
/boot/tegra210-p3448-0000-p3449-0000-b00-imx219-ov5647.dtb
```

Backups of `Image`, the previous DTB and `extlinux.conf` should be preserved before changing boot configuration.

## Alternative extlinux Entry

```text
LABEL ov5647-test
  MENU LABEL IMX219 + OV5647 test
  LINUX /boot/Image
  INITRD /boot/initrd
  FDT /boot/tegra210-p3448-0000-p3449-0000-b00-imx219-ov5647.dtb
  APPEND ${cbootargs} quiet root=/dev/mmcblk0p1 rw rootwait rootfstype=ext4 console=ttyS0,115200n8 console=tty0 fbcon=map:0 net.ifnames=0
```

## Verification

```bash
uname -r
modinfo ov5647
lsmod | grep ov5647
dmesg | grep -i ov5647
```

Expected project messages included:

```text
ov5647 8-0036: tegracam sensor driver: ov5647_v2.0.6
vi 54080000.vi: subdev ov5647 8-0036 bound
```

## Expected Logical Mapping

```text
sensor-id=0 -> IMX219 RGB
sensor-id=1 -> OV5647 NoIR
```

## Kernel Taint Warning

An externally built module may cause the kernel to report that it is "tainted." During this project, that message alone did not indicate a sensor failure.
