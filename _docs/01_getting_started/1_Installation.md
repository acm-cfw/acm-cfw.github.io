---
title: Installation
category: Getting Started
order: 1
label: draft
---

NOTE; while these insructions are written for the ACM, they are similar for the E2M.

The installation of this custom firmware requires the following steps:
1. Download and flash the batocera image into a USB drive (16GB+)
2. Connect the ACM to the computer via a USB data cable
3. Copy and extract the installation scripts from the USB to your computer
4. Insert the USB with the batocera image into port P2 of the ACM
5. Run the script (```acm-backup-and-install.sh``` or ```acm-backup-and-install.bat```) that will backup the ACM nand and install the necessary files into the ACM nand partitions
6. Wait for reboot

Once the device reboots, the system will start batocera automatically. During that process batocera will expand the second partition of the USB from 500MB to the maximum available space on the drive. After that process batocera will be available to enjoy.

Below are additional details of each of the steps involved.

## Download and flashing the USB image

* Download the latest release of the firmware for your ACM console from the [releases page](https://github.com/ACM-CFW/acm-cfw.github.io/releases)
* Download [Balena Etcher](https://www.balena.io/etcher/) for your operative system
* Install and run Balena Etcher
* Open the firmware file and select your USB drive as your target device. Note that you will need a minimum of 8GB but we recommend a minimum of 16GB.
* Click on Flash! and wait for the image to be created.

## Using a data cable to connect the ACM

The cable bundled with the ACM is a power cable only, it does not transfer data. You'll need to use a full data cable, for instance like the one used to connect Dualshock 4 controllers to a Playstation.

## Copying scripts from the USB

* Once the batocera image has been flashed, mount the USB drive on your computer and copy the acm_installation.zip archive to your computer.
* Extract the ```acm_installation.zip``` archive 
* Open the extracted ```acm_installation``` folder, inside you will see the following files:
    * ```acm_backup_and_install.bat``` (windows installation)
    * ```acm_backup_and_install.sh``` (linux installation)
    * ```acm_backup_and_install_mac.sh``` (mac installation)
    * ```data``` folder with the required installation files
    * ```bin``` folder with the required installation programs

The following instructions provide the basic steps to install or update the firmware on your device, but if you need further information please follow the rest of the installation instructions and details from the [batocera wiki](https://wiki.batocera.org/install_batocera).

## Installation from scratch

The installation package includes a version for the installation script for each architecture:
* Run the installation. Note that the installation package includes a version for the installation script for each architecture:
    * Linux: 
        * Open a terminal
        * Change directory to the location where you have extracted the installation archive
        * Run the installation with ```./acm_backup_and_install.sh```
    * MacOS: 
        * Open a terminal
        * Change directory to the location where you have extracted the installation archive
        * Run```acm_backup_and_install_mac.sh```
    * Windows: 
        * Open the folder where the installation files has been extracted
        * Double click on the ```acm_backup_and_install.bat```
* Follow the prompts on the screen:
    * Connect the USB cable to the ACM, but don't connect it to the computer yet
    * Make sure the ACM power switch is set to ON
    * When you see the prompt: ```*** PRESS A KEY WHEN READY AND CONNECT THE ACM TO THE USB ***```, press a key and then connect the USB cable to the computer
* The installation will start by transfering some images to the ACM memory
* The installation will show and output similar to this:

```shell
$ ./acm-backup-and-install.sh
ACM batocera installation
=========================

1. Copy the folder install to a USB key
2. Insert the USB key into the 2P port of the ACM
3. Connect the usb power cable to the ACM power port
4. Press a key when you are ready, and then plug the USB cable to this computer.

*** PRESS A KEY WHEN READY AND CONNECT THE ACM TO THE USB ***
No Allwinner devices in FEL mode detected.
Attempt #:1
No Allwinner devices in FEL mode detected.
Attempt #:2
No Allwinner devices in FEL mode detected.
Attempt #:3
Initializing...

Sending now...


finished: status:0...31


Astro City Mini should be in FEL mode now.

Booting from memory...
100% [================================================]    13 kB,  126.4 kB/s
100% [================================================]  3549 kB,  191.9 kB/s 
100% [================================================]   517 kB,  191.7 kB/s 
100% [================================================]  1114 kB,  191.8 kB/s 
100% [================================================]   131 kB,  190.4 kB/s
Done. The installation will continue on the ACM...
The ACM will reboot when the hack is installed.
```

* After this is finished, the script will continue installing two small partitions into the ACM NAND. This process takes only a few seconds.
* Once the installation is finished, the device will reboot and batocera will start its processes.
* The first time batocera boots it will enlarge the second partition to all the available storage. Once that's finished, batocera will continue with its normal boot sequence. You should see the batocera logo in a bit, followed with EmulationStation (the main batocera interface) starting.

## Updating and existing installation without flashing

You can update the batocera system by flashing a new installation image, or you can update an existing image by following this procedure:
* Download the ``boot.tar.xz`` for the latest release from the [releases page](https://github.com/ACM-CFW/acm-cfw.github.io/releases)
* Insert the USB with your existing batocera installation on your PC/Mac/Linux
* Mount the first partition called BATOCERA
* Extract all the contents of the ``boot.tar.xz`` file and copy the contents to the BOOT partition
* Eject the USB
* Insert the USB on your console and boot

