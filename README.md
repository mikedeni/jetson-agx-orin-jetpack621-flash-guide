# Flash Jetson AGX Orin Dev Kit — JetPack 6.2.1

Complete guide to flash the **NVIDIA Jetson AGX Orin Developer Kit** with **JetPack 6.2.1** (L4T r36.4.4) using **NVIDIA SDK Manager** on an **Ubuntu 24.04** host.

---

## Table of Contents

- [Requirements](#requirements)
- [Hardware Overview](#hardware-overview)
- [Step 1 — Install SDK Manager on Host](#step-1--install-sdk-manager-on-host)
- [Step 2 — Put Jetson into Force Recovery Mode](#step-2--put-jetson-into-force-recovery-mode)
- [Step 3 — Launch SDK Manager and Configure](#step-3--launch-sdk-manager-and-configure)
- [Step 4 — Flash JetPack OS](#step-4--flash-jetpack-os)
- [Step 5 — Initial Device Setup](#step-5--initial-device-setup)
- [Step 6 — Install SDK Components (Optional)](#step-6--install-sdk-components-optional)
- [Troubleshooting](#troubleshooting)
- [References](#references)

---

## Requirements

### Host Machine

| Item | Requirement |
|------|-------------|
| OS | Ubuntu 24.04 LTS (x86\_64) |
| RAM | 8 GB minimum |
| Disk | 60 GB free (OS image + SDK components) |
| Internet | Required |
| Display | 1440×900 or larger (for SDK Manager GUI) |
| NVIDIA Account | [developer.nvidia.com](https://developer.nvidia.com) — free registration |

### Target Device

| Item | Detail |
|------|--------|
| Board | Jetson AGX Orin Developer Kit (64 GB) |
| JetPack | 6.2.1 (L4T r36.4.4) |
| Storage | NVMe SSD (recommended) or eMMC |

### Cables Required

| Cable | Purpose |
|-------|---------|
| USB Type-A to USB Type-C (included) | Flashing — host ↔ Jetson |
| USB Type-C power supply (included) | Power the Jetson |

---

## Hardware Overview

### Board Layout — Side View 1 (Ports & Connectors)

![Jetson AGX Orin Dev Kit — Side 1](https://developer.download.nvidia.com/embedded/images/jetsonAgxOrin/getting_started/jaodk_labeled_01.png)

### Board Layout — Side View 2 (Buttons & Power)

![Jetson AGX Orin Dev Kit — Side 2](https://developer.download.nvidia.com/embedded/images/jetsonAgxOrin/getting_started/jaodk_labeled_02.png)

> **Critical:** Use the USB-C port **next to the 40-pin connector** (port 10) for flashing — NOT the power USB-C port (port 4).

---

## Step 1 — Install SDK Manager on Host

### 1.1 Download SDK Manager

Go to the official download page and download the latest `.deb` package:

```
https://developer.nvidia.com/sdk-manager
```

Log in with your NVIDIA Developer account to access the download.

### 1.2 Install the Package

```bash
# Navigate to download directory
cd ~/Downloads

# Install (filename may vary by version)
sudo apt install ./sdkmanager_*-*_amd64.deb
```

### 1.3 Verify Installation

```bash
sdkmanager --version
```

### 1.4 Required Host Dependencies

```bash
sudo apt update
sudo apt install -y libgconf-2-4 libcanberra-gtk-module
```

---

## Step 2 — Put Jetson into Force Recovery Mode

Force Recovery Mode lets SDK Manager flash the device.

### Method A — Power Off, Then Enter Recovery

1. If Jetson is powered on, shut it down fully.
2. Connect the **USB Type-A to USB Type-C cable**:
   - Type-A end → host Ubuntu machine
   - Type-C end → **USB-C port next to the 40-pin connector** on Jetson
3. **Hold** the **middle (Force Recovery)** button.
4. While holding, connect the **USB-C power supply** to the power port (above DC jack).
5. Release the Force Recovery button after 2 seconds.

### Method B — Already Powered On

1. **Hold** the **Force Recovery** button.
2. While holding, press and release the **Reset** button.
3. Release the Force Recovery button after 2 seconds.

### Verify Recovery Mode on Host

```bash
lsusb | grep -i nvidia
```

Expected output (device ID may vary):

```
Bus 001 Device 005: ID 0955:7023 NVIDIA Corp. APX
```

> If nothing shows, recheck the cable port and redo the recovery mode steps.

---

## Step 3 — Launch SDK Manager and Configure

### 3.1 Launch

```bash
sdkmanager
```

Log in with your NVIDIA Developer account when prompted.

### 3.2 Step 01 — Development Environment

![SDK Manager Step 01 — Hardware Selection](https://docs.nvidia.com/sdk-manager/_images/jetson-step1-hw.02.png)

| Field | Value |
|-------|-------|
| Product Category | Jetson |
| Hardware Configuration — Target Hardware | Jetson AGX Orin modules |
| Hardware Configuration — Host Machine | **Deselect** (uncheck) |
| Target Operating System | Linux |
| JetPack Version | **JetPack 6.2.1 (rev. 1)** |

Click **Continue**.

---

## Step 4 — Flash JetPack OS

### 4.1 Step 02 — Details and License

![SDK Manager Step 02 — Details and License](https://docs.nvidia.com/sdk-manager/_images/jetson-step3-details.png)

| Section | Action |
|---------|--------|
| Jetson OS | **Select** (check) |
| Jetson SDK Components | Deselect for now (flash OS first) |
| License Agreement | Accept all |

Click **Continue**.

### 4.2 Step 03 — Setup Process

SDK Manager downloads the OS image (~7–12 GB depending on components). Wait for download to complete.

### 4.3 Flash Prompt — Manual Setup

![SDK Manager Flash Dialog — Manual Setup](https://docs.nvidia.com/sdk-manager/_images/jetson-preflash-dialog-manual.png)

A dialog box appears:

1. Select **Manual Setup — Jetson AGX Orin**
2. Choose **Storage Device**:
   - `NVMe` — if NVMe SSD installed (recommended)
   - `eMMC` — use built-in storage
3. Click **Flash**

Flashing takes **5–15 minutes** depending on storage type. Do not disconnect the USB cable or power during this time.

### 4.4 Monitor Progress

![SDK Manager Step 03 — Install Progress](https://docs.nvidia.com/sdk-manager/_images/jetson-install-3-preconfig.png)

Progress bar shows:
- Downloading
- Installing
- Flashing
- Complete

SDK Manager shows **"Installation Finished"** when done.

---

## Step 5 — Initial Device Setup

After flashing, Jetson reboots into Ubuntu setup wizard.

1. Connect display (HDMI or DisplayPort), keyboard, mouse to Jetson.
2. Complete Ubuntu first-run setup:
   - Language
   - Username and password
   - Network (connect to same network as host for SDK components)
3. Note the IP address — needed for SDK component installation.

```bash
# On Jetson, check IP
ip a show eth0
# or
hostname -I
```

---

## Step 6 — Install SDK Components (Optional)

SDK components include CUDA, cuDNN, TensorRT, VPI, and OpenCV.

### 6.1 Via SDK Manager (Recommended)

1. Keep Jetson powered on and connected via the **flashing USB-C cable** (port next to 40-pin connector).
2. Re-open SDK Manager on host.
3. At **Step 01**, select Jetson AGX Orin — same as before.
4. At **Step 02**, **select Jetson SDK Components**, deselect Jetson OS, accept licenses.
5. At **Step 03**, set:
   - **Connection**: USB
   - **IPv4 Address**: `192.168.55.1` (auto-populated, do not change)
6. Click **Install**.

### 6.2 Directly on Jetson (Alternative)

SSH into Jetson or open terminal on device:

```bash
sudo apt update
sudo apt install -y nvidia-jetpack
```

This installs all JetPack components from NVIDIA's apt repository.

### 6.3 Verify Installation

```bash
# Check JetPack version
cat /etc/nv_tegra_release

# Check CUDA
nvcc --version

# Check L4T version
head -1 /etc/nv_tegra_release
```

Expected output for JetPack 6.2.1:

```
# R36 (release), REVISION: 4.4, ...
```

---

## Troubleshooting

### Device Not Detected in Recovery Mode

```bash
lsusb | grep -i nvidia
```

- Check cable is plugged into the **flashing USB-C port** (next to 40-pin connector)
- Try a different USB cable — cheap cables often fail
- Try a direct USB port on host (avoid hubs)
- Retry Force Recovery Mode sequence

### SDK Manager Shows "No Available Releases"

- Ensure SDK Manager is the **latest version** — update via:
  ```bash
  sudo apt install ./sdkmanager_*-*_amd64.deb
  ```
- Log out and back in to NVIDIA account inside SDK Manager

### Flash Fails Mid-Way

1. Close SDK Manager
2. Redo Force Recovery Mode
3. Re-launch SDK Manager and retry — it resumes from the Flash step

### USB Connection Drops During Flash

- Disable USB autosuspend on host:
  ```bash
  sudo bash -c 'echo -1 > /sys/module/usbcore/parameters/autosuspend'
  ```
- Reconnect and retry flash

### Cannot Install SDK Components — Connection Refused

- Verify Jetson and host are on the same subnet
- For USB connection, check USB interface IP:
  ```bash
  ip a show usb0
  # Should show 192.168.55.x
  ```
- Disable host firewall temporarily:
  ```bash
  sudo ufw disable
  ```

---

## References

- [Jetson AGX Orin Dev Kit User Guide — JetPack SDK Setup](https://docs.nvidia.com/jetson/agx-orin-devkit/user-guide/setup_jetpack.html)
- [Jetson AGX Orin Dev Kit User Guide — BSP Installation](https://docs.nvidia.com/jetson/agx-orin-devkit/user-guide/setup_bsp.html)
- [NVIDIA SDK Manager — System Requirements](https://docs.nvidia.com/sdk-manager/system-requirements/index.html)
- [NVIDIA SDK Manager Download](https://developer.nvidia.com/sdk-manager)
- [JetPack SDK Downloads](https://developer.nvidia.com/embedded/jetpack/downloads)
- [JetPack 6.2 SDK Page](https://developer.nvidia.com/embedded/jetpack-sdk-62)

---

## License

MIT
