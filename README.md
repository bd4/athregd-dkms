# athregd

Some wireless cards have the region set to 0x0 in firmware, and recent versions
of the Linux kernel (since 5.8?) interpret this as the "global" region 0x64
which is very restrictive and do not allow setting an alternate region. This
basically completely breaks functionality for 5Ghz bands. This affects the
Compex Mini-PCI and Mini-PCIe cards often sold with pcengines ALIX and APU2
boards, which make great wireless access points/routers.

This repository contains DKMS directories for patched version of the drivers,
which adds a `cn=COUNTRY_CODE` argument to the ath kernel module, and allows
the region to be overridden at module load time.

Based on https://gist.github.com/BigNerd95/0be0a5b52a16524a78fc768f0d208a74

The `dkms.conf` only rebuilds ath9k and ath10k, which are the drivers and
hardware I know are affected by this issue. Others can be added, by figuring
out which `.ko` modules are built and adding them to the `dkms.conf`.

# Usage

## Debian 11 (bullseye) 32-bit, for ALIX

As root (or with sudo):
```
apt install dkms linux-headers
cp -R athregd-5.10.56 /usr/src
dkms install --no-depmod -m athregd -v 5.10.46 -k $(uname -r)
echo "options ath cn=US" > /etc/modprobe.d/ath.conf
```

Stop any connections or hostapd daemons running on the interface, then
remove the modules, check that the new module built with dkms is installed,
and re-load the driver module. For example, for ath9k (as root):
```
modprobe -r ath9k ath9k_hw ath ath9k_common
depmod -a
modinfo ath | grep cn # should show a parameter
modprobe ath9k
```

## Ubuntu 20.04 HWE 5.11 64-bit, for APU2

Note: the default kernel for 20.40, 5.4, does not have this bug. However if you
need / want a newer kernel, you can use this method to retain functionality
with the HWE kernel.

As root (or with sudo):
```
apt install dkms linux-image-generic-hwe-20.04 linux-headers-generic-hwe-20.04
cp -R athregd-5.11.22 /usr/src
dkms install --no-depmod -m athregd -v 5.11.22 -k $(uname -r)
echo "options ath cn=US" > /etc/modprobe.d/ath.conf
```

Stop any connections or hostapd daemons running on the interface, then
remove the modules, check that the new module built with dkms is installed,
and re-load the driver module. For example, for ath10k (as root):
```
modprobe -r ath10k ath10k_hw ath ath10k_common
depmod -a
modinfo ath | grep cn # should show a parameter
modprobe ath10k
```
