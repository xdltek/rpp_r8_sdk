![XDL Logo](pic/logo_color_horizontal.png)

**Technology Enterprise Edition** | AI-Optimized | Production-Ready

## Document Purpose

This document is an **official customer-delivery SOP** for the XDL SDK (RPP-based AI acceleration). It is intended for **end customers, system integrators, and solution partners** to independently complete environment setup, SDK installation, and functional validation of **RPP AI acceleration hardware** on x86_64 platforms.

This guide focuses on **reproducibility, stability, and first-time bring-up success**, and reflects the **recommended installation flow validated by XDL Technical Support Team**.

---

## Contents

> **Audience**: System administrators, solution engineers, and AI application developers  
> **Delivery Type**: Customer-facing (Production / Evaluation)  
> **Prerequisite Knowledge**: Linux command line, basic CMake, Python/Conda

**SDK Package**: `xdl_sw_v1.7.2.5_x86_Ubuntu.run`  
**SDK Version**: V1.7.2.5  
**Target Platform**: Ubuntu x86_64

---

## 1. System Requirements

Ensure the target system meets the following minimum requirements prior to installation:

| Component          | Requirement                                      |
| ------------------ | ------------------------------------------------ |
| Operating System   | Ubuntu 20.04 LTS (x86_64)                        |
| System Memory      | ≥ 16 GB                                          |
| CPU Architecture   | x86_64 (PCIe® Gen3 Ready)                        |
| Compiler           | GCC 9.4.0 (C/C++)                                |
| CMake              | ≥ 3.16 (Ubuntu 20.04 default: 3.16.3)            |
| Python             | 3.8+ (python3-pip)                               |
| Build Tools        | build-essential, linux-headers-generic            |
| Kernel Headers     | linux-headers-$(uname -r) for driver compilation  |

> **Note**: Systems that do not meet these requirements may encounter installation or runtime issues.

### Auto-Installed Prerequisites

The SDK installer automatically checks and installs the following system packages via `apt`:

- **Build**: build-essential, bison, flex, cmake
- **Libraries**: libjsoncpp-dev, libboost-all-dev, libspdlog-dev, libncurses-dev, libsqlite3-dev, libssl-dev, net-tools, python3-pip
- **Video/Media**: libopencv-dev, ffmpeg, libavcodec-dev, libavformat-dev, libavutil-dev, libswscale-dev, libjpeg-dev, libpng-dev, libyaml-cpp-dev, gfortran
- **GStreamer-related**: libnice-dev, libglib2.0-dev, libgtk2.0-dev, libv4l-dev, libwayland-dev, libx11-dev

Ensure the system has internet access during installation so these dependencies can be fetched if missing.

---

## 2. SDK Installation

> **Objective**: Deploy a clean and supported XDL SDK runtime and development environment on the target system.

### 2.1 Prerequisites

> **Important**: XDL strongly recommends performing SDK installation on a **clean system image** or ensuring that any previous RPP/XDL SDK versions are fully removed before proceeding.

The XDL SDK is distributed as a self-extracting installer package that includes:

- High-performance RPP drivers (PCIe® optimized)
- Runtime libraries (OpenRT, MPPSDK, RPP tool chains)
- Development headers and build tools
- GStreamer 1.18 (media pipeline)
- OpenCV hardware decoder
- RPP kernel driver (built against current kernel)

### 2.2 Step 1: Obtain the SDK Installer

Download or copy the SDK installer to the target machine:

```
xdl_sw_v1.7.2.5_x86_Ubuntu.run
```

Example location:

```
~/xdl_sdk/xdl_sw_v1.7.2.5_x86_Ubuntu.run
```

### 2.3 Step 2: Remove Existing Installation (Recommended)

Before installing a new SDK version (or when upgrading from a previous version), remove any existing XDL/RPP SDK installation:

```bash
bash /usr/local/rpp/doc/uninstall.sh
```

> **Note**: This script is only available **after** a previous SDK installation. If this is a first-time installation on a clean system, skip this step.

A successful uninstallation will:

- Purge all RPP-related Debian packages
- Roll back any modified system files (e.g. `linux/version.h`)
- Unload the RPP kernel module
- Remove driver source directory and documentation

Minor warnings (e.g. kernel module not loaded, package not found) can be safely ignored if the final status indicates successful removal.

### 2.4 Step 3: Install the SDK

Grant execute permission and run the installer:

```bash
cd /path/to/sdk/directory
chmod +x xdl_sw_v1.7.2.5_x86_Ubuntu.run
sudo bash xdl_sw_v1.7.2.5_x86_Ubuntu.run
```

During installation:

1. The installer extracts the package and detects system configuration (Debian, x86_64).
2. Required system dependencies are checked and installed via `apt`.
3. RPP driver is built against the current kernel (`linux-headers-$(uname -r)`).
4. RPP-related Debian packages are installed.
5. GStreamer 1.18 and OpenCV hardware decoder are deployed.
6. Environment configuration files are written (e.g. `/etc/profile.d/R8_VE_env.sh`, `/etc/ld.so.conf.d/VE_R8_env.conf`).

**Successful completion** is indicated by a message similar to:

```
Installation completed, sdk version: [1.7.2.5], creation timestamp: [YYYYMMDD_HHMMSS].
```

If `ldconfig` reports non-symbolic-link warnings for other libraries (e.g. `libgraph_crypt.so.1`, `libsmart_core.so.1`), these are typically pre-existing and do not affect SDK operation.

### 2.5 Step 4: Load RPP Kernel Driver (After Install or Reboot)

> **Operational Note**: RPP driver loading is required **after every system reboot or power cycle**. This behavior is expected and aligned with current kernel security and deployment policies.

The RPP driver is built and installed to:

```
$HOME/azurengine/rpp_drv
```

Load the driver and set device permissions:

```bash
cd $HOME/azurengine/rpp_drv
sudo insmod rpp.ko
sudo chmod 666 /dev/rpp0_entire_ctrl /dev/ve0_entire_ctrl
```

Verify the driver is loaded:

```bash
dmesg
```

If no errors are reported, the driver is ready for use.

---

## 3. SDK Uninstallation

> **Objective**: Completely remove the XDL SDK and RPP components from the system.

### 3.1 Uninstall Procedure

Run the official uninstall script (installed with the SDK):

```bash
bash /usr/local/rpp/doc/uninstall.sh
```

This script will:

1. Purge the following Debian packages:
   - azurengine-rpp-mpu-tools
   - azurengine-rpp-drv-api
   - azurengine-graph-loader
   - azurengine-ae-smi
   - azurengine-rpp-tool-chain-main
   - azurengine-rpp-tool-chain-rppfft
   - azurengine-rpp-tool-chain-rppblas
   - azurengine-openrt-core
   - azurengine-mppsdk-core
   - azurengine-mppsdk-gst-omx
   - opencv-hw-decoder

2. Roll back modified system files (e.g. `/usr/include/linux/version.h`).

3. Unload the RPP kernel module: `rmmod rpp`

4. Remove the RPP driver source directory: `$HOME/azurengine/rpp_drv`

5. Remove SDK documentation: `/usr/local/rpp/doc`

6. Run `ldconfig` to refresh library cache.

### 3.2 Post-Uninstall

- The script removes its own directory (`/usr/local/rpp/doc`); therefore, the uninstall script cannot be run twice.
- After uninstallation, a new SDK can be installed by following Section 2.

---

## 4. Driver Validation and Troubleshooting

### 4.1 Verify RPP Device Connection

To confirm the RPP accelerator is correctly connected on the PCIe bus:

```bash
lspci | grep -i Co-processor
```

**Expected output**:

```
XX:XX.X Co-processor: Device 1f2e:00a1 (rev 01)
```

> **Note**: `XX:XX.X` is the BDF (Bus:Device.Function), e.g. `05:00.0`.

If no devices are detected, verify physical PCIe card seating and BIOS PCIe configuration.

### 4.2 Troubleshooting Driver Installation

#### Error: Module Already Loaded

```
insmod: ERROR: could not insert module rpp.ko: File exists
```

**Resolution**: The driver is already loaded. No further action is required.

#### Error: Invalid Module Format

```
insmod: ERROR: could not insert module rpp.ko: Invalid module format
```

**Resolution**: Rebuild the driver against the current kernel:

```bash
cd $HOME/azurengine/rpp_drv
make clean
make -j$(nproc)
sudo insmod rpp.ko
sudo chmod 666 /dev/rpp0_entire_ctrl /dev/ve0_entire_ctrl
```

### 4.3 PCIe Device Not Detected

If `lspci` does not show the RPP device:

- Ensure the RPP acceleration card is firmly seated.
- Check BIOS: enable PCIe, disable ASPM if recommended.
- Power off, reseat the card, then power on and run `lspci` again.

---

## 5. Document History

| Version | Date       | Description                    |
| ------- | ---------- | ------------------------------ |
| 1.0     | 2025-02-25 | Initial SOP for XDL SDK V1.7.2.5 |

---

*This SOP is provided for customer self-service deployment. For technical support, contact XDL Technical Support Team.*
