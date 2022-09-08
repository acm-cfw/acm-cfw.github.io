## PocketGo-S30 Custom Firmware

The Pocket-Go S30 (also known as Miyoo-350 as marked on the board) is an Allwinner A33 SOC (sun8i) based console. 

The original firmware (OFW) modifications have not been published by the vendor, therefore breaching the GPL license its software is based upon. This page is a summary of the efforts to develop an open source firmware based on the available open source sunxi/allwinner u-boot and kernels.

## Status/To-Do list

Complete
- UART output from Mainline U-Boot
- Booting into U-Boot from USB (sunxi-fel)
- Booting into U-Boot from SDCard 

Ongoing
- U-Boot spinor support
- Kernel boot/console output

Pending
- Kernel boot
- Display

## Accessing the console with the original firmware

The OFW has adb daemon enabled, so you can use it to open a terminal session on the console. From an ubuntu linux pc:

```console
$ sudo udevadm trigger
$ adb shell
* daemon not running; starting now at tcp:5037
* daemon started successfully
zkswe@flythings:/ # ls
bin       dev       init      mnt       sbin      system    userdata
data      etc       lib       proc      sys       tmp       usr
zkswe@flythings:/ # 
```

You can find a full output of the dmesg output at [the bottom of this page](#OFW-dmesg-output)

The original kernel:
```console
uname -a
Linux localhost 3.4.39 #154 SMP PREEMPT Mon Jan 25 07:18:08 UTC 2021 armv7l GNU/Linux
```

Partitions:
```console
zkswe@flythings # cat /proc/partitions                         
major minor  #blocks  name

  31        0        384 mtdblock0
  31        1        128 mtdblock1
  31        2       7296 mtdblock2
  31        3        128 mtdblock3
  31        4        256 mtdblock4
 179        0   30910464 mmcblk0 <- SDCARD device
 179        1   30909440 mmcblk0p1 <- SDCard partition 1
 ```
The partitions can also be seen via dmesg:
```console
[    0.409669] m25p_probe()1167 - Use the Dual Mode Read.
[    0.409826] m25p80 spi0.0: found zb25q64, expected w25q128
[    0.409841] m25p80 spi0.0: zb25q64 (8192 Kbytes)
[    0.410680] Creating 6 MTD partitions on "spi0.0":
[    0.410699] 0x000000000000-0x000000060000 : "uboot"
[    0.411552] 0x000000060000-0x000000080000 : "env"
[    0.412317] 0x000000080000-0x0000007a0000 : "boot"
[    0.412996] 0x0000007a0000-0x0000007c0000 : "res"
[    0.413661] 0x0000007c0000-0x000000800000 : "boot_logo"
[    0.414339] 0x000000800000-0x000000800000 : "UDISK"
[    0.414350] mtd: partition "UDISK" is out of reach -- disabled
```

## UART

The only UART connected on the S30 is the UART1 (ttyS1). In order to get the uart to produce any output you need to change the U-boot configuration to redirect the output to that UART. In order to do so you need to change the following:
- Modify the DTS to replace any uart0 mentions to uart1, also change the pins to uart1_pins_a
- Modify the u-boot config: 
  - CONFIG_CONS_INDEX = 2 (switches to UART1)
  - CONFIG_MMC0_CD_PIN="" (enables booting via SDcard)

As an example, using the sun8i-a33-sinlinx-sina33.dts definition from u-boot (see below for compilation), you can apply this patch:

```diff
diff --git a/arch/arm/dts/sun8i-a33-sinlinx-sina33.dts b/arch/arm/dts/sun8i-a33-sinlinx-sina33.dts
index 541acb4d2b..f019fa2a61 100644
--- a/arch/arm/dts/sun8i-a33-sinlinx-sina33.dts
+++ b/arch/arm/dts/sun8i-a33-sinlinx-sina33.dts
@@ -54,7 +54,7 @@
        compatible = "sinlinx,sina33", "allwinner,sun8i-a33";
 
        aliases {
-               serial0 = &uart0;
+               serial0 = &uart1;
        };
 
        chosen {
@@ -276,9 +276,9 @@
        };
 };
 
-&uart0 {
+&uart1 {
        pinctrl-names = "default";
-       pinctrl-0 = <&uart0_pins_b>;
+       pinctrl-0 = <&uart1_pins_a>;
        status = "okay";
 };
```

# Mainline U-Boot and Kernel

Allwinner A33 has support for both legacy and mainline U-Boot and kernels. This section focuses on mainline compilation.

## Mainline U-BOOT

Obtain the mainline u-boot source code, create a patch file with [this content](#U-Boot-Patch) and call it ``pocketgo-s30.patch``
```console
$ git clone git://git.denx.de/u-boot.git
$ cd u-boot
$ patch -p0 < pocketgo-s30.patch 
$ make Sinlinx_SinA33_defconfig
$ make CROSS_COMPILE=arm-linux-gnueabihf- -j17 
```
Note: change the -j17 to your number of cores+1 (e.g. 8 cores -> -j9)

The build should generate a ``u-boot-sunxi-with-spl.bin`` u-boot file that can be used via USB with sunxi-fel or flashed to a SDCard 

Once u-boot is compiled, you can either boot via tethered USB or flashed SD card.

### USB (sunxi-fel) BOOT

Sunxi-fel is part of the sunxi-tools package, you can install it by checking out their repo: ``https://github.com/linux-sunxi/sunxi-tools``.

After ``sunxi-fel`` is installed you need to put the console in FEL mode. The Pocket-Go S30 does not have a dedicated recovery/reset button, so you can use the [fel-sdboot.sunxi](https://github.com/linux-sunxi/sunxi-tools/blob/master/bin/fel-sdboot.sunxi) image and flash it to a SDcard with:

```console
$ get https://github.com/linux-sunxi/sunxi-tools/raw/master/bin/fel-sdboot.sunxi
$ sudo dd if=fel-sdboot.sunxi of=/dev/sdX bs=1024 seek=8
```
Note: replace sdX with your sdcard device (you can find it with fdisk -l)

Power down the Pocket-Go S30, insert the new SDCARD that you flashed with ``fel-sdboot.sunxi`` in it, and connect your UART to your compture.

Then you can boot the console via USB:

```console
sunxi-fel -v -p uboot u-boot-sunxi-with-spl.bin
```

If you have UART connected, you will see the following output:

```console
U-Boot SPL 2022.01-rc2-dirty (Nov 23 2021 - 16:07:52 +0000)
DRAM: 512 MiB
Trying to boot from FEL


U-Boot 2022.01-rc2-dirty (Nov 23 2021 - 16:07:52 +0000) Allwinner Technology

CPU:   Allwinner A33 (SUN8I 1667)
Model: PocketGo S30
DRAM:  512 MiB
WDT:   Not starting watchdog@1c20ca0
MMC:   mmc@1c0f000: 0, mmc@1c11000: 1
Loading Environment from FAT... Unable to use mmc 0:2... Setting up a 320x480 lcd console (overscan 0x0)
In:    serial
Out:   vidconsole
Err:   vidconsole
Allwinner mUSB OTG (Peripheral)
Net:   eth0: usb_ether
starting USB...
Bus usb@1c1a000: USB EHCI 1.00
Bus usb@1c1a400: USB OHCI 1.0
scanning bus usb@1c1a000 for devices... 1 USB Device(s) found
scanning bus usb@1c1a400 for devices... 1 USB Device(s) found
       scanning usb for storage devices... 0 Storage Device(s) found
Hit any key to stop autoboot:  0 
```

### SDCARD BOOT

Another option is to flash the compiled u-boot (``u-boot-sunxi-with-spl.bin``) to the sdcard with:

```console
sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8
``` 
Note: replace sdX with your sdcard device (you can find it with fdisk -l)

Power down the Pocket-Go S30, insert the new SDCARD that you flashed with ``u-boot-sunxi-with-spl.bin``. Connect your UART and turn on your console. You will see something like:

<details>
  <summary>PocketGo S30 CFW Boot - Click to expand!</summary>
  
```console
U-Boot SPL 2022.01-rc2-00024-g3144ba23bf-dirty (Nov 20 2021 - 04:03:00 +0000)
DRAM: 512 MiB
Trying to boot from MMC1


U-Boot 2022.01-rc2-00024-g3144ba23bf-dirty (Nov 20 2021 - 04:03:00 +0000) Allwinner Technology

CPU:   Allwinner A33 (SUN8I 1667)
Model: PocketGo S30
DRAM:  512 MiB
WDT:   Not starting watchdog@1c20ca0
MMC:   mmc@1c0f000: 0, mmc@1c11000: 1
Loading Environment from FAT... *** Warning - bad CRC, using default environment

Setting up a 1024x600 lcd console (overscan 0x0)
In:    serial
Out:   vidconsole
Err:   vidconsole
Allwinner mUSB OTG (Peripheral)
Net:   eth0: usb_ether
starting USB...
Bus usb@1c1a000: USB EHCI 1.00
Bus usb@1c1a400: USB OHCI 1.0
scanning bus usb@1c1a000 for devices... 1 USB Device(s) found
scanning bus usb@1c1a400 for devices... 1 USB Device(s) found
       scanning usb for storage devices... 0 Storage Device(s) found
Hit any key to stop autoboot:  0 
=> setenv bootargs console=ttyS0,115200 earlyprintk=serial,ttyS1,115200 debug loglevel=7 rootwait root=/dev/mmcblk0p1
=> load mmc 0 0x43000000 boot/sun8i-a33-olinuxino.dtb
21702 bytes read in 3 ms (6.9 MiB/s)
=> load mmc 0 0x42000000 boot/zImage                      
4087568 bytes read in 172 ms (22.7 MiB/s)
=> bootz 0x42000000 - 0x43000000
Kernel image @ 0x42000000 [ 0x000000 - 0x3e5f10 ]
## Flattened Device Tree blob at 43000000
   Booting using the fdt blob at 0x43000000
   Using Device Tree in place at 43000000, end 430084c5

  
Starting kernel ...

[    0.000000] Booting Linux on physical CPU 0x0
[    0.000000] Linux version 5.0.0 (acmeplus@endymion) (gcc version 6.3.1 20170404 (Linaro GCC 6.3-2017.05)) #3 SMP Fri Nov 26 1
[    0.000000] CPU: ARMv7 Processor [410fc075] revision 5 (ARMv7), cr=10c5387d
[    0.000000] CPU: div instructions available: patching division code
[    0.000000] CPU: PIPT / VIPT nonaliasing data cache, VIPT aliasing instruction cache
[    0.000000] OF: fdt: Machine model: Olimex A33-OLinuXino
[    0.000000] printk: bootconsole [earlycon0] enabled
[    0.000000] Memory policy: Data cache writealloc
[    0.000000] cma: Reserved 16 MiB at 0x5d000000
[    0.000000] psci: probing for conduit method from DT.
[    0.982066] hub 1-0:1.0: 1 port detected
[    0.986741] ohci-platform 1c1a400.usb: Generic Platform OHCI controller
[    0.993386] ohci-platform 1c1a400.usb: new USB bus registered, assigned bus number 2
[    1.001344] ohci-platform 1c1a400.usb: irq 27, io mem 0x01c1a400
[    1.076061] hub 2-0:1.0: USB hub found
[    1.079842] hub 2-0:1.0: 1 port detected
[    1.084569] usb_phy_generic usb_phy_generic.0.auto: usb_phy_generic.0.auto supply vcc not found, using dummy regulator
[    1.095330] usb_phy_generic usb_phy_generic.0.auto: Linked as a consumer to regulator.0
[    1.103660] musb-hdrc musb-hdrc.1.auto: MUSB HDRC host driver
[    1.109404] musb-hdrc musb-hdrc.1.auto: new USB bus registered, assigned bus number 3
[    1.118413] hub 3-0:1.0: USB hub found
[    1.122213] hub 3-0:1.0: 1 port detected
[    1.127172] sun8i-a33-pinctrl 1c20800.pinctrl: 1c20800.pinctrl supply vcc-pf not found, using dummy regulator
[    1.137291] sunxi-mmc 1c0f000.mmc: Linked as a consumer to regulator.1
[    1.144328] sunxi-mmc 1c0f000.mmc: Got CD GPIO
[    1.174253] sunxi-mmc 1c0f000.mmc: initialized, max. request size: 16384 KB
[    1.181508] simple-framebuffer 5e000000.framebuffer: Linked as a consumer to regulator.6
[    1.189675] simple-framebuffer 5e000000.framebuffer: framebuffer at 0x5e000000, 0x96000 bytes, mapped to 0x(ptrval)
[    1.200136] simple-framebuffer 5e000000.framebuffer: format=x8r8g8b8, mode=320x480x32, linelength=1280
[    1.212411] Console: switching to colour frame buffer device 40x30
[    1.221094] simple-framebuffer 5e000000.framebuffer: fb0: simplefb registered!
[    1.228459] sun6i-rtc 1f00000.rtc: setting system clock to 1970-01-01T00:00:14 UTC (14)
[    1.236793] ALSA device list:
[    1.239759]   No soundcards found.
[    1.244068] Waiting for root device /dev/mmcblk0p1...
[    1.278480] mmc0: host does not support reading read-only switch, assuming write-enable
[    1.288323] mmc0: new high speed SDXC card at address 59b4
[    1.295033] mmcblk0: mmc0:59b4       58.2 GiB 
[    1.301732]  mmcblk0: p1
[    1.336164] EXT4-fs (mmcblk0p1): mounted filesystem with ordered data mode. Opts: (null)
[    1.344364] VFS: Mounted root (ext4 filesystem) readonly on device 179:1.
[    1.351730] devtmpfs: mounted
[    1.355798] Freeing unused kernel memory: 1024K
[    1.381592] Run /sbin/init as init process
[    1.455036] random: fast init done
[    1.458519] EXT4-fs (mmcblk0p1): re-mounted. Opts: (null)
Starting syslogd: OK
Starting klogd: OK
Running sysctl: OK
Initializing random number generator: OK
Saving random seed: [    1.566874] random: dd: uninitialized urandom read (512 bytes read)
OK
Starting network: OK

Welcome to A33 OLinuXino!
A33-olinuxino login: root
```
</details>


## KERNEL


## Additional info/files

### U-Boot Patch

<details>
  <summary>Mainline U-Boot patch</summary>
  
```diff
diff --git a/arch/arm/dts/Makefile b/arch/arm/dts/Makefile
index cc34da7bd8..cccb4e18e7 100644
--- a/arch/arm/dts/Makefile
+++ b/arch/arm/dts/Makefile
@@ -598,6 +598,7 @@ dtb-$(CONFIG_MACH_SUN8I_A33) += \
        sun8i-a33-olinuxino.dtb \
        sun8i-a33-q8-tablet.dtb \
        sun8i-a33-sinlinx-sina33.dtb \
+       sun8i-a33-pocketgo-s30.dtb \
        sun8i-r16-bananapi-m2m.dtb \
        sun8i-r16-nintendo-nes-classic-edition.dtb \
        sun8i-r16-parrot.dtb
diff --git a/arch/arm/dts/sun8i-a33-olinuxino.dts b/arch/arm/dts/sun8i-a33-olinuxino.dts
index a1a1eb64ca..7ec00a5bea 100644
--- a/arch/arm/dts/sun8i-a33-olinuxino.dts
+++ b/arch/arm/dts/sun8i-a33-olinuxino.dts
@@ -52,7 +52,7 @@
        compatible = "olimex,a33-olinuxino","allwinner,sun8i-a33";
 
        aliases {
-               serial0 = &uart0;
+               serial0 = &uart1;
        };
 
        chosen {
@@ -205,9 +205,9 @@
        status = "okay";
 };
 
-&uart0 {
+&uart1 {
        pinctrl-names = "default";
-       pinctrl-0 = <&uart0_pins_b>;
+       pinctrl-0 = <&uart1_pins_a>;
        status = "okay";
 };
 
diff --git a/configs/Sinlinx_SinA33_defconfig b/configs/Sinlinx_SinA33_defconfig
index 4eb5300b04..58d80a7f6f 100644
--- a/configs/Sinlinx_SinA33_defconfig
+++ b/configs/Sinlinx_SinA33_defconfig
@@ -5,8 +5,9 @@ CONFIG_SPL=y
 CONFIG_MACH_SUN8I_A33=y
 CONFIG_DRAM_CLK=552
 CONFIG_DRAM_ZQ=15291
-CONFIG_MMC0_CD_PIN="PB4"
+CONFIG_MMC0_CD_PIN=""
 CONFIG_MMC_SUNXI_SLOT_EXTRA=2
+CONFIG_SPL_SPI_SUNXI=y
 CONFIG_USB0_ID_DET="PH8"
 CONFIG_VIDEO_LCD_MODE="x:1024,y:600,depth:18,pclk_khz:66000,le:90,ri:160,up:3,lo:127,hs:70,vs:20,sync:3,vmode:0"
 CONFIG_VIDEO_LCD_DCLK_PHASE=0
@@ -20,3 +21,4 @@ CONFIG_USB_EHCI_HCD=y
 CONFIG_USB_OHCI_HCD=y
 CONFIG_USB_MUSB_GADGET=y
 CONFIG_USB_FUNCTION_MASS_STORAGE=y
+CONFIG_CONS_INDEX=2
```

</details>
  

### OFW dmesg output

<details>
  <summary>PocketGo S30 OFW dmesg output - Click to expand!</summary>
  
```console
[    0.000000] Booting Linux on physical CPU 0
[    0.000000] Linux version 3.4.39 (jf@jf) (gcc version 6.4.1 (OpenWrt/Linaro GCC 6.4-2017.11 2017-11) ) #154 SMP PREEMPT Mon Jan 25 07:18:08 UTC 2021
[    0.000000] CPU: ARMv7 Processor [410fc075] revision 5 (ARMv7), cr=10c5387d
[    0.000000] CPU: PIPT / VIPT nonaliasing data cache, VIPT aliasing instruction cache
[    0.000000] Machine: sun8i
[    0.000000] Initialized persistent memory from 43080800-430907ff
[    0.000000] cma: CMA: reserved 128 MiB at 58000000
[    0.000000] Memory policy: ECC disabled, Data cache writealloc
[    0.000000] On node 0 totalpages: 131072
[    0.000000] free_area_init_node: node 0, pgdat c09a8240, node_mem_map c0aea000
[    0.000000]   Normal zone: 1152 pages used for memmap
[    0.000000]   Normal zone: 0 pages reserved
[    0.000000]   Normal zone: 129920 pages, LIFO batch:31
[    0.000000] script_init enter!
[    0.000000] script_init exit!
[    0.000000] PERCPU: Embedded 7 pages/cpu @c0f7a000 s6592 r8192 d13888 u32768
[    0.000000] pcpu-alloc: s6592 r8192 d13888 u32768 alloc=8*4096
[    0.000000] pcpu-alloc: [0] 0 [0] 1 [0] 2 [0] 3 
[    0.000000] Built 1 zonelists in Zone order, mobility grouping on.  Total pages: 129920
[    0.000000] Kernel command line: console= init=/sbin/init disp_para=100 ion_cma_list=32m,64m,128m boot_type=1 loglevel=0 partitions= initcall_debug=0 fb_base=0x41800000
[    0.000000] PID hash table entries: 2048 (order: 1, 8192 bytes)
[    0.000000] Dentry cache hash table entries: 65536 (order: 6, 262144 bytes)
[    0.000000] Inode-cache hash table entries: 32768 (order: 5, 131072 bytes)
[    0.000000] Memory: 512MB = 512MB total
[    0.000000] Memory: 376396k/376396k available, 147892k reserved, 0K highmem
[    0.000000] Virtual kernel memory layout:
[    0.000000]     vector  : 0xffff0000 - 0xffff1000   (   4 kB)
[    0.000000]     fixmap  : 0xfff00000 - 0xfffe0000   ( 896 kB)
[    0.000000]     vmalloc : 0xe0800000 - 0xff000000   ( 488 MB)
[    0.000000]     lowmem  : 0xc0000000 - 0xe0000000   ( 512 MB)
[    0.000000]     pkmap   : 0xbfe00000 - 0xc0000000   (   2 MB)
[    0.000000]     modules : 0xbf000000 - 0xbfe00000   (  14 MB)
[    0.000000]       .text : 0xc0008000 - 0xc04ff000   (5084 kB)
[    0.000000]       .init : 0xc04ff000 - 0xc08a79c0   (3747 kB)
[    0.000000]       .data : 0xc08a8000 - 0xc09a8f80   (1028 kB)
[    0.000000]        .bss : 0xc09a9e00 - 0xc0ae9150   (1277 kB)
[    0.000000] Preemptible hierarchical RCU implementation.
[    0.000000] NR_IRQS:416
[    0.000000] Architected local timer running at 24.00MHz.
[    0.000000] Switching to timer-based delay loop
[    0.000000] sched_clock: 32 bits at 24MHz, resolution 41ns, wraps every 178956ms
[    0.000000] Console: colour dummy device 80x30
[    0.000185] Calibrating delay loop (skipped), value calculated using timer frequency.. 4800.00 BogoMIPS (lpj=24000000)
[    0.000204] pid_max: default: 4096 minimum: 301
[    0.000340] Security Framework initialized
[    0.000416] Mount-cache hash table entries: 512
[    0.001257] CPU: Testing write buffer coherency: ok
[    0.001485] CPU0: thread -1, cpu 0, socket 0, mpidr 80000000
[    0.001496] [sunxi_smp_prepare_cpus] enter
[    0.001533] Setting up static identity map for 0x403b39e8 - 0x403b3a40
[    0.006150] CPU1: Booted secondary processor
[    0.006214] CPU1: thread -1, cpu 1, socket 0, mpidr 80000001
[    0.010000] CPU2: Booted secondary processor
[    0.010000] CPU2: thread -1, cpu 2, socket 0, mpidr 80000002
[    0.010334] CPU3: Booted secondary processor
[    0.010334] CPU3: thread -1, cpu 3, socket 0, mpidr 80000003
[    0.010446] Brought up 4 CPUs
[    0.010446] SMP: Total of 4 processors activated (19200.00 BogoMIPS).
[    0.014036] VFP support v0.3: implementor 41 architecture 2 part 30 variant 7 rev 5
[    0.014413] pinctrl core: initialized pinctrl subsystem
[    0.024343] NET: Registered protocol family 16
[    0.024859] DMA: preallocated 2048 KiB pool for atomic coherent allocations
[    0.024859] script_sysfs_init success
[    0.024859] sunxi_dump_init success
[    0.024859] gpiochip_add: registered GPIOs 0 to 383 on device: sunxi-pinctrl
[    0.024859] sunxi-pinctrl sunxi-pinctrl: initialized sunXi PIO driver
[    0.024859] gpiochip_add: registered GPIOs 1024 to 1031 on device: axp-pinctrl
[    0.024859] persistent_ram: uncorrectable error in header
[    0.024859] persistent_ram: no valid data in buffer (sig = 0xffffffff)
[    0.024859] console [ram-1] enabled
[    0.024859] [sunxi-module]: [sunxi-module.0] probe success
[    0.024859] script config pll3 to 297 Mhz
[    0.024859] script config pll4 to 300 Mhz
[    0.024859] script config pll6 to 600 Mhz
[    0.024859] script config pll8 to 408 Mhz
[    0.024859] script config pll9 to 480 Mhz
[    0.024859] script config pll10 to 297 Mhz
[    0.024859] sunxi_default_clk_init
[    0.024859] try to set pll6ahb1 to 200000000
[    0.024859] try to set ahb clk source to pll6ahb1
[    0.024859] set ahb clk source to pll6ahb1
[    0.024859] try to set ahb1 to 200000000
[    0.024859] try to set apb1 to 100000000
[    0.030232] bio: create slab <bio-0> at 0
[    0.030373] [ARISC] :sunxi-arisc driver v1.02
[    0.030573] [ARISC WARING] :sys_config.fex have no arisc s_uart0 config!
[    0.030584] [ARISC WARING] :sys_config.fex have no arisc s_jtag0 config!
[    0.030617] [ARISC WARING] :sys_config.fex have no arisc s_uart0 config!
[    0.045329] [ARISC] :arisc version: [v0.1.33]
[    0.051823] [ARISC] :sunxi-arisc driver v1.02 startup succeeded
[    0.051963] pwm module init!
[    0.053390] SCSI subsystem initialized
[    0.053390] usbcore: registered new interface driver usbfs
[    0.053390] usbcore: registered new interface driver hub
[    0.053390] usbcore: registered new device driver usb
[    0.053390] twi_chan_cfg()348 - [twi0] has no twi_regulator.
[    0.053390] twi_chan_cfg()348 - [twi1] has no twi_regulator.
[    0.053390] twi_chan_cfg()332 - [twi2] has no twi_used!
[    0.053390] [axp22_init_chip] read axp22 ic type=0x6
[    0.060352] current_limit = 1050000
[    0.060599] Advanced Linux Sound Architecture Driver Version 1.0.25.
[    0.060908] Switching to clocksource arch_sys_counter
[    0.061214] FS-Cache: Loaded
[    0.061334] CacheFiles: Loaded
[    0.066484] hci: ERR: get ehci1 abh clk failed.
[    0.066493] hci: ERR: clock_init failed
[    0.066510] hci: ERR: get ohci1 abh clk failed.
[    0.066517] hci: ERR: clock_init failed
[    0.067819] NET: Registered protocol family 2
[    0.068071] IP route cache hash table entries: 4096 (order: 2, 16384 bytes)
[    0.068597] TCP established hash table entries: 16384 (order: 5, 131072 bytes)
[    0.068841] TCP bind hash table entries: 16384 (order: 5, 196608 bytes)
[    0.069114] TCP: Hash tables configured (established 16384 bind 16384)
[    0.069124] TCP: reno registered
[    0.069398] NET: Registered protocol family 1
[    0.319482] [pm]aw_pm_init!
[    0.319508] standby_mode = 1. 
[    0.319515] wakeup src cnt is : 2. 
[    0.319527] [exstandby]leave extended_standby_enable_wakeup_src : event 0x800000
[    0.319537] [exstandby]leave extended_standby_enable_wakeup_src : wakeup_gpio_map 0x80
[    0.319547] [exstandby]leave extended_standby_enable_wakeup_src : wakeup_gpio_group 0x0
[    0.319557] [exstandby]leave extended_standby_enable_wakeup_src : event 0x800000
[    0.319567] [exstandby]leave extended_standby_enable_wakeup_src : wakeup_gpio_map 0x280
[    0.319577] [exstandby]leave extended_standby_enable_wakeup_src : wakeup_gpio_group 0x0
[    0.319621] sunxi_reg_init enter
[    0.320375] audit: initializing netlink socket (disabled)
[    0.320424] type=2000 audit(0.320:1): initialized
[    0.321314] squashfs: version 4.0 (2009/01/31) Phillip Lougher
[    0.321376] jffs2: version 2.2. (NAND) (SUMMARY)  © 2001-2006 Red Hat, Inc.
[    0.321575] msgmni has been set to 991
[    0.322604] io scheduler noop registered
[    0.322616] io scheduler deadline registered
[    0.322683] io scheduler cfq registered (default)
[    0.322707] [DISP]disp_module_init
[    0.324099] [DISP] parser_disp_init_para,line:160:    fetch script data disp_init.screen2_output_type fail
[    0.324116] [DISP] parser_disp_init_para,line:177:    fetch script data disp_init.screen2_output_mode fail
[    0.324140] [DISP] parser_disp_init_para,line:238:    fetch script data disp_init.fb2_format fail
[    0.324153] [DISP] parser_disp_init_para,line:243:    fetch script data disp_init.fb2_scaler_mode_enable fail
[    0.324167] [DISP] parser_disp_init_para,line:248:    fetch script data disp_init.fb2_width fail
[    0.324181] [DISP] parser_disp_init_para,line:253:    fetch script data disp_init.fb2_height fail
[    0.403912] [DISP]disp_module_init finish
[    0.404064] sw_uart_get_devinfo()2038 - uart1 has no uart_regulator.
[    0.404081] sw_uart_get_devinfo()2008 - get uart4's usedcfg failed
[    0.404092] sw_uart_get_devinfo()2052 - get s_uart0 usedcfg failed
[    0.404412] uart1: ttyS1 at MMIO 0x1c28400 (irq = 33) is a SUNXI
[    0.404423] sw_uart_pm()1296 - uart1 clk is already enable
[    0.408245] loop: module loaded
[    0.408993] sunxi_spi_chan_cfg()1389 - [spi-0] has no spi_regulator.
[    0.409492] spi spi0: master is unqueued, this is deprecated
[    0.409603] m25p_probe()1167 - Use the Dual Mode Read.
[    0.409774] m25p80 spi0.0: found zb25q64, expected w25q128
[    0.409791] m25p80 spi0.0: zb25q64 (8192 Kbytes)
[    0.410646] Creating 6 MTD partitions on "spi0.0":
[    0.410667] 0x000000000000-0x000000060000 : "uboot"
[    0.411539] 0x000000060000-0x000000080000 : "env"
[    0.412305] 0x000000080000-0x0000007a0000 : "boot"
[    0.412994] 0x0000007a0000-0x0000007c0000 : "res"
[    0.413667] 0x0000007c0000-0x000000800000 : "boot_logo"
[    0.414344] 0x000000800000-0x000000800000 : "UDISK"
[    0.414354] mtd: partition "UDISK" is out of reach -- disabled
[    0.415090] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[    0.415172] ohci_hcd: USB 1.1 'Open' Host Controller (OHCI) Driver
[    0.415223] Initializing USB Mass Storage driver...
[    0.415300] usbcore: registered new interface driver usb-storage
[    0.415310] USB Mass Storage support registered.
[    0.415351] usbcore: registered new interface driver ums-alauda
[    0.415393] usbcore: registered new interface driver ums-datafab
[    0.415429] usbcore: registered new interface driver ums-freecom
[    0.415465] usbcore: registered new interface driver ums-isd200
[    0.415501] usbcore: registered new interface driver ums-jumpshot
[    0.415538] usbcore: registered new interface driver ums-karma
[    0.415579] usbcore: registered new interface driver ums-onetouch
[    0.415631] usbcore: registered new interface driver ums-realtek
[    0.415667] usbcore: registered new interface driver ums-sddr09
[    0.415704] usbcore: registered new interface driver ums-sddr55
[    0.415742] usbcore: registered new interface driver ums-usbat
[    0.416063] file system registered
[    0.417376] android_usb gadget: Mass Storage Function, version: 2009/09/11
[    0.417388] android_usb gadget: Number of LUNs=3
[    0.417398]  lun0: LUN: removable file: (no medium)
[    0.417408]  lun1: LUN: removable file: (no medium)
[    0.417417]  lun2: LUN: removable file: (no medium)
[    0.417708] android_usb gadget: android_usb ready
[    0.417937] +++++nbuttons=21
[    0.417981] +++++poll_interval=40
[    0.419155] sunxi-rtc sunxi-rtc: rtc core: registered sunxi-rtc as rtc0
[    0.419207] i2c /dev entries driver
[    0.419406] sunxi cedar version 0.1 
[    0.419455] [cedar]: install start!!!
[    0.419596] [cedar]: install end!!!
[    0.423265] axp22_dcdc3: Failed to create debugfs directory
[    0.429564] sunxi_leds_fetch_sysconfig_para script_parser_fetch "leds_para" leds_used = -1071623036
[    0.429577] =========script_get_err============
[    0.429650] zram: num_devices not specified. Using default: 1
[    0.429657] zram: Creating 1 devices ...
[    0.430213] logger: created 256K log 'log_main'
[    0.430285] logger: created 256K log 'log_events'
[    0.430359] logger: created 256K log 'log_radio'
[    0.430438] logger: created 256K log 'log_system'
[    0.430629] Linux telephony interface: v1.00
[    0.431723] [ audio ] err:try to get audio_pa_en failed!
[    0.431732] request gpio failed!
[    0.431931] [ audio ] err:try to get usb_detect failed!
[    0.431939] [ audio ] err:try to get mic_array_led_ctrl failed!
[    0.437045] asoc: codec-aif1 <-> sunxi-codec mapping ok
[    0.437272] asoc: codec-aif2 <-> sunxi-codec-aif2-dai mapping ok
[    0.437436] asoc: codec-aif3 <-> sunxi-codec-aif2-dai mapping ok
[    0.439503] u32 classifier
[    0.439510]     Actions configured
[    0.439520] Netfilter messages via NETLINK v0.30.
[    0.439540] nf_conntrack version 0.5.0 (7929 buckets, 31716 max)
[    0.439783] ctnetlink v0.93: registering with nfnetlink.
[    0.439823] IPVS: Registered protocols ()
[    0.439872] IPVS: Connection hash table configured (size=4096, memory=32Kbytes)
[    0.440059] IPVS: Creating netns size=840 id=0
[    0.440102] IPVS: ipvs loaded.
[    0.440268] IPv4 over IPv4 tunneling driver
[    0.440700] gre: GRE over IPv4 demultiplexor driver
[    0.440709] ip_gre: GRE over IPv4 tunneling driver
[    0.441188] ip_tables: (C) 2000-2006 Netfilter Core Team
[    0.441334] TCP: cubic registered
[    0.441341] Initializing XFRM netlink socket
[    0.441364] NET: Registered protocol family 17
[    0.441386] NET: Registered protocol family 15
[    0.441510] ThumbEE CPU extension supported.
[    0.441528] Registering SWP/SWPB emulation handler
[    0.441880] [LCD]lcd_module_init
[    0.442312] [LCD]lcd_module_init finish
[    0.443253] otg_wakelock_init: No USB transceiver found
[    0.443300] sunxi-rtc sunxi-rtc: setting system clock to 1970-01-01 00:04:51 UTC (291)
[    0.446421] zkpin_type=0x0
[    0.446430] zkpin_enmap=0x0
[    0.446435] zkpin_dirmap=0x0
[    0.446440] zkpin_pullmap=0x0
[    0.446445] zkpin_levelmap=0x0
[    0.446451] zkswe_init ok!
[    0.446900] ALSA device list:
[    0.446907]   #0: sndcodec
[    0.446995] Warning: unable to open an initial console.
[    0.452530] Freeing init memory: 3744K
[    0.460793] init: could not import file '/init.sun8i.rc' from '/etc/init.rc'
[    0.477231] *******************Try sdio*******************
[    0.478076] mmc0: req failed (CMD5): -110, retrying...
[    0.478913] mmc0: req failed (CMD5): -110, retrying...
[    0.479747] mmc0: req failed (CMD5): -110, retrying...
[    0.480588] *******************Try sd *******************
[    0.504478] mmc0: new high speed SDHC card at address 0001
[    0.504969] mmcblk0: mmc0:0001 SD16G 29.4 GiB 
[    0.506049] mmcblk0: p1
[    0.506517] *******************sd init ok*******************
[    0.547569] *******************Try sdio*******************
[    0.548408] mmc1: req failed (CMD5): -110, retrying...
[    0.549242] mmc1: req failed (CMD5): -110, retrying...
[    0.550080] mmc1: req failed (CMD5): -110, retrying...
[    0.550917] *******************Try sd *******************
[    0.554245] *******************Try mmc*******************
[    0.580870] Mali<2>: Inserting Mali v900 device driver. 
[    0.580885] Mali<2>: Compiled: Jan 25 2021, time: 07:14:04.
[    0.580893] Mali<2>: Driver revision: -09bed8f
[    0.580900] Mali<2>: mali_module_init() registering device
[    0.580944] Mali: ERR: /opt/disk/part1/jf/zkswe_sdk/lichee/linux-3.4/modules/gpu/mali-utgard/kernel_mode/driver/src/devicedrv/mali/linux/mali_platform.c
[    0.580955]            aw_init() 672
[    0.580958]            Failed to get regulator!
[    0.580966] 
[    0.580995] Mali: Set gpu frequency to 384 MHz
[    0.581478] Mali: Mali GPU initialization finished.
[    0.581488] Mali<2>: mali_module_init() registering driver
[    0.581583] Mali<2>: mali_probe(): Called for platform device mali-utgard
[    0.581715] Mali<2>: Mali SWAP: Swap out threshold vaule is 60M
[    0.581814] Mali<2>: Mali memory settings (shared: 0x1F33B000)
[    0.581825] Mali<2>: Using device defined frame buffer settings (0x0012C000@0x58200000)
[    0.581836] Mali<2>: Memory Validator installed for Mali physical address base=0x58200000, size=0x0012C000
[    0.581857] Mali<2>: Mali PM domain: Creating Mali PM domain (mask=0x00001000)
[    0.581885] Mali<2>: Mali PP: Creating Mali PP core: Mali_PP0
[    0.581894] Mali<2>: Mali PP: Base address of PP core: 0x1c48000
[    0.582012] Mali<2>: Found Mali GPU Mali-400 MP r1p1
[    0.582955] Mali<2>: Mali L2 cache: Created Mali_L2:  64K, 4-way, 64byte cache line,  64bit external bus
[    0.582988] Mali<2>: Mali MMU: Creating Mali MMU: Mali_GP_MMU
[    0.583020] Mali<2>: mali_mmu_probe_irq_acknowledge: intstat 0x3
[    0.583029] Mali<2>: Probe: Page fault detect: PASSED
[    0.583036] Mali<2>: Probe: Bus read error detect: PASSED
[    0.583060] Mali<2>: Mali GP: Creating Mali GP core: Mali_GP
[    0.583094] Mali<2>: Mali MMU: Creating Mali MMU: Mali_PP0_MMU
[    0.583114] Mali<2>: mali_mmu_probe_irq_acknowledge: intstat 0x3
[    0.583123] Mali<2>: Probe: Page fault detect: PASSED
[    0.583130] Mali<2>: Probe: Bus read error detect: PASSED
[    0.583147] Mali<2>: Mali PP: Creating Mali PP core: Mali_PP0
[    0.583155] Mali<2>: Mali PP: Base address of PP core: 0x1c48000
[    0.583200] Mali<2>: Mali MMU: Creating Mali MMU: Mali_PP1_MMU
[    0.583220] Mali<2>: mali_mmu_probe_irq_acknowledge: intstat 0x3
[    0.583229] Mali<2>: Probe: Page fault detect: PASSED
[    0.583236] Mali<2>: Probe: Bus read error detect: PASSED
[    0.583251] Mali<2>: Mali PP: Creating Mali PP core: Mali_PP1
[    0.583259] Mali<2>: Mali PP: Base address of PP core: 0x1c4a000
[    0.583287] Mali<2>: 2+0 PP cores initialized
[    0.583303] Mali<2>: Mali GPU Timer: 500
[    0.583312] Mali<2>: Mali GPU Utilization: Utilization handler installed 
[    0.583806] Mali<2>: mali_probe(): Successfully initialized driver for platform device mali-utgard
[    0.583918] Mali: Mali device driver loaded
[    0.586993] android_usb: already disabled
[    0.592614] adb_open
[    0.592639] mtp_bind_config
[    0.592669] ep_matches, wrn: endpoint already claimed, ep(0xc0995454, 0xd6f67a00, ep1in-bulk)
[    0.592683] ep_matches, wrn: endpoint already claimed, ep(0xc0995454, 0xd6f67a00, ep1in-bulk)
[    0.592693] ep_matches, wrn: endpoint already claimed, ep(0xc09954a0, 0xd6f67a00, ep1out-bulk)
[    0.592703] gadget_is_softwinner_otg is not -int
[    0.592709] gadget_is_softwinner_otg is not -int
[    0.592753] adb_bind_config
[    0.592767] ep_matches, wrn: endpoint already claimed, ep(0xc0995454, 0xd6f67a00, ep1in-bulk)
[    0.592779] ep_matches, wrn: endpoint already claimed, ep(0xc09954a0, 0xd6f67a00, ep1out-bulk)
[    0.592791] ep_matches, wrn: endpoint already claimed, ep(0xc0995454, 0xd6f67a00, ep1in-bulk)
[    0.592803] ep_matches, wrn: endpoint already claimed, ep(0xc09954a0, 0xd6f67a00, ep1out-bulk)
[    0.592818] ep_matches, wrn: endpoint already claimed, ep(0xc09954ec, 0xd79112c0, ep2in-bulk)
[    0.608765] *******************Try sdio*******************
[    0.609884] mmc1: req failed (CMD5): -110, retrying...
[    0.610992] mmc1: req failed (CMD5): -110, retrying...
[    0.612101] mmc1: req failed (CMD5): -110, retrying...
[    0.613206] *******************Try sd *******************
[    0.617636] *******************Try mmc*******************
[    0.670608] *******************Try sdio*******************
[    0.672300] mmc1: req failed (CMD5): -110, retrying...
[    0.673970] mmc1: req failed (CMD5): -110, retrying...
[    0.675628] mmc1: req failed (CMD5): -110, retrying...
[    0.677288] *******************Try sd *******************
[    0.683927] *******************Try mmc*******************
[    0.738538] *******************Try sdio*******************
[    0.739653] mmc1: req failed (CMD5): -110, retrying...
[    0.740774] mmc1: req failed (CMD5): -110, retrying...
[    0.741883] mmc1: req failed (CMD5): -110, retrying...
[    0.742992] *******************Try sd *******************
[    0.747433] *******************Try mmc*******************
[    1.347824] android_usb gadget: high-speed config #1: android
[    1.606411] mtp_open
[    4.040116] +++mcu_ts_open
[    4.090039] WRN:L2917(drivers/usb/sunxi_usb/udc/sunxi_udc.c):pdev is null
[    4.110045] +++mcu_ts_close
[    4.160250] +++mcu_ts_open
[    4.230057] +++mcu_ts_close
[    4.460298] +++mcu_ts_open
[   20.912963] adbd (160): /proc/160/oom_adj is deprecated, please use /proc/160/oom_score_adj instead.
[   96.810328] sunxi_i2c_do_xfer()1001 - [i2c0] incomplete xfer (status: 0x48, dev addr: 0x60)
[   96.810592] sunxi_i2c_do_xfer()1001 - [i2c0] incomplete xfer (status: 0x48, dev addr: 0x60)
[   96.810774] twi_start()450 - [i2c0] START can't sendout!
[   96.810785] twi_stop()487 - [i2c0] i2c state isn't idle(0xf8)
[   96.810794] sunxi_i2c_core_process()849 - [i2c0] STOP failed!
```
</details>




