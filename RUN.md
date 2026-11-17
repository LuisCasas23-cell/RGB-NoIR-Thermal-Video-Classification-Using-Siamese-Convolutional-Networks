# Running the Project

This document describes the current distributed RGB + NoIR workflow.

## 1. Laptop

Install the laptop dependencies required by your environment, including MediaPipe, OpenCV, NumPy, Pillow and OpenPyXL.

Run:

```powershell
py -3.13 code/main/laptop/V19.py
```

The laptop pipeline handles:

- RGB and NoIR preview reception.
- Original-frame reception.
- MediaPipe facial analysis.
- Structured 68-landmark representation.
- Facial blendshapes.
- NoIR digital variants.
- Microexpression-related temporal analysis.
- Excel / dataset generation.
- Dataset quality control.

### Laptop network ports

| Port | Purpose |
|---:|---|
| 5050 | Compatibility server and PING/PONG |
| 5051 | Preview and telemetry — FPR8 |
| 5052 | Original RGB + NoIR stream — FORG |

Allow Python through the Windows firewall on the private network used by the Jetson Nano and laptop.

---

## 2. Jetson Nano — YOLO11n Service

Run the YOLO service inside the CUDA/Docker environment where Ultralytics is configured:

```bash
python3 code/main/jetson/yolo11_v9_server_docker.py
```

Configuration:

```text
Host: 127.0.0.1
Port: 5070
Model: yolo11n.pt
Protocol: FRM2
```

---

## 3. Jetson Nano — RGB + NoIR Acquisition

In another Jetson terminal:

```bash
python3 code/main/jetson/JETSON_V11_10_ESTABLE.py LAPTOP_IP
```

Example:

```bash
python3 code/main/jetson/JETSON_V11_10_ESTABLE.py 192.168.1.50
```

The laptop IP is stored locally in `laptop_transfer_config.json`.

---

## 4. NOIR FILTER LAB v8.1

For standalone OV5647 NoIR testing:

```bash
python3 code/tools/noir_interface_v8_1.py
```

Reference configuration:

- OV5647 NoIR.
- `sensor-id=1`.
- 960 × 540 display output.
- `exposurecompensation=1.2`.
- Primary maximum gain 9, fallback 8.
- Exposure range approximately 20–33.3 ms.
- Six digital NoIR representations.

---

## 5. Files That Should Not Be Committed

Do not commit:

- Facial datasets or participant captures.
- Generated Excel datasets.
- Local laptop IP configuration.
- Model binaries unless licensing and size are appropriate.
- Private participant information.
- Temporary recovery files.
