![XDL Logo](images/logo_color_horizontal.png)

# 🚀 XDL RPP SDK

High-performance AI inference SDK optimized for **RPP accelerators (DSP + CUDA)**.

> ⚡ Designed for real-world deployment, not just research.

---

## 🔥 Why XDL RPP SDK

* 🚀 Ultra-fast AI inference on RPP hardware
* ⚡ DSP + CUDA hybrid acceleration
* 📦 Production-ready SDK for deployment
* 🔧 Easy integration for edge devices

---

## 🎬 Quick Start

```bash
git clone https://github.com/xdltek/rpp_r8_sdk
cd rpp_r8_sdk

mkdir build && cd build
cmake ..
make -j

./demo_yolo
```

---

## 🧠 Supported Models

* YOLOv5 / YOLOv8
* ResNet50
* ONNX models
* Qwen Series
* Transformer models

---

## 📘 Installation & Deployment Guides

This section provides detailed installation instructions for different platforms.

| Architecture  | English                             | 中文                                  |
| ------------- | ----------------------------------- | ----------------------------------- |
| x86           | [x86-en.md](docs/content/x86-en.md) | [x86-zh.md](docs/content/x86-zh.md) |
| ARM (aarch64) | [arm-en.md](docs/content/arm-en.md) | [arm-zh.md](docs/content/arm-zh.md) |

---

## 📄 PDF Downloads

| Document                                  | English                                                                                              | 中文                                                                                                      |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| SDK Installation & Deployment Guide (x86) | [EN](https://github.com/xdltek/rpp_r8_sdk/raw/main/xdl-sdk-installation-deployment-guide-x86-en.pdf) | [中文](https://github.com/xdltek/rpp_r8_sdk/raw/main/xdl-sdk-installation-deployment-guide-x86-zh-cn.pdf) |
| SDK Installation & Deployment Guide (ARM) | [EN](https://github.com/xdltek/rpp_r8_sdk/raw/main/xdl-sdk-installation-deployment-guide-arm-en.pdf) | [中文](https://github.com/xdltek/rpp_r8_sdk/raw/main/xdl-sdk-installation-deployment-guide-arm-zh-cn.pdf) |

---

## 🧭 Which Guide Should I Use?

* If your host is **x86_64**, use the **x86** guide
* If your host is **aarch64 / ARM64**, use the **ARM** guide
* Choose **English** or **中文**

---

## ⚠️ Notes

* Do not mix steps from different architectures
* Commands and packages differ between x86 and ARM
* Driver loading steps are included in each guide

---

## 🤝 Support

For customer delivery support, contact your designated **XDL Technical Support** channel.
