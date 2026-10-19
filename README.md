# Bare-Metal Triage & Recovery Toolkit (Specification)

Architecture and payload specification for a high-throughput, offline hardware diagnostics, OS deployment, and incident recovery drive. 

Designed to eliminate I/O bottlenecks during live RAM-disk booting (WinPE / Linux Live environments) and provide zero-install diagnostic execution on degraded or air-gapped hosts without altering target storage or registry state.

## Hardware Architecture

Standard flash drives can choke on random 4K reads during live OS boot and offline malware scanning. This drive was specifically selected as the ideal device for this use case because it runs on an SSD-grade USB bridge to maintain near-native NVMe throughput.

* **Media:** Kingston DataTraveler Max 256GB (DTMAXA/256GB)
* **Interface:** USB 3.2 Gen 2 (Type-A)
* **Throughput:** Up to 1,000 MB/s Read / 900 MB/s Write
* **Bootloader:** [Ventoy](https://www.ventoy.net/) (Multiboot UEFI / Legacy BIOS, exFAT payload partition)

## Directory Structure

    E:\
    ├── ventoy\                       # Bootloader configuration and hardware bypasses (drive root)
    └── Ventoy_Toolkit\
        ├── 01_OS_Deploy\             # Mainstream bare-metal installation media
        ├── 02_Security_Audit\        # Security, auditing, and amnesic live environments
        ├── 03_Emergency_Rescue\      # Block-level imaging, partition repair, WinPE, and RAM testing
        ├── 04_CS_Academic\           # Minimalist UNIX/Linux research and lab targets
        ├── 05_Hardware_&_Drivers\    # Offline NIC bootstrapping and hardware telemetry binaries
        └── 06_System_Maintenance\    # Zero-install portable diagnostics, triage, and repair tools

## Boot Automation (`ventoy/ventoy.json`)

Global control configuration applied during Ventoy initialization to enable Windows 11 installation bypasses (TPM, Secure Boot, RAM) and local offline account provisioning (NRO), scoped to your toolkit directory:

```json
{
    "control": [
        { "VTOY_WIN11_BYPASS_CHECK": "1" },
        { "VTOY_WIN11_BYPASS_NRO": "1" },
        { "VTOY_DEFAULT_SEARCH_ROOT": "/Ventoy_Toolkit" },
        { "VTOY_DEFAULT_MENU_MODE": "1" }
    ]
}
```
## 1. Pre-Boot Live Images (`01` - `04`)

Booted directly into RAM via Ventoy without extracting filesystem archives. Numeric prefixes enforce deterministic menu ordering.

### 01_OS_Deploy (Standard Provisioning)
* **[Windows 11](https://www.microsoft.com/software-download/windows11):** Clean modern Windows installation image.
* **[Windows 10](https://www.microsoft.com/software-download/windows10ISO):** Legacy client deployment image for older hardware baselines.
* **[Fedora Workstation](https://fedoraproject.org/workstation/):** Upstream enterprise Linux workstation environment.
* **[Ubuntu Desktop](https://ubuntu.com/download/desktop):** Standard LTS Debian-based deployment image.
* **[Linux Mint (Cinnamon)](https://linuxmint.com/):** Production desktop Linux for client machine repurposing.
* **[Linux Mint (XFCE)](https://linuxmint.com/):** Low-overhead desktop Linux for resource-constrained legacy systems.

### 02_Security_Audit (Security & Isolation)
* **[Kali Linux Live](https://www.kali.org/get-kali/#kali-live):** Portable offensive security, protocol auditing, and network testing suite.
* **[Parrot OS Home](https://parrotsec.org/):** Lightweight general security and development workstation.
* **[Tails](https://tails.net/):** Amnesic incognito live operating system for isolated, non-persistent operations.

### 03_Emergency_Rescue (Disaster Recovery & Hardware Faults)
* **[SystemRescue](https://www.system-rescue.org/):** Linux-based administrative toolkit for disk partitioning, filesystem recovery, and offline boot repair.
* **[Clonezilla Live](https://clonezilla.org/):** Bare-metal, block-level partition and disk imaging for rapid drive migration.
* **[Hiren's BootCD PE](https://www.hirensbootcd.org/):** WinPE-based rescue platform for offline registry repair, BCD rebuilding, and file retrieval.
* **[MemTest86](https://www.memtest86.com/):** Standalone physical RAM tester for pinpointing defective memory modules and address bus faults.

### 04_CS_Academic (UNIX & Minimalist Targets)
* **[FreeBSD](https://www.freebsd.org/):** Monolithic UNIX operating system for studying BSD userland, kernel mechanics, and ZFS implementations.
* **[Alpine Linux](https://alpinelinux.org/):** Minimal musl-libc and BusyBox distribution for building container hosts and embedded environments.

## 2. Offline Network Bootstrapping (`05_Hardware_&_Drivers`)

Carries pre-seeded offline network driver indexes to resolve fresh-install connectivity immediately, allowing all subsequent drivers to be pulled online without bloating the flash drive.

* **[Snappy Driver Installer Origin (SDIO)](https://glenn.delahoy.com/snappy-driver-installer-origin/):** Offline driver index seeded strictly with LAN, WLAN, and WWAN packs:
  * Intel, Realtek, Broadcom, and Qualcomm network chipsets.
  * Standard mobile broadband and Wi-Fi interface packs.
* **[3DP Net](https://www.3dpchip.com/3dpchip/3dp/net_down_content.php):** Standalone, single-binary offline Ethernet and Wi-Fi driver installer for legacy recovery.

## 3. Portable Host Diagnostics (`05` & `06`)

Zero-install portable binaries executed directly from flash storage (either inside a live WinPE environment or on an active host) to avoid altering the target's operating system or registry.

### Process, Kernel & Memory Analysis
* **[System Informer Portable](https://portableapps.com/apps/utilities/system-informer-portable):** Advanced process manager with kernel-mode driver support (`ksi.dll`) for handle inspection, thread tracing, and active socket tracking.
* **[Sysinternals RAMMap](https://portableapps.com/apps/utilities/rammap-portable):** Advanced physical memory allocation monitor for analyzing standby lists, kernel pools, and memory leaks.
* **[HWiNFO Portable](https://portableapps.com/apps/utilities/hwinfo-portable):** Comprehensive hardware sensor monitor for VRM voltages, rail stability, and detailed bus configurations.
* **[HWMonitor Portable](https://portableapps.com/apps/utilities/hwmonitor-portable):** Real-time thermal and voltage monitor for rapid thermal throttling diagnosis.
* **[CPU-Z Portable](https://portableapps.com/apps/utilities/cpu-z-portable):** Processor architecture, cache layout, memory timings, and mainboard revision identification.
* **[GPU-Z Portable](https://portableapps.com/apps/utilities/gpu-z-portable):** Video controller inspection, VBIOS reporting, clock speeds, and PCIe link width validation.
* **[StressMyPC Portable](https://portableapps.com/apps/utilities/stressmypc-portable):** Synthetic CPU, GPU, and disk load utility for thermal headroom and power supply verification.

### Storage & Filesystem Analysis
* **[CrystalDiskInfo Portable](https://portableapps.com/apps/utilities/crystaldiskinfo_portable):** S.M.A.R.T. health reader, reallocated sector monitor, and NAND wear-level tracking.
* **[CrystalDiskMark Portable](https://portableapps.com/apps/utilities/crystaldiskmark_portable):** Storage benchmark utility leveraging DiskSpd for sequential and random 4K IOPS throughput testing.
* **[Everything Portable](https://portableapps.com/apps/utilities/everything-portable):** Instant filename search engine reading raw NTFS Master File Tables (MFT).
* **[TreeSize Free Portable](https://portableapps.com/apps/utilities/treesize-free-portable):** Fast MFT-based folder size analyzer for isolating runaway log files and drive exhaustion.
* **[WinDirStat Portable](https://portableapps.com/apps/utilities/windirstat_portable):** Visual disk usage mapper providing file extension breakdowns and spatial block maps.
* **[TeraCopy Portable](https://support.codesector.com/en/articles/8789953-portable-version-of-teracopy):** High-speed file transfer tool with CRC32/MD5 verification, buffer tuning, and interactive error recovery.
* **[Explorer++ Portable](https://portableapps.com/apps/utilities/explorerplusplus_portable):** Multi-tabbed shell alternative for file management when Windows `explorer.exe` is frozen or corrupt.

### Network, Security & System Utilities
* **[Wireshark Portable](https://www.wireshark.org/download.html):** Network packet analyzer and protocol dissector for live traffic auditing and packet capture inspection.
* **[Emsisoft Emergency Kit](https://portableapps.com/apps/security/emsisoft-emergency-kit-portable):** Dual-engine offline malware and rootkit scanner with command-line interface (`a2cmd.exe`) support.
* **[VirusTotal CLI](https://github.com/VirusTotal/vt-cli):** Fast executable hash checking and submission utility for triage analysis.
* **[PuTTY Portable](https://portableapps.com/apps/internet/putty_portable):** Telnet, SSH, and serial terminal client for out-of-band console access to network hardware.
* **[7-Zip Portable](https://portableapps.com/apps/utilities/7-zip_portable):** High-compression file archiver supporting standard formats, split volumes, and nested ISO extraction.
* **[Notepad++ Portable](https://portableapps.com/apps/development/notepadpp_portable):** Extensible text and code editor for reviewing raw log files, configuration scripts, and hex dumps.
* **[BleachBit Portable](https://portableapps.com/apps/utilities/bleachbit_portable):** Disk cleaner for purging volatile browser caches, temporary directories, and junk files.
* **[Rufus Portable](https://portableapps.com/apps/utilities/rufus-portable):** Standalone utility for flashing bootable USB drives, testing flash media, and pulling official Windows media images.

## Disclaimer

Product names, logos, and other trademarks referenced in this repository are the property of their respective owners. References are for informational purposes and do not imply endorsement. Third-party software, operating systems, and ISO images remain subject to their owners' terms and licenses and are not covered by CC0.

The original documentation, curation, and manifest in this repository are dedicated to the public domain under [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/).