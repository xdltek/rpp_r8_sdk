## Document Purpose

**XDL RPP SDK Official SOP** - x86_64 platform deployment

- For **end customers, system integrators, and solution partners** to complete installation independently
- Covers: environment setup -> SDK installation -> functional validation
- Emphasizes **reproducibility**, **stability**, and **first-boot success rate**


## Scope

| Item | Description |
|------|------|
| Target readers | System administrators, solution engineers, AI application developers |
| Deployment scenarios | Customer delivery (production / evaluation) |
| Required skills | Linux command line, basic CMake, Python/Conda |



## 1. System Requirements

Before installation, make sure the target system meets the minimum requirements:

- **Operating system**: Ubuntu 20.04 LTS (x86_64)
- **System memory**: >= 16 GB DDR5
- **CPU architecture**: x86_64 (supports PCIe Gen3)
- **Compiler**: GCC 9.4.0 · **CMake**: >= 3.26.5 · **Python**: 3.11
- **Build tools**: `build-essential`, `linux-headers`

> Failing to meet these requirements may cause installation failure or unstable runtime behavior



## 2. Before Installation: Hardware Validation

**Goal**: confirm that the RPP accelerator card is correctly enumerated on the PCIe bus

Run:

```bash
lspci | grep Co-processor
```

**Expected output**: `XX:XX.X Co-processor: Device 1f2e:00a1 (rev 01)`

- `XX:XX.X` = BDF, for example **05:00.0**
- A Co-processor entry indicates successful detection

> If no device appears, check the card seating, PCIe slot, and BIOS settings



## 3. SDK Installation - Step 1: Download and unpack the package

The x86 SDK package is provided in the `sdk` folder:

```
azurengine_sw_v1.7.3.11_x86_Ubuntu.tar.gz
```

Unpack it first:

```bash
tar zxvf azurengine_sw_v1.7.3.11_x86_Ubuntu.tar.gz
cd azurengine_sw_v1.7.3.11_x86_Ubuntu
```

The extracted folder contains:

- `azurengine_sw_v1.7.3.11_x86_Ubuntu.run`
- `get_version.sh`
- `install.sh`
- `uninstall.sh`
- `upgrade.sh`
- `verify_run_file.sh`


## 3. SDK Installation - Step 2: Uninstall the Previous Version (Recommended)

```bash
bash /usr/local/rpp/doc/uninstall.sh
```

- Removes all RPP packages, drivers, and support files
- Minor warnings can be ignored if the final result shows success

> **Important**: install on a clean system or after a full uninstall; multiple versions are not supported



## 3. SDK Installation - Step 3: Run the installer and upgrade scripts

Verify the SDK package:

```bash
bash verify_run_file.sh
```

Expected output:

`Verification passed: MD5 checksum matches for file: azurengine_sw_v1.7.3.11_x86_Ubuntu.run`

Check the current installed version:

```bash
bash get_version.sh
```

Example output:

`1.7.2.4`

Upgrade to the new SDK:

```bash
bash upgrade.sh
```

If the installation succeeds, you should see:

`Installation completed, sdk version: [1.7.3.11], creation timestamp: [20260326_195602].`

Alternatively, you can uninstall and install via the provided scripts:

```bash
bash uninstall.sh
bash install.sh
```

## 4. Driver Installation and Verification

**Load the driver** (must be repeated after every reboot):

```bash
cd $HOME/azurengine/rpp_drv
sudo insmod rpp.ko
sudo chmod 666 /dev/rpp0_entire_ctrl /dev/ve0_entire_ctrl
```

**Verification**: no errors in `dmesg` means the driver loaded successfully

> For automated setup, refer to the XDL Demo Guide



## 5. Troubleshooting - Driver Issues

| Error | Resolution |
|------|------|
| `File exists` | The driver is already loaded; no action needed |
| `Invalid module format` | Rebuild with `make clean && make -j$(nproc)` and then repeat the load steps |



## 5. Troubleshooting - PCIe Not Detected

**Symptom**: `RPP accelerator card not found on PCIe bus`

**Possible causes**: loose card seating, faulty slot, PCIe disabled in BIOS, outdated BIOS

**Diagnostics**:

```bash
lspci | grep -i Co-Processor
sudo lspci -s "05:00.0" -vv | awk '/LnkCap:|LnkSta:/ {print}'
```

**Resolution**: reseat the card (insert at a 30-degree angle, then press firmly), enable PCIe Gen4/5 in BIOS, disable ASPM, update the BIOS



## Summary

**RPP SDK installation flow**

1. Verify system requirements
2. Hardware detection: `lspci | grep Co-processor`
3. Download -> uninstall previous version -> run installer
4. Load the driver after every reboot
5. Refer to the troubleshooting section if issues occur


## 6. Acknowledgments

This guide was developed by the XDL Technical Support team based on extensive customer engagements and feedback. We thank all customers and partners who contributed to the validation and improvement of this installation and deployment process.


## 7. Feedback and Contributions

We welcome feedback and contributions to improve this document. Please submit any suggestions, corrections, or additional information to XDL Technical Support or an authorized support channel. Your input helps us enhance the quality and accuracy of our documentation for all customers.


## 8. Disclaimer

The information contained in this document is provided "as is" by XDL Technologies. XDL Technologies makes no representations or warranties, express or implied, regarding the accuracy, completeness, or reliability of the information. XDL Technologies reserves the right to modify or discontinue any product or service described in this document without prior notice.


## 9. Copyright

© 2026 XDL Technologies. All rights reserved. No part of this document may be reproduced or transmitted in any form or by any means, electronic or mechanical, including photocopying, recording, or any information storage and retrieval system, without the prior written permission of XDL Technologies.


## 10. Support

For customer delivery engagements, please contact your designated **XDL Technical Support** or authorized support channel. When reporting issues, include:

* SDK version and creation timestamp
* Host OS and kernel version
* RPP hardware model and PCIe topology
* Full installation or build logs

Providing the above information will significantly accelerate issue resolution.

For additional assistance, performance tuning, or platform-specific guidance, please contact your **XDL Technical Support** or authorized support channel.


## 12. Legal Notice

This document is provided for informational purposes only and does not constitute a legal agreement. XDL Technologies assumes no responsibility for any errors or omissions in this document. The information contained herein is subject to change without notice.


## 13. Trademarks

XDL and the XDL logo are trademarks of XDL Technologies. All other product names, company names, trademarks, and registered trademarks mentioned herein are the property of their respective owners.


## 14. Endnotes

1. All specifications and features are subject to change without notice.
2. Performance metrics are based on internal testing and may vary based on system configuration and workload.
3. For the latest information, please refer to the official XDL Technologies website.


## 15. Safety and Compliance

XDL Technologies products comply with relevant safety and environmental regulations. Users are advised to follow proper handling and usage guidelines as outlined in the product documentation to ensure safety and compliance with local regulations.


## 16. Release Notes

- **Version 2.0** (2026-01-18): Initial release of the RPP SDK Installation Guide for x86_64 platforms.
- **Version 2.1** (2026-02-07): Updated formatting and added detailed troubleshooting steps based on customer feedback.
- **Version 2.2** (2026-03-19): Updated to Installation and Deployment Guide for x86_64 platforms.
- **Version 2.3** (2026-03-30): Updated x86 SDK installation instructions for `azurengine_sw_v1.7.3.11_x86_Ubuntu.tar.gz`.


## Revision History

| Version | Date       | Author                     | Description                        |
|---------|------------|----------------------------|------------------------------------|
| 2.0     | 2026-01-18 | XDL Technical Support Team | Initial version                    |
| 2.1     | 2026-02-07 | XDL Technical Support Team | Release                            |
| 2.2     | 2026-03-19 | XDL Technical Support Team | Updated to Installation and Deployment Guide |
| 2.3     | 2026-03-30 | XDL Technical Support Team | Updated x86 SDK installation |

**Technical support**: XDL Demo Guide · XDL Technical Support Team