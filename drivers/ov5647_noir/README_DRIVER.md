# OV5647 NoIR Driver for NVIDIA Jetson Nano

This directory documents the custom driver and Device Tree configuration used to operate a Raspberry Pi Camera Rev. 1.3 with the OmniVision OV5647 NoIR sensor together with an IMX219 RGB camera.

## Development Environment

| Item | Configuration |
|---|---|
| Platform | NVIDIA Jetson Nano Developer Kit |
| JetPack | 4.6.1 |
| L4T | R32.7.1 |
| Kernel | 4.9.253-tegra |
| Architecture | aarch64 |
| RGB camera | IMX219 |
| NoIR camera | OV5647 / Raspberry Pi Camera Rev. 1.3 |

## Final Camera Assignment

| sensor-id | Sensor | Role | Logical node |
|---:|---|---|---|
| 0 | IMX219 | RGB | /dev/video0 |
| 1 | OV5647 | NoIR | /dev/video1 |

Kernel messages observed during the working configuration included:

```text
vi-output, imx219 7-0010
ov5647 8-0036: tegracam sensor driver: ov5647_v2.0.6
vi 54080000.vi: subdev ov5647 8-0036 bound
```

## Files Used During the Port

```text
ov5647.c
ov5647_mode_tbls.h
tegra210-porg-camera-imx219-ov5647.dtsi
ov5647.ko
tegra210-p3448-0000-p3449-0000-b00-imx219-ov5647.dtb
```

### Source-File Integrity Note

The exact original contents of `ov5647.c`, `ov5647_mode_tbls.h` and the customized `.dtsi` were not recovered from the stored project files. Approximate reconstructed versions are intentionally **not** included.

The documentation in this directory records only configuration details that were preserved in the project reports.

## Source Tree Used

Kernel:

```text
~/ov5647_work/Linux_for_Tegra/source/public/kernel_src/kernel/kernel-4.9
```

NVIDIA media drivers:

```text
~/ov5647_work/Linux_for_Tegra/source/public/kernel_src/kernel/nvidia/drivers/media/i2c
```

## Porting Work

The development process included:

- Adapting OV5647 driver calls and structures to NVIDIA kernel 4.9 camera infrastructure.
- Correcting duplicated functions and compilation errors.
- Adjusting gain, `set_mode` and fine-integration-time handling.
- Correcting includes and compilation warnings.
- Preparing the source tree with `Module.symvers`.
- Using `CONFIG_LOCALVERSION="-tegra"`.
- Creating a mixed IMX219 + OV5647 Device Tree.
- Registering the OV5647 at I²C address `0x36`.
- Associating the OV5647 with the second CSI path / `serial_e`.

## Compiled Module

Output:

```text
kernel/nvidia/drivers/media/i2c/ov5647.ko
```

Recorded `vermagic`:

```text
4.9.253-tegra SMP preempt mod_unload modversions aarch64
```

## Device Tree

Board DTS:

```text
hardware/nvidia/platform/t210/porg/kernel-dts/
tegra210-p3448-0000-p3449-0000-b00.dts
```

Dual-IMX219 configuration used as the starting point:

```text
hardware/nvidia/platform/t210/porg/kernel-dts/porg-platforms/
tegra210-porg-camera-rbpcv2-dual-imx219.dtsi
```

Final logical configuration:

- IMX219 retained on `i2c@0` and `serial_a`.
- The second IMX219 from the original dual configuration was disabled.
- OV5647 added on `i2c@1`.
- OV5647 I²C address: `0x36`.
- OV5647 associated with `serial_e`.
- Driver compatible string: `nvidia,ov5647`.

## Custom DTB

```text
/boot/tegra210-p3448-0000-p3449-0000-b00-imx219-ov5647.dtb
```

## Reference NoIR Pipeline

```text
nvarguscamerasrc sensor-id=1
exposurecompensation=1.2
gainrange="1 9"
ispdigitalgainrange="1 1"
exposuretimerange="20000000 33333333" !
video/x-raw(memory:NVMM),width=1920,height=1080,framerate=30/1,format=NV12 !
nvvidconv !
video/x-raw,width=960,height=540,format=BGRx !
videoconvert !
video/x-raw,format=BGR !
appsink drop=true max-buffers=1 sync=false
```

This camera configuration became the basis for **NOIR FILTER LAB v8.1**.
