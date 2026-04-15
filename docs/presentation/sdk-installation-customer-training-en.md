# XDL RPP SDK Installation Training Deck

This file is a slide-ready Markdown draft for a customer training PPT.
Each `##` section represents one slide.

---

## Slide 1 - Title

**XDL RPP SDK Installation Guide**

Customer Training for SDK Setup, Driver Loading, and First Validation

- Product: XDL RPP SDK
- Audience: customers, system integrators, solution partners
- Goal: complete installation independently and successfully

Presenter notes:

- Use this session to explain the full installation path from repository download to driver verification.
- Emphasize that customers should select the correct guide based on CPU architecture.

---

## Slide 2 - Training Objective

After this session, customers will be able to:

- Identify the correct SDK package for their platform
- Clone the repository and find the correct documentation
- Check host requirements before installation
- Install or upgrade the SDK package
- Load the driver and verify the device
- Troubleshoot the most common installation issues

Presenter notes:

- Position this as a practical onboarding session, not just a document walkthrough.

---

## Slide 3 - Supported Platforms

The XDL RPP SDK currently supports:

- `x86_64` host platform
- `aarch64 / ARM64` host platform
- Operating system: `Ubuntu 20.04 LTS`

Guide selection:

- `docs/content/x86-en.md` for `x86_64`
- `docs/content/arm-en.md` for `aarch64`

Important:

- Do not mix `x86` and `ARM` installation steps
- Use the package and commands that match the target host

Presenter notes:

- This is one of the most important slides because many install issues come from using the wrong architecture package.

---

## Slide 4 - Minimum Requirements

Before installation, confirm the target system meets the requirements:

- OS: `Ubuntu 20.04 LTS`
- Memory: `>= 16 GB DDR5`
- CPU architecture: `x86_64` or `aarch64`
- PCIe support: `PCIe Gen3` or above
- Compiler: `GCC 9.4.0`
- CMake: `>= 3.26.5`
- Python: `3.11`
- Required tools: `build-essential`, `linux-headers`

Presenter notes:

- Explain that unmet requirements can cause installation failure, driver mismatch, or unstable runtime behavior.

---

## Slide 5 - Get the Repository

Recommended first step:

```bash
git clone git@github.com:xdltek/rpp_r8_sdk.git
cd rpp_r8_sdk
```

Repository layout:

- Documentation root: `rpp_r8_sdk/README.md`
- English guides: `rpp_r8_sdk/docs/content/`
- SDK packages: `rpp_r8_sdk/sdk/`
- PDF delivery guides: repo root PDF files

Presenter notes:

- If the customer uses HTTPS in their environment, replace the Git SSH URL with the company-approved HTTPS URL.
- Show the repo tree briefly so customers know where to look next.

---

## Slide 6 - SDK Paths and Package Names

SDK package location:

- `rpp_r8_sdk/sdk/`

Current package examples:

- `azurengine_sw_v1.7.3.11_x86_Ubuntu.tar.gz`
- `azurengine_sw_v1.7.3.14_aarch64_Ubuntu.tar.gz`

After extraction, the package directory contains:

- `.run` installer package
- `verify_run_file.sh`
- `get_version.sh`
- `upgrade.sh`
- `install.sh`
- `uninstall.sh`

Presenter notes:

- Point out that the customer usually works inside the extracted SDK folder during installation.

---

## Slide 7 - Pre-Installation Hardware Check

Before installing the SDK, confirm the RPP accelerator card is detected:

```bash
lspci | grep Co-processor
```

Expected result:

```text
XX:XX.X Co-processor: Device 1f2e:00a1 (rev 01)
```

Interpretation:

- `XX:XX.X` is the BDF, for example `05:00.0`
- If the device appears, PCIe detection is successful

If no device appears:

- Check card seating
- Check PCIe slot status
- Check BIOS PCIe settings

Presenter notes:

- Make it clear that customers should not continue with software installation until hardware enumeration is confirmed.

---

## Slide 8 - Installation Flow Overview

Standard installation flow:

1. Check system requirements
2. Confirm PCIe device detection
3. Go to the correct SDK package path
4. Unpack the SDK package
5. Uninstall the previous version if present
6. Verify package integrity
7. Run upgrade or install scripts
8. Load the driver
9. Perform first validation

Presenter notes:

- This slide is useful as a roadmap before showing command-level details.

---

## Slide 9 - Installation Commands

Example for `x86_64`:

```bash
cd rpp_r8_sdk/sdk
tar zxvf azurengine_sw_v1.7.3.11_x86_Ubuntu.tar.gz
cd azurengine_sw_v1.7.3.11_x86_Ubuntu
bash /usr/local/rpp/doc/uninstall.sh
bash verify_run_file.sh
bash get_version.sh
bash upgrade.sh
```

Alternative clean install:

```bash
bash uninstall.sh
bash install.sh
```

Presenter notes:

- Explain that `upgrade.sh` is the normal path when moving from an older version, while `install.sh` is used after a clean uninstall.

---

## Slide 10 - ARM Installation Example

Example for `aarch64 / ARM64`:

```bash
cd rpp_r8_sdk/sdk
tar zxvf azurengine_sw_v1.7.3.14_aarch64_Ubuntu.tar.gz
cd azurengine_sw_v1.7.3.14_aarch64_Ubuntu
bash /usr/local/rpp/doc/uninstall.sh
bash verify_run_file.sh
bash get_version.sh
bash upgrade.sh
```

Success example:

```text
Installation completed, sdk version: [1.7.3.14], creation timestamp: [20260404_181046].
```

Presenter notes:

- Highlight that the command flow is the same, but the package name and output version are architecture-specific.

---

## Slide 11 - Driver Load After Installation

The driver must be loaded after every reboot:

```bash
cd $HOME/azurengine/rpp_drv
sudo insmod rpp.ko
sudo chmod 666 /dev/rpp0_entire_ctrl /dev/ve0_entire_ctrl
```

Verification:

- Check that `insmod` does not report an error
- Check `dmesg` if troubleshooting is needed

Key reminder:

- SDK installation alone is not enough
- Driver loading is required before the device can be used

Presenter notes:

- This is another common customer pain point, so call it out clearly.

---

## Slide 12 - Post-Installation Validation

Recommended validation checklist:

- Confirm package verification passed
- Confirm SDK version with `bash get_version.sh`
- Confirm driver loads successfully
- Confirm device nodes are accessible
- Confirm no abnormal errors in `dmesg`

Suggested message to customers:

- If all checks pass, the SDK environment is ready for application deployment

Presenter notes:

- Keep this slide operational and outcome-focused.

---

## Slide 13 - Common Issues and Fixes

Driver-related issues:

- `File exists`
  Meaning: driver is already loaded
  Action: no further action required

- `Invalid module format`
  Meaning: driver build does not match the running kernel
  Action:

```bash
make clean
make -j$(nproc)
```

Then reload the driver.

PCIe detection issue:

- If the card is not detected, reseat the card, check BIOS PCIe configuration, disable ASPM if needed, and update the BIOS if required.

Presenter notes:

- Focus on quick first-response troubleshooting that customer engineers can perform on site.

---

## Slide 14 - Customer Support Information

When requesting support, please provide:

- SDK version and creation timestamp
- Host OS version and kernel version
- Hardware model and PCIe topology
- Full installation logs
- Error messages or screenshots

Support channel:

- Contact your designated XDL Technical Support representative or authorized support channel

Presenter notes:

- This slide helps reduce turnaround time for customer issues.

---

## Slide 15 - Summary

Key takeaways:

- Choose the correct guide for `x86_64` or `aarch64`
- Start from the repository and use the `sdk/` path
- Validate hardware before software installation
- Verify, install, and then load the driver
- Re-load the driver after every reboot
- Escalate with logs and version information if issues occur

Closing message:

- Following the official guide step by step gives the best first-time installation success rate

