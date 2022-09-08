---
title: Compile Batocera image
category: Development
order: 1
---

# Compilation Steps

Follow these steps to compile the firmware.

- Clone our repo:
  - HTTPS: ```git clone https://github.com/acm-CFW/batocera.linux.git```
  - SSH: ```git clone git@github.com:acm-CFW/batocera.linux.git```

- Change your directory to that folder and switch to our ```acm-cfw``` branch:
```
$ cd batocera.linux
$ git checkout acm-cfw
```
- Update submodules (buildroot) and switch to our ```acm-cfw``` branch:
```
git submodule update --init --recursive
git checkout acm-cfw
```

- Build the ACM image with:
```
make acm-build
```

Depending on your system this may take more or less, but it will be about 1-4 hours

Once the build is complete, you should find the generated image in these path:
* ```output/acmimages/batocera/images/acm```

You will find the following files there:
- batocera-sunxi_r16-35-20220907.img.gz
- batocera-sunxi_r16-35-20220907.img.gz.md5
- batocera-sunxi_r16-35-20220907.img.gz.sha256
- batocera.version
- boot.tar.xz
- boot.tar.xz.md5
- boot.tar.xz.sha256

Flash the firmware with your preferred tool (e.g. Balena Etcher). You will need at least a 4GB card, but we recommend a minimum of 16GB. The system will automatically expand the card to use all the space available during the first boot.

