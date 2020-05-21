# BJTU-Embedded-Assignment-4

<div align=center>
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d7/Android_robot.svg/170px-Android_robot.svg.png" alt="Android" width="200"/>
  
# Building a custom android kernel
Charles CLAUDE 19129127  
Louison HARIZI 19129047
</div>


## Step 1

Before everything else, we need to install or update all needed packages using following commands:
```bash
sudo apt-get update
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip
sudo apt install gcc-arm-linux-gnueabihf
sudo apt-get install python-mako
```

## Step 2

Once the system up-to-date, we have to get Android sources.
```bash
repo init -u https://github.com/tab-pi/platform_manifest -b nougat
repo sync
```

## Step 3

The step 2 command can take a lot of time.  
Then go into the "kernel/rpi folder".
```bash
cd kernel/rpi
```
And build our kernel with those commands (you have to use bash, tcsh or zsh can make the build fail).
```bash
ARCH=arm scripts/kconfig/merge_config.sh arch/arm/configs/bcm2709_defconfig android/configs/android-base.cfg android/configs/android-recommended.cfg
ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make zImage
ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make dtbs
```

## Step 4

Now we can build our android source with following commands.
```bash
cd ..
cd ..
source build/envsetup.sh
lunch rpi3-eng
make ramdisk systemimage
```
This step can also take a lot of time.

## Running steps
 To run the custom android os on rpi you need to set-up the sd-card partition like following.
- p1 512MB for BOOT
- p2 1024MB for /system
- p3 512MB for /cache
- p4 remainings for /data

Then you can copy custom android OS on the sd-card into the corresponding partition (from their output folder). 
- device/brcm/rpi3/boot/* to p1:/
- kernel/rpi/arch/arm/boot/zImage to p1:/
- kernel/rpi/arch/arm/boot/dts/bcm2710-rpi-3-b.dtb to p1:/
- kernel/rpi/arch/arm/boot/dts/overlays/vc4-kms-v3d.dtbo to p1:/overlays/vc4-kms-v3d.dtbo
- out/target/product/rpi3/ramdisk.img to p1:/

## Running screens

We tried to make it work on our raspberry, but without screen because we don't have required wired to connect it (micro hdmi to mini hdmi).
And unfortunately we were not able to make it work with qemu. We got a black screen with a message "the host didn't choose display yet" everytime.
