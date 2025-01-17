# Real_Time_RT_Programming_in_Linux_Kernel_Build
Real-Time (RT) Programming with Linux (Kernel Build) by Shivam Shah

Use Raspberry Pi Imager or compatible card reader to install 64 - bit OS in SD card.
NOTE: - This will remove all the existing data. Please have a or create a backup of the data.

When the Raspberry Pi Boots up, run the following commands:
```bash
sudo apt-get update
sudo apt-get upgrade
uname -a
```

This will update and upgrade the system and "uname -a" is used to get the kernel information and you will see something like this:
```bash
Linux raspberrypi 6.1.0-rpi8-rpi-v8 #1 SMP PREEMPT Debian 1:6.1.73-1+rpt1 (2024- 01 - 25) aarch64 GNU/Linux
```

Now, you need to install necessary packages and clone the Linux repository:
```bash
sudo apt install git bc bison flex libssl-dev make libncurses5-dev
mkdir kernel
cd kernel/
git clone --depth=1 --branch rpi-6.1.y https://github.com/raspberrypi/linux
```

Now download the patch:
```bash
wget https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/6.1/patch-6.1.77-rt24.patch.gz
```

NOTE: Please check for any error. If you follow same steps, you should not get any problem and patch should apply easily.
```bash
zcat ../patch-6.1.77-rt24.patch.gz | patch -p1 --dry-run
```

Apply the path
```bash
cd linux/
zcat ../patch-6.1.77-rt24.patch.gz | patch -p
```

Configure the kernel environment and customize for PREEMPT_RT
```bash
KERNEL=kernel
make bcm2711_defconfig
```

Now you will see a menu type screen when you use this command and in that, navigate to General -> Preemption model. There select Fully Preemptible Kernel (Real-Time).
```bash
make menuconfig
```
Now, save the .config

Build the kernel. 
NOTE: Compilation will take approximately 1 hour to build.
```bash
make -j4 Image.gz modules dtbs
```

Install the kernel, module, and device tree blobs
```bash
sudo make modules_install
sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/
sudo cp arch/arm64/boot/dts/overlays/.dtb /boot/overlays/
sudo cp arch/arm64/boot/dts/overlays/README /boot/overlays/
sudo cp arch/arm64/boot/Image.gz /boot/kernel8.img
```

Also, copy the kernel to the Raspberry Pi SD card:
```bash
sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/firmware/
sudo cp arch/arm64/boot/dts/overlays/README /boot/firmware/overlays/
sudo cp arch/arm64/boot/dts/overlays/.dtb /boot/firmware/overlays/
sudo cp arch/arm64/boot/Image.gz /boot/firmware/kernel8.img
```

Finally, reboot the system:
```bash
sudo reboot
```

Now, the system is looks like this:
```bash
uname -a
```

Linux raspberrypi 6.1.77-rt24-v8+ #1 SMP PREEMPT_RT Sun Feb 11 12:20:08 PST 2024 aarch64 GNU/Linux
