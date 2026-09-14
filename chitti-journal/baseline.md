# Chitti Baseline — 2026-09-14

## Platform
| Item | Value |
|---|---|
| Board | Jetson Orin Nano Super Dev Kit (Eng. Reference), p3767-0005, 8 GB |
| JetPack | 6.2.1 (nvidia-jetpack 6.2.1+b38) |
| Jetson Linux | L4T 36.4.7, GCID 42132812 |
| OS / kernel | Ubuntu 22.04.5 / 5.15.148-tegra |
| CUDA / cuDNN / TensorRT / VPI | 12.6.68 / 9.3.0 / 10.3.0.30 / 3.2.4 |
| ROS 2 | Humble (desktop-full), pub/sub verified |
| Root storage | 128 GB microSD (mmcblk0p1), no NVMe |

## Power mode: before vs after
| Clock ceiling | 15W (mode 0) | MAXN_SUPER (mode 2) |
|---|---|---|
| CPU max | 1,497,600 kHz | 1,728,000 kHz |
| GPU max | 612 MHz | 1020 MHz |
| EMC max | 2133 MHz | 3199 MHz |
| Rated AI perf | ~40 TOPS | ~67 TOPS |
| Memory bandwidth | 68 GB/s | 102 GB/s |

Changed via `sudo nvpmodel -m 2`. No reflash required — the board was already
flashed with the Super config (/etc/nvpmodel.conf -> nvpmodel_p3767_0003_super.conf).

## Sensor inventory
| Device | ID | Node | Notes |
|---|---|---|---|
| OAK-D-Lite-AF | 03e7:2485 | depthai | RVC2, IMX214 + 2x OV7251, BMI270 IMU |
| Xitech USB webcam | 0abd:8050 | /dev/video0 | UVC + mic |
| CH9102 serial -> MCU | 1a86:55d3 | /dev/ttyACM0 | serial 5AB0170529 |
| USB audio out | 0c76:1229 | snd-usb-audio | |
| Lidar | none | | 2D lidar planned before SLAM |

## Known issues
1. OAK-D-Lite enumerates at 480 Mbps (USB 2.0); 10 Gbps bus unused. Suspect cable.
2. All USB devices share one 480 Mbps bus via daisy-chained hubs.
3. Root on microSD; Isaac ROS wants 128+ GB NVMe.
4. OAK-D-Lite has no dot projector -> passive stereo unreliable on blank walls.
5. Intermittent DNS resolution failures on wlP1p1s0 during pip/apt.

## Multi-machine ROS 2 — verified 2026-09-14
- Jetson: ROS 2 Humble desktop-full 0.10.0, Ubuntu 22.04, 192.168.4.36 (wlP1p1s0)
- Mac: ROS 2 Humble desktop 0.10.0 via RoboStack/pixi, macOS 26.6.1 arm64 (M4 Pro), 192.168.4.43
- ROS_DOMAIN_ID=42, default Fast DDS, Wi-Fi multicast discovery working
- Verified: talker on Jetson, `ros2 topic echo /chatter` on Mac, late-join at msg 20
- CycloneDDS unicast fallback installed on Mac but not required
