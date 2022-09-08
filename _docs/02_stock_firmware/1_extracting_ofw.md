---
title: Extracting the original firmware
category: Stock Firmware (OFW)
order: 1
label: OFW
---

The first time you run the ```acm-backup-and-install.sh``` script to backup all the nand partitions to your USB.

1. Insert a FAT32 formatted USB drive on the ACM P2 port
2. Run ```acm-backup-and-install.sh``` on your computer
3. The script will finish and the ACM will start dumping the NAND to your USB into a ```backup``` folder.

After then nand is dumped you should see the following partitions on your USB:


| NAME       | START       | END          | FILENAME |    
| ---------- | ----------- | ------------ | --------- |
|boot-res    | 1000000     |  200000      | nanda.img |
|env         | 1200000     |  200000      | nandb.img |
|boot        | 1400000     |  800000      | nandc.img |
|rootfs      | 1c00000     |  12280000    | nandd.img |
|savedata    | 13e80000    |  6400000     | nande.img |
|misc        | 1a280000    |  200000      | nandf.img |
|UDISK       | 1a480000    |  0           | nandg.img |
