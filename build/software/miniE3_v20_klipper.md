---
layout: default
title: SKR mini E3 V2.0 Klipper Firmware
nav_exclude: true
---

# SKR mini E3 V2.0 Klipper Firmware

{: .note }
This guide is for first time installation of klipper on an mcu.  For updating, check [below](#firmware-update)

### Required Items

* Klipper must be installed onto the Raspberry Pi
* At least one microSD card needs to be available depending on number of controllers.

### Build Firmware Image

* Login to the Raspberry Pi
* Run the following:

```
sudo apt install make
cd ~/klipper
make menuconfig
```

In the menu structure there are a number of items to be selected.

* Ensure that the micro-controller architecture is set to 'STM32' 
* Ensure that the processor model is set to 'STM32F103' 
* Ensure that the Bootloader offset is set to '28KiB'
* Ensure that the Clock Reference is set to '8 Mhz'
* Ensure that "Enable extra low-level configuration options" is selected.
* Ensure that "Use USB for communication (instead of serial)" is selected.
* Ensure that "GPIO pins to set at micro-controller startup" includes '!PA14'.

![](./images/miniE3_v20_klipper_menuconfig.png)

Once the configuration is selected, select "Exit" and "Yes" if asked to save the configuration.

Run the following:

```
make clean
make
```

The `make` command, when completed, creates a firmware file **klipper.bin** which is stored in the folder `/home/pi/klipper/out`.  This file need to be copied to the Mini E3 board.  The easiest way to do this is to use a GUI like [WinSCP](https://winscp.net/eng/download.php) on Windows or either [Cyberduck](https://cyberduck.io) or scp (from terminal) on Mac to copy the file to your computer.

![](./images/cyberduck_example.png)

### Load Firmware Image

Copy the **klipper.bin** to the desktop, then rename it to **firmware.bin**

**Important:** If the file is not renamed, the firmware will not be updated properly.

Ensure that the Mini E3 board is not powered, then remove the microSD card that is installed.

Connect the microSD card to the computer with the **firmware.bin** file.  Open the microSD card in the file browser.  There may be a file named 'firmware.cur'; that file can be deleted or left alone. It does not matter because it will be overwritten later.

Copy the **firmware.bin** from the desktop onto the microSD card.  If problems are encountered transferring the file(s) to the microSD card, reformat the microSD card with a FAT32 filesystem and try again.

Replace the microSD card into the Mini E3. 

Turn on the power supply to power on the Mini E3 board.  If named properly, the SKR will automatically update with the Klipper firmware.

**Important:** If the Mini E3 is not powered with 12-24V, Klipper will be unable to communicate with the TMC drivers via UART and the Mini E3 will automatically shut down.

### Back to [Software Installation](./index.md#klipper-octoprint-configuration)

{: .note }
It is an expected part of updating klipper, that you will occasionally need to flash updated firmware onto your MCU as well.  Leaving an sdcard permanently in the mini SKR is not required for normal operation, but will simplify the firmware update process.

### Firmware Update

If you do not have an sdcard already in your SKR mini, there are unfortunately no short cuts.  To update the board, you must repeat the entire [Build Firmware Image](#build-firmware-image) and [Load Firmware Image](#load-firmware-image) processes above.

If there is an sdcard already in your skr mini, it can be reflashed without physical access.

1. Build the updated firmware image.  See [Build Firmware Image](#build-firmware-image ) above
2. stop klipper: `sudo systemctl stop klipper`
3. Determine the Serial id of your SKR, either by looking at printer.cfg, or via `ls /dev/serial/by-id`
4. execute the command `~/klipper/scripts/flash-sdcard.sh /dev/serial/your-serial-id-here btt-skr-mini-e3-v2` , replacing "your-serial-id-here" with the actual ID from the previous step
5. restart klipper: `sudo systemctl start klipper`