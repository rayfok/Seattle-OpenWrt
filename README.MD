# Seattle on OpenWrt

## Flashing OpenWrt
The first task was to install OpenWrt on a TP-LINK WDR3600 model router. 

- Updating OpenWrt 
   * ```cd /tmp wget downloads.openwrt.org/chaos_calmer/15.05/ar71xx/generic/openwrt-15.05-ar71xx-generic-tl-wdr3600-v1-squashfs-factory.bin```
   * ```mtd -r write /tmp/openwrt-ar71xx-generic-tl-wdr3600-v1-squashfs-factory.bin firmware```

## Errors I Ran Into
The following are descriptions and screenshots of issues I encountered when trying to download OpenWrt/Seattle onto a router.

- ![SshLoginError](~/Ssh login error.png)

## Installing Seattle

The code snippets are intended for OpenWrt's Chaos Calmer v. 15.05.1. Change the directory and file names appropriately. 

#### 1. Installing OpenWrt SDK 

- ```cd ~ wget downloads.openwrt.org/chaos_calmer/15.05-rc2/ar71xx/generic/OpenWrt-SDK-15.05-rc2-ar71xx-generic_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64.tar.bz2```

- ```tar -xvf OpenWrt-SDK-15.05-rc2-ar71xx-generic_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64.tar.bz2```

- ```cd ~/OpenWrt-SDK-15.05-rc2-ar71xx-generic_gcc-4.8-linaro_uClibc-0.9.33.2.Linux-x86_64```

#### 2. Downloading Seattle code and Makefile

This code was provided by Xuefeng Huang. It can be accessed at https://github.com/XuefengHuang/seattle-package.

- ```git clone github.com/XuefengHuang/seattle-package.git```

- ```mv seattle-package seattle```


