---
title: Introduction
category: General
order: 1
label: WIP
---

The Astro City Mini is based on an Allwinner R16 SoC. The graphics are driven by a Mali 400 GPU, and the screen has a native resolution of 480x800 (rotated).

The internal screen is rotated, and while the stock firmware applications have been rotated internally, any new applications  require to be manually rotated.

The custom firmware (CFW) described in these pages installs the necessary bits to allow the system to boot a custom version of batocera from USB. Since the screen is rotated, many of batocera applications have been patched to rotate them to their correct orientation. In addtion to this, the firmware expands the system capability by supporting additional controllers, enabling networking, and of course by adding a significant amount of emulators (retroarch, as well as standalone ones).
