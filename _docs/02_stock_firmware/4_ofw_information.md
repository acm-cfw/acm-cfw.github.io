---
title: OFW Internal Information
category: Stock Firmware (OFW)
order: 4
label: OFW
---

This page includes some internal traces/information about the OFW.

### Bootargs:
```console
sunxi#env print
boot_fastboot=fastboot
boot_normal=fatload sunxi_flash 2 43800000 uImage;bootm 43800000
boot_recovery=fatload sunxi_flash extend 43800000 uImage; bootm 43800000
bootcmd=run setargs_nand boot_normal
bootdelay=3
console=ttyS0,115200
ethact=usb_ether
fastboot_key_value_max=0x8
fastboot_key_value_min=0x2
filesize=119436
init=/sbin/init
loglevel=0
mmc_root=/dev/mmcblk0p7
nand_root=/dev/nandd
nor_root=/dev/mtdblock4
partitions=boot-res@nanda:env@nandb:boot@nandc:rootfs@nandd:savedata@nande:misc@nandf:UDISK@nandg
recovery_key_value_max=0x13
recovery_key_value_min=0x10
setargs_mmc=setenv bootargs console=${console} noinitrd root=${mmc_root} rootfstype=ext4 rootwait init=${init} ion_cma_512m=64m ion_cma_1g=176m ion_carveout_512m=96m ion_carveout_1g=150m cohe
rent_pool=4m loglevel=${loglevel} partitions=${partitions}
setargs_nand=setenv bootargs console=${console} noinitrd root=${nand_root} rootfstype=ext4 rootwait init=${init} ion_cma_512m=64m ion_cma_1g=176m ion_carveout_512m=96m ion_carveout_1g=150m co
herent_pool=4m loglevel=${loglevel} partitions=${partitions}
setargs_nor=setenv bootargs console=${console} noinitrd root=${nor_root} rootfstype=ext4 rootwait init=${init} ion_cma_512m=8m ion_cma_1g=176m ion_carveout_512m=8m ion_carveout_1g=150m cohere
nt_pool=4m loglevel=${loglevel} partitions=${partitions}
stderr=serial
stdin=serial
stdout=serial
sunxi_hardware=sun8i
sunxi_serial=27d78a546042ffffc572
usbnet_devaddr=9a:6a:8b:d1:6a:d1
usbnet_hostaddr=1a:6a:8b:d1:6a:d1

Environment size: 1538/131068 bytes
```

### Fastboot partitions

```console
--------fastboot partitions--------
-total partitions:7-
-name-        -start-       -size-      
boot-res    : 1000000       200000      
env         : 1200000       200000      
boot        : 1400000       800000      
rootfs      : 1c00000       12280000    
savedata    : 13e80000      6400000     
misc        : 1a280000      200000      
UDISK       : 1a480000      0        
-----------------------------------
```

### Normal boot log

```console
[      0.301]

U-Boot 2011.09-rc1 (Oct 05 2020 - 09:32:27) Allwinner Technology 

[      0.308]version: 1.1.0
[      0.311]uboot commit : 8 
ready
normal dc exist, limit to dc
no key input
dram_para_set start
dram_para_set end
In:    Out:   Err:   Net:   Warning: failed to set MAC address

IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0

   Verifying Checksum ... OK
OK
OK
Uncompressing Linux... done, booting the kernel.
init started: BusyBox v1.30.1 (2020-06-04 04:55:27 UTC)
starting pid 72, tty '': '/bin/mount -t proc proc /proc'
starting pid 73, tty '': '/bin/mount -t sysfs sysfs /sys'
starting pid 74, tty '': '/bin/mount -t devtmpfs devtmpfs /dev'
mount: mounting devtmpfs on /dev failed: Device or resource busy
starting pid 75, tty '': '/bin/mount -o remount,ro /'
starting pid 77, tty '': '/bin/mkdir -p /dev/pts'
starting pid 78, tty '': '/bin/mount -t devpts devpts /dev/pts'
starting pid 79, tty '': '/bin/mount -a'
starting pid 84, tty '': '/etc/init.d/rcS'
/etc/init.d/rcS: .: line 12: can't open '/etc/default/rcS': No such file or directory
/dev/nandg: LABEL="UDISK" UUID="b7cb3e71-b8d7-4e54-95d6-509dc6ddd855" TYPE="ext4"
/dev/nandg already format by ext4
/dev/nande: LABEL="savedata" UUID="a6d80992-bd93-475a-b1f3-608f41b1ebd5" TYPE="ext4"
/dev/nande already format by ext4
Starting udev
Loading modules: cp: can't stat '/etc/xr_wifi.conf': No such file or directory
mali 
/etc/init.d/rcS: .: line 11: can't open '/etc/default/rcS': No such file or directory
/etc/init.d/rcS: .: line 3: can't open '/etc/default/rcS': No such file or directory
/etc/init.d/rcS: .: line 13: can't open '/etc/default/rcS': No such file or directory
Populating volatile Filesystems.
Applying /etc/default/volatiles/00_core
Checking for -/run/lock-.
Creating directory -/run/lock-.
Checking for -/var/volatile/log-.
Creating directory -/var/volatile/log-.
Checking for -/var/volatile/tmp-.
Creating directory -/var/volatile/tmp-.
Target already exists. Skipping.
Checking for -/var/lock-.
Creating link -/var/lock- pointing to -/run/lock-.
Checking for -/var/run-.
Creating link -/var/run- pointing to -/run-.
Checking for -/var/tmp-.
Creating link -/var/tmp- pointing to -/var/volatile/tmp-.
Checking for -/tmp-.
Creating link -/tmp- pointing to -/var/tmp-.
rm: can't remove '/tmp': Read-only file system
ln: /tmp/tmp: File exists
Checking for -/var/lock/subsys-.
Creating directory -/var/lock/subsys-.
Checking for -/var/log/wtmp-.
Creating file -/var/log/wtmp-.
Checking for -/var/run/utmp-.
Creating file -/var/run/utmp-.
Checking for -/etc/resolv.conf-.
Creating link -/etc/resolv.conf- pointing to -/var/run/resolv.conf-.
Target already exists. Skipping.
rm: can't remove '/etc/resolv.conf': Read-only file system
ln: /etc/resolv.conf: File exists
Checking for -/var/run/resolv.conf-.
Creating file -/var/run/resolv.conf-.
Checking for -/var/log-.
Creating link -/var/log- pointing to -/var/volatile/log-.
cat: can't open '/var/volatile/tmp/tmp_volatile.84': No such file or directory
cat: can't open '/var/volatile/tmp/tmp_volatile.84': No such file or directory
Applying /var/volatile/tmp/tmp_volatile.84
cat: can't open '/var/volatile/tmp/tmp_volatile.84': No such file or directory
rm: can't remove '/var/volatile/tmp/tmp_volatile.84': No such file or directory
/etc/rcS.d/S38urandom: .: line 18: can't open '/etc/default/rcS': No such file or directory
User Mode:policy=0
[gpio-keys] Does not verify vid and pid
[joypad-1] device: /dev/input/by-path/platform-sunxi-ehci.1-usb-0:1.2:1.0-event-joystick does not exists.
[joypad-2] device: /dev/input/by-path/platform-sunxi-ehci.1-usb-0:1.4:1.0-event-joystick does not exists.
[joypad-1] fd is not valid.
[joypad-2] fd is not valid.
start game.
numid=12,iface=MIXER,name='Lineout volume control'
  ; type=INTEGER,access=rw------,values=1,min=0,max=63,step=0
  : values=40
value is: 0

Configuring network interfaces... ip: SIOCGIFFLAGS: No such device
System time was Thu Jan  1 00:00:03 UTC 1970.
Setting the System Clock using the Hardware Clock as reference...
hwclock: RTC_RD_TIME: Operation not permitted
System Clock set. System local time is now Thu Jan  1 00:00:03 UTC 1970.
Starting syslogd/klogd: done
starting pid 281, tty '/dev/ttyS0': '/sbin/getty 115200 ttyS0'

Vivid-Yocto-Distro V0.1 z7213-astro-pp /dev/ttyS0

z7213-astro-pp login: 
```

### BOOT0

```console
????key_press  
0x00000073 
HELLO! BOOT0 is starting!
boot0 version : 4.2.0
boot0 commit : 8 
fel_flag = 0x00000000
rtc[0] value = 0x00000000
rtc[1] value = 0x00000000
rtc[2] value = 0x00000000
rtc[3] value = 0x00000000
DRAM DRIVE INFO: V1.7
DRAM Type =3 (2:DDR2,3:DDR3,6:LPDDR2,7:LPDDR3)
DRAM zq value: 00003bbbDRAM CLK =600 MHZ
ID CHECK VERSION: V0.1
using ic R16
USE PLL DDR1
USE PLL NORMAL
PLL FREQUENCE = 1200 MHZ
DRAM PLL DDR1 frequency extend open !
DRAM master priority setting ok.
Auto calculate timing parameter!
para_dram_tpr0 = 0047a14f
para_dram_tpr1 = 01c2294c
para_dram_tpr2 = 00069049
tcl = 6,tcwl = 4
DRAM TIMING PARA0 = 0b0f180c
DRAM TIMING PARA1 = 0003040f
DRAM TIMING PARA2 = 0406050a
DRAM TIMING PARA3 = 0000400c
DRAM TIMING PARA4 = 05020405
DRAM TIMING PARA5 = 05050403
DRAM TIMING PARA8 = 90003310
DRAM PHY INTERFACE PARA = 02040102
DRAM VTC is disable
DRAM dynamic DQS/DQ ODT is on
DRAM DQS gate is PD mode.
DRAM one rank training is on,the value is 91003587
DRAM work mode register value = 004318d4
DRAM SIZE =256 M
set one rank ODTMAP
DRAM simple test OK.
dram size =256
========= sw2001_verify ============
rsb_send_initseq: rsb clk 400Khz -> 3Mhz
tableNum = 6
plaint = 000000ed 0000002b 00000007 0000009c 000000b3 0000004b 00000014 000000bf 00000090 00000046 0000007e 0000008c 00000052 000000e7 0000009c 00000035 
result = 000000ed 0000002b 00000007 0000009c 000000b3 0000004b 00000014 000000bf 00000090 00000046 0000007e 0000008c 00000052 000000e7 0000009c 00000035 
sw2001_verify success!
====================================
cid:0x005a7213
NAND_ClkRequest, nand_index: 0x00001000
Reg 0x01c20080: 0x00053dd3
Reg 0x01c20060: 0x00053dc6
Reg 0x01c202c0: 0x00053dc6
NAND_SetClk, nand_index: 0x0000000a
Reg 0x01c20080: 0x00053dd7
NB0 : nand phy init ok
block from 8 to 39
current block is 8 and last block is 39.
current block is 9 and last block is 39.
current block is 10 and last block is 39.
current block is 11 and last block is 39.
current block is 12 and last block is 39.
current block is 13 and last block is 39.
current block is 14 and last block is 39.
sum=61abe206
src_sum=61abe206
The file stored in start block %u is perfect.
Ready to disable icache.
Jump to secend Boot.
[      0.484]
```

### Recovery log

```console
[      0.301]

U-Boot 2011.09-rc1 (Oct 05 2020 - 09:32:27) Allwinner Technology 

[      0.308]version: 1.1.0
[      0.311]uboot commit : 8 
ready
normal dc exist, limit to dc
dram_para_set start
dram_para_set end
[      1.406]DRAM:  256 MiB
relocation Offset is: 07aef000
board.c 622
smcl's set manager is NULL
<axp22, dc1sw>
<axp22, dcdc1>
<axp22, aldo1>
workmode = 0
[      1.512]NAND: NAND_UbootInit
NAND_UbootInit start
NB1 : enter NAND_LogicInit
uboot:nand version: 3 5018 20181107 16671134 
nand : get id_number_ctl fail, 1
uboot:nand info: 9510dcec ffffff56 8c 0 0 
nand : get sorting_flag fail, a
nand : get CapacityLevel fail, 4eb951a1
not burn nand partition table!
NB1 : nftl num: 1 
 init nftl: 0 
NB1 : NAND_LogicInit ok, result = 0x0 
[      2.731]sunxi flash init ok
In:    serial
Out:   serial
Err:   serial
--------fastboot partitions--------
-total partitions:7-
-name-        -start-       -size-      
boot-res    : 1000000       200000      
env         : 1200000       200000      
boot        : 1400000       800000      
rootfs      : 1c00000       12280000    
savedata    : 13e80000      6400000     
misc        : 1a280000      200000      
UDISK       : 1a480000      0           
-----------------------------------
base bootcmd=run setargs_nand boot_normal
bootcmd set setargs_nand
key 0
cant find rcvy value
cant find fstbt value
misc partition found
to be run cmd=run setargs_nand boot_normal
sunxi_serial: sn_filename is not exist
serial is: 27d78a546042ffffc572
Net:   usb_etherWarning: failed to set MAC address

WORK_MODE_BOOT
check_update_key_gpio: 49 gpio_set_one_pin_io_status ret = 0
not goto fel mode! 
board_status_probe
[      2.856]power trigger
gpio_set_one_pin_io_status ret = 0
gpio_read_one_pin_value value = 0
power switch on
sunxi_bmp_logo_display
screen_id =0, screen_width =480, screen_height =800
[      2.947]probe MP tools from boot
delay time 0
[      3.018]usb init ok
usb sof ok
[      3.453]usb probe ok
IRQ: EP0
IRQ: EP0
IRQ: EP0
set address 0x3
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
IRQ: EP0
usb: have no handshake
[      5.491]Hit any key to stop autoboot:  0 
sunxi#
```

### Boot Sequence from U-boot

```console
sunxi#boot
## Booting kernel from Legacy Image at 43800000 ...
   Image Name:   Linux-3.4.113
   Image Type:   ARM Linux Kernel Image (uncompressed)
   Data Size:    3469320 Bytes = 3.3 MiB
   Load Address: 40008000
   Entry Point:  40008000
   Verifying Checksum ... OK
   Loading Kernel Image ... OK
OK
para err in disp_ioctl, cmd = 0xa,screen id = 1
[     66.619][mmc]: MMC Device 2 not found
[     66.622][mmc]:  mmc  not find,so not exit
NAND_UbootExit
NB1 : NAND_LogicExit
nand release dma:0
[     66.627]
Starting kernel ...

Uncompressing Linux... done, booting the kernel.
init started: BusyBox v1.30.1 (2020-06-04 04:55:27 UTC)
starting pid 72, tty '': '/bin/mount -t proc proc /proc'
starting pid 73, tty '': '/bin/mount -t sysfs sysfs /sys'
starting pid 74, tty '': '/bin/mount -t devtmpfs devtmpfs /dev'
mount: mounting devtmpfs on /dev failed: Device or resource busy
starting pid 75, tty '': '/bin/mount -o remount,ro /'
starting pid 77, tty '': '/bin/mkdir -p /dev/pts'
starting pid 78, tty '': '/bin/mount -t devpts devpts /dev/pts'
starting pid 79, tty '': '/bin/mount -a'
starting pid 84, tty '': '/etc/init.d/rcS'
/etc/init.d/rcS: .: line 12: can't open '/etc/default/rcS': No such file or directory
/dev/nandg: LABEL="UDISK" UUID="b7cb3e71-b8d7-4e54-95d6-509dc6ddd855" TYPE="ext4"
/dev/nandg already format by ext4
/dev/nande: LABEL="savedata" UUID="a6d80992-bd93-475a-b1f3-608f41b1ebd5" TYPE="ext4"
/dev/nande already format by ext4
Starting udev
Loading modules: cp: can't stat '/etc/xr_wifi.conf': No such file or directory
mali 
/etc/init.d/rcS: .: line 11: can't open '/etc/default/rcS': No such file or directory
/etc/init.d/rcS: .: line 3: can't open '/etc/default/rcS': No such file or directory
/etc/init.d/rcS: .: line 13: can't open '/etc/default/rcS': No such file or directory
Populating volatile Filesystems.
Applying /etc/default/volatiles/00_core
Checking for -/run/lock-.
Creating directory -/run/lock-.
Checking for -/var/volatile/log-.
Creating directory -/var/volatile/log-.
Checking for -/var/volatile/tmp-.
Creating directory -/var/volatile/tmp-.
Target already exists. Skipping.
Checking for -/var/lock-.
Creating link -/var/lock- pointing to -/run/lock-.
Checking for -/var/run-.
Creating link -/var/run- pointing to -/run-.
Checking for -/var/tmp-.
Creating link -/var/tmp- pointing to -/var/volatile/tmp-.
Checking for -/tmp-.
Creating link -/tmp- pointing to -/var/tmp-.
rm: can't remove '/tmp': Read-only file system
ln: /tmp/tmp: File exists
Checking for -/var/lock/subsys-.
Creating directory -/var/lock/subsys-.
Checking for -/var/log/wtmp-.
Creating file -/var/log/wtmp-.
Checking for -/var/run/utmp-.
Creating file -/var/run/utmp-.
Checking for -/etc/resolv.conf-.
Creating link -/etc/resolv.conf- pointing to -/var/run/resolv.conf-.
Target already exists. Skipping.
rm: can't remove '/etc/resolv.conf': Read-only file system
ln: /etc/resolv.conf: File exists
Checking for -/var/run/resolv.conf-.
Creating file -/var/run/resolv.conf-.
Checking for -/var/log-.
Creating link -/var/log- pointing to -/var/volatile/log-.
cat: can't open '/var/volatile/tmp/tmp_volatile.84': No such file or directory
cat: can't open '/var/volatile/tmp/tmp_volatile.84': No such file or directory
Applying /var/volatile/tmp/tmp_volatile.84
cat: can't open '/var/volatile/tmp/tmp_volatile.84': No such file or directory
rm: can't remove '/var/volatile/tmp/tmp_volatile.84': No such file or directory
/etc/rcS.d/S38urandom: .: line 18: can't open '/etc/default/rcS': No such file or directory
User Mode:policy=0
[gpio-keys] Does not verify vid and pid
[joypad-1] device: /dev/input/by-path/platform-sunxi-ehci.1-usb-0:1.2:1.0-event-joystick does not exists.
[joypad-2] device: /dev/input/by-path/platform-sunxi-ehci.1-usb-0:1.4:1.0-event-joystick does not exists.
[joypad-1] fd is not valid.
[joypad-2] fd is not valid.
start game.
numid=12,iface=MIXER,name='Lineout volume control'
  ; type=INTEGER,access=rw------,values=1,min=0,max=63,step=0
  : values=40
value is: 0

Configuring network interfaces... ip: SIOCGIFFLAGS: No such device
System time was Thu Jan  1 00:01:57 UTC 1970.
Setting the System Clock using the Hardware Clock as reference...
System Clock set. System local time is now Thu Jan  1 00:01:57 UTC 1970.
Starting syslogd/klogd: done
starting pid 280, tty '/dev/ttyS0': '/sbin/getty 115200 ttyS0'

Vivid-Yocto-Distro V0.1 z7213-astro-pp /dev/ttyS0

z7213-astro-pp login: 
```

