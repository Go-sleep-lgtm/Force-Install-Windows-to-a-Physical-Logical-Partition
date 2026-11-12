# Force Install Windows to a Physical Logical Partition (WinToGo + WIM Deployment)

## 🎯 Objective
- **Primary OS**: Win10 installed on the Primary partition of the physical drive, used as the daily driver for coding.
- **Secondary OS**: Win7 installed on a **Logical Partition** of the same physical drive, dedicated to gaming.

## 🕳️ The Pitfall: Installer Rejection
Initially, I tried the standard method: booting from the Windows installer and selecting the logical partition.
The installer immediately threw an error and refused to proceed!
After researching, I found out that under the traditional BIOS/MBR architecture, the Windows native installer has a strict rule: **The OS must be installed on a Primary Partition.** Logical partitions are completely blocked.

## 🧠 The Workaround: Run First, Then Move
Since the ISO installer won't allow it, we don't install directly from the ISO.
The core idea: **Find a place that CAN run the system, configure it fully, and then "move" the entire system into the logical partition.**
It's like building a car on open ground, making sure it runs perfectly, and then driving it into a tight parking spot, rather than trying to build the car inside the parking spot.

## 🛠️ Implementation Steps (The "Wild" Way)
1. **WinToGo Setup**: Use a WinToGo tool to install Win7 onto a 16GB USB flash drive.
2. **Complete OOBE**: Boot from the USB drive, complete the Out-of-Box Experience (OOBE), install basic drivers, and ensure the system is fully functional.
3. **Boot into PE**: Restart the PC and boot into a PE environment (my PE is based on Win11).
4. **Capture WIM**: Use Dism++ to capture the fully configured system on the USB drive into a WIM image.
5. **Force Deployment**: Use Dism++ again to force-deploy the WIM image onto the target **Logical Partition** of the physical drive.
6. **Reboot**: Remove the USB drive and restart the PC.

## 🎉 The Result
Booted straight into the desktop!
All software and data were intact, with zero errors during the process.
Most surprisingly: I didn't type a single `bcdboot` or `bootsect` command to repair the bootloader.

## 🤔 Why did this work? (My Analysis)
Most online tutorials install directly from an ISO, which causes issues with partition types and bootloader recreation.
But I used a **"captured WIM from a running system"**.
Because the WIM image already contains a complete boot file structure (Bootmgr, etc.), Dism++ likely automatically injected the boot entry into the primary system's (Win10) BCD menu during deployment.
**Core Takeaway**: The source MUST be a "fully configured, booted system", not a clean ISO. This one step bypasses the most painful part: bootloader repair.

## 💻 My Hardware Environment
- ThinkPad X230 (3rd Gen i5, a true veteran)
- 16GB USB drive (for WinToGo)
- 8GB USB drive (for MicroPE)

---
If you are also stuck in the "Windows cannot be installed to this disk" error or the "infinite blue screen loop" after installation, try this "Run First, Move Later" approach. Feel free to correct me if I missed anything.
