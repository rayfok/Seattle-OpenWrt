# Seattle on OpenWrt

## Flashing OpenWrt
The first task was to install OpenWrt on a TP-LINK WDR
 model router. 

- Updating OpenWrt 
```
cd /tmp wget downloads.openwrt.org/chaos_calmer/15.05/ar71xx/generic/openwrt-15.05-ar71xx-generic-tl-wdr3600-v1-squashfs-factory.bin
mtd -r write /tmp/openwrt-ar71xx-generic-tl-wdr3600-v1-squashfs-factory.bin firmware
```

## Errors I Ran Into
The following are descriptions and screenshots of common issues and/or errors encountered when trying to download OpenWrt/Seattle onto a router. Included are how they were resolved.

![SshLoginError](/Users/RaymondFok/Desktop/Seattle\ Download\ Step\ 3\ Error.png)
* Tried to execute make in OS X terminal. Needed to make in Linux. 

## Installing Seattle

#### 1. Installing OpenWrt SDK 
> Construct a Seattle package for OpenWrt by installing a precompiled OpenWrt toolchain used to cross compile single packages on the system. The code below is intended for the Chaos Calmer release (v. 15.05.1). Make sure to change the directory and file names appropriately for future releases. 

```
cd ~ 
wget downloads.openwrt.org/chaos_calmer/15.05-rc2/ar71xx/generic/OpenWrt-SDK-15.05-rc2-ar71xx-generic_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64.tar.bz2
tar -xvf OpenWrt-SDK-15.05-rc2-ar71xx-generic_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64.tar.bz2
```

#### 2. Downloading Seattle code and Makefile

> Change into the package directory of the SDK, and clone the Seattle code. This code was provided by Xuefeng Huang. It can be accessed at https://github.com/XuefengHuang/seattle-package. Note this binary code is meant to be executed on a machine running Linux. 

```
cd OpenWrt-SDK-15.05-rc2-ar71xx-generic_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64/package
git clone https://github.com/XuefengHuang/seattle-package.git
mv seattle-package seattle
```

#### 3. Building Seattle .ipk

> Return to the SDK directory and execute make. 

```
cd..
make clean && make world
```

#### 4. Copy the package to the router

> Copy the ipk to /tmp in the router. Install the package using OpenWrt's package manager `opkg`. 

```
scp seattle_1.0-1_ar71xx.ipk root@192.168.1.1:/tmp
opkg install /tmp/seattle_1.0-1_ar71xx.ipk 
```



