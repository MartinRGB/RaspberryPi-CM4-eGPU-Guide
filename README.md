![](https://raw.githubusercontent.com/MartinRGB/RaspberryPi-CM4-eGPU-Guide/main/art/neofetch.png)
## Foreword

Please back up your data before building. There is no guarantee that some tools will still work after installing the kernel patch, so it is recommended that you install the tools before installing the patch.

Since this is a hack, it is inevitable that you will have to reinstall many times. Before starting, I highly recommend reading these articles

**Flash OS**

[How to flash Raspberry Pi OS onto the Compute Module 4 eMMC with usbboot](https://www.jeffgeerling.com/blog/2020/how-flash-raspberry-pi-os-compute-module-4-emmc-usbboot)

**Building Kernel**

[building-the-kernel-locally](https://www.raspberrypi.com/documentation/computers/linux_kernel.html#building-the-kernel-locally)

[Raspberry Pi Linux Cross-compilation Environment](https://github.com/geerlingguy/raspberry-pi-pcie-devices/tree/master/extras/cross-compile)

[cross-compile](https://github.com/geerlingguy/raspberry-pi-pcie-devices/tree/master/extras/cross-compile)

## Requirements:

**PC**:
  - A Linux(Ubuntu/Debian) PC,flash os & cross-compile on it

**CM4 Core Board**:
  - [Compute Module 4](https://www.raspberrypi.com/products/compute-module-4/?variant=raspberry-pi-cm4001000)
    
**CM4 IO Board**(I've tried both of the following):
  - [Compute Module 4 IO Board](https://www.raspberrypi.com/products/compute-module-4-io-board/)
    - You need n 15-Pin SATA Male to 4-Pin Floppy FDD Female like [this](https://www.amazon.co.uk/KALEA-INFORMATIQUE-Floppy-Molex-POWER-cable/dp/B07Z9H9ZLZ) to power Pi.
  - [CM4-IO-BASE-A](https://www.waveshare.com/wiki/)
    
I recommend this one because it has an external power connector and you can use an ATX power supply to power both GPU & Pi

**Adapter**:
  - PCI-E 1x to PCI-E 16x riser(for Official CM4 IO Board)
  - M.2 to PCI-E 16x riser(for CM4-IO-BASE-A)

**ATX PSU**：
  - Modular/Rated Power 500W+(Because I used some high-powered graphics cards)

Do not use cell phone chargers or direct USB/USBC power, they are undervoltage. Please use 5V3A/5V4A power adapter

**Raspberry Pi OS Image**:
  -Kernel 5.1 - [2022-01-28-raspios-bullseye-arm64-full.zip](http://downloads.raspberrypi.org/raspios_full_arm64/images/raspios_full_arm64-2022-01-28/)
  -Or Kernel 6.1 - newest RPI OS Img,download from [official download page](https://www.raspberrypi.com/software/operating-systems/)

After testing, if you use the 64 bit image from 2023-05-03 (kernel version 6.1) as a base and then add the Radeon driver, the performance is better than the 2022 (kernel version 5.1) package mentioned in JeefGeerling's article

the kernel 6.1 image info:
```
Raspberry Pi OS with desktop
Release date: May 3rd 2023
System: 64-bit
Kernel version: 6.1
Debian version: 11 (bullseye)
Size: 818MB
```

now let's start.

## I.Compile Kernel
#### 1.in your PC Linux system
```
sudo apt install git bc bison flex libssl-dev make 
sudo apt-get install gcc-aarch64-linux-gnu gh
```
#### 2.clone the project & check out jeffgeerling's brach:
```
git clone https://github.com/Coreforge/linux.git
gh auth login
gh pr checkout 1
```

```bash
## Which should be the base repository (used for e.g. querying issues) for this directory? 
Coreforge/linux
```

#### 3.compile(need select `ATI Radeon` Module in config)

```
cd linux
sudo apt install libncurses-dev
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- bcm2711_defconfig
make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- menuconfig
```

`Device Drivers` > `Graphics support` > `ATI Radeon` > `Press M` > `Save` > `Exit`
![](https://raw.githubusercontent.com/MartinRGB/RaspberryPi-CM4-eGPU-Guide/main/art/menuconfig.png)

then(I used j16 because I got 10900k(16 threads) and 11900k(20 threads)),
```
make -j16 ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- Image modules dtbs
```

While you are compiling your code, you can install the image(See Part II).

## II.Install Image & Edit the boot config

#### 1.install RPI OS img

#### 2.backup the boot drive & edit config in `/boot`.

add these into `/boot/config.txt`
```
# Enable the USB 2.0 ports on CM4
dtoverlay=dwc2,dr_mode=host
```
referenced from this article —— [USB 2.0 ports not working on the Compute Module 4? Check your overlays!
](https://www.jeffgeerling.com/blog/2020/usb-20-ports-not-working-on-compute-module-4-check-your-overlays)

add thest to `/boot/cmdline.txt`
```bash
radeon.uvd=0 pci=noaer,nomsi radeon.msi=0 radeon.pcie_gen2=0 pcie_aspm=off radeon.aspm=0 radeon.runpm=0 radeon.dpm=0
```

disconnect the USB slave port connection & power, then remove the eMMC Boot disable jumper on J2. 

## III.Init the OS

#### 1.tool preparation and SSH login

first you need enable `SSH` in `Preferences` > `Raspberry Pi Configuration` > `Interface` on Raspberry Pi

pack the tools you need on the computer where you are cross-compiling your code:
```
tar -czvf tools.tar.gz ./tools
```
copy them to raspberrypi

```shell
# default pwd is `raspberry`,default address is `pi@raspberrypi.local`
scp tools.tar.gz pi@raspberrypi.local:~/Desktop
```

SSH login

```
# default pwd is `raspberry`,default address is `pi@raspberrypi.local`
ssh pi@raspberrypi.local
```

unzip in raspberrypi

```
tar -xvzf tools.tar.gz
```

#### 2.Install AMD driver

```
sudo apt-get update
sudo apt update
sudo apt install -y firmware-amd-graphics
```

#### 3.install neofetch

```
# iptables for v2rayA,Since I'm in China, I need v2ray service to download some packages.
sudo apt-get install iptables neofetch
```
#### 4.install v2rayA(optional)

v2ray core
```
curl -Ls https://mirrors.v2raya.org/go.sh | sudo bash
sudo systemctl disable v2ray --now
```

download v2rayA [here](https://github.com/v2rayA/v2rayA/releases/)

```
sudo apt install ./installer_debian_arm64_2.1.3.deb
sudo systemctl start v2raya.service
sudo systemctl enable v2raya.service
```

#### 5.blacklist radeon

```
sudo nano /etc/modprobe.d/blacklist-radeon.conf

# content is: 
blacklist radeon
```

#### 6.memcpy.so

```bash
# Download Coreforge's modified memcpy library.
wget https://gist.githubusercontent.com/Coreforge/91da3d410ec7eb0ef5bc8dee24b91359/raw/1b72d428b2fe1cba459d5ae7f73663483743ff55/memcpy_unaligned.c

# Compile the library and move it into place.
sudo gcc -shared -fPIC -o memcpy.so memcpy_unaligned.c
sudo mv memcpy.so /usr/local/lib/memcpy.so

# Create an `ld.so.preload` file to instruct Linux to use our version of `memcpy`.
sudo nano /etc/ld.so.preload

# Put the following line inside ld.so.preload:
/usr/local/lib/memcpy.so
```
#### 7.Boot to CLI

select Boot to CLI

`Preferences` > `Raspberry Pi Configuration` > `System` > `Boot` > `To CLI`

## IV.Copy compiled kernel

`sudo shutdown -h now` shutdown the Pi

plug in microUSB(USB slave) & connect the jumper,dive into the built linux kernel's path,run:

#### Modules Folder

```
sudo env PATH=$PATH make ARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=/media/${USER}/rootfs modules_install
```

#### kernel img & overlays folder

for `5.1`

```
cp ./arch/arm64/boot/Image /media/${USER}/boot/kernel8.img
cp ./arch/arm64/boot/dts/broadcom/*.dtb /media/${USER}/boot/
cp ./arch/arm64/boot/dts/overlays/*.dtb* /media/${USER}/boot/overlays/
cp ./arch/arm64/boot/dts/overlays/README /media/${USER}/boot/overlays/
```

for `6.1`

```
cp ./arch/arm64/boot/Image /media/${USER}/bootfs/kernel8.img
cp ./arch/arm64/boot/dts/broadcom/*.dtb /media/${USER}/bootfs/
cp ./arch/arm64/boot/dts/overlays/*.dtb* /media/${USER}/bootfs/overlays/
cp ./arch/arm64/boot/dts/overlays/README /media/${USER}/bootfs/overlays/
```

## V.Restart & Load the driver

**disconnect HDMI.**

disconnect microUSB(USB slave) & jumper,reboot,run

```
ssh pi@raspberrypi.local
```

then enable `radeon` module

```bash
# if you want xorg is hardware acceleration,do not run this,radeon driver not works in xorg session,it will become software rendering
sudo modprobe radeon
```

## VI.Benchmark & Utils(optional)

`glmark2`

```
sudo apt install -y meson libjpeg-dev libdrm-dev libgbm-dev libudev-dev
git clone https://github.com/glmark2/glmark2.git
cd glmark2
meson setup build -Dflavors=drm-gl,drm-glesv2
ninja -C build
sudo ninja -C build install
```

run `glmark2-drm`,other options you can refer [ubuntu manuals](https://manpages.ubuntu.com/manpages/focal/man1/glmark2.1.html)

`vkmark`

```
sudo apt install libvulkan-dev cmake libvulkan1 mesa-vulkan-drivers libglm-dev libassimp-dev
meson build
ninja -C build
sudo ninja -C build install
```

`phoronix-test-suite`

``` bash
# How to install the Phoronix Test Suite
# Log into your Ubuntu Server instance and download the Phoronix Test Suite .deb file with:

wget https://phoronix-test-suite.com/releases/repo/pts.debian/files/phoronix-test-suite_10.8.3_all.deb

# Once the file has been downloaded, install the tool with:

sudo dpkg -i phoronix*.deb

# The installation will error out because there are still dependencies to meet. You can fix that error and complete the installation with the single command:

sudo apt-get install -f

# Once all of the dependencies are met, the installation will be complete. You can verify the installation with:

phoronix-test-suite
```

`lm-sensor`

```
sudo apt-get install lm-sensors
sudo sensors-detect
sensors
```

`keyboard layout`

select en US layout.

```
sudo dpkg-reconfigure keyboard-configuration
```

## VII.Change x-session(optional)

#### xfce install(optional)

xfce's software rendering performance is better than LXDE

```
sudo apt-get install xfce4-session xfce4-goodies
sudo update-alternatives --config x-session-manager
```

then

```
sudo update-alternatives --config x-session-manager
```

select xfce session,then `startx`
