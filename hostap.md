# Installing hostap in debian (jessie) from source

## 1. Download the source.

## 2. Edit a file
Edit hostap/src/ap/hw_features.c  
Find line: iface->conf->secondary_channel=0 & comment it.

## 3. Install required packages
libnl-3-dev lissl-dev libnl-genl-3 (both dev and normal)

## 4. Change paths
**Debian searches for netlink in /usr/include/ but it is installed in /usr/include/libnl3 so copy netlink from /usr/include/libnl3 to /usr/include/**
*Maybe symlink it*

## 5. Installed missing library of libnl1
**Manually download it libnl-dev_1.1-6 dev & normal**
*OR*
**Copy the libnl.so files from this repo to /usr/lib**

## 6. Other failures
* In case failure of "nl80211" saying "undefined reference bla bla" then uncommenting line "#CONFIG_LIBNL32=y" in .config may help

## 7. Other failures
* Uncomment CONFIG_IEEE80211N=y from .config if you want support for wireless "N" mode.
