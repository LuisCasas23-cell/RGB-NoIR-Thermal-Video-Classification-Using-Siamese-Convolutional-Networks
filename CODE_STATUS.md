# Code Status

## Main Recommended Workflow

| Platform | File | Status | Purpose |
|---|---|---|---|
| Jetson Nano | `code/main/jetson/JETSON_V11_10_ESTABLE.py` | Stable project base | RGB + NoIR + TFmini + YOLO telemetry + preview + original stream |
| Jetson Nano / CUDA Docker | `code/main/jetson/yolo11_v9_server_docker.py` | Compatible | Local YOLO11n inference over `127.0.0.1:5070` |
| Laptop | `code/main/laptop/V19.py` | Dataset V16.19 | MediaPipe, 68 landmarks, 52 blendshapes, NoIR variants, microexpressions, Excel and IQR quality control |
| Jetson Nano | `code/tools/noir_interface_v8_1.py` | Canonical NoIR reference | NOIR FILTER LAB v8.1 |

## Network Compatibility

- Jetson → YOLO service:
  - Port **5070**
  - Header **FRM2**
- Jetson → Laptop:
  - Port **5050**: PING/PONG and compatibility.
  - Port **5051**: preview / telemetry, **FPR8**.
  - Port **5052**: original RGB + NoIR stream, **FORG**.
- `V19.py` listens on ports **5050**, **5051** and **5052**.

## Important

These files correspond to the validated project versions recovered from the development workflow. Functions that depend on CSI, TFmini, CUDA, GPIO/IR hardware, JetPack drivers and physical cameras require the corresponding hardware environment for complete validation.
