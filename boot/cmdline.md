`/boot/cmdline.txt`
  
for `VideoCore` GPU

```
video=HDMI-A-1:1280x720@60D console=serial0,115200 console=tty1 root=PARTUUID=76b22e26-02 rootfstype=ext4 fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles radeon.uvd=0 pci=noaer,nomsi radeon.msi=0 radeon.pcie_gen2=0 pcie_aspm=off radeon.aspm=0 radeon.runpm=0 radeon.dpm=0
```

for `radeon` GPU
```
console=serial0,115200 console=tty1 root=PARTUUID=76b22e26-02 rootfstype=ext4 fsck.repair=yes rootwait quiet splash plymouth.ignore-serial-consoles radeon.uvd=0 pci=noaer,nomsi radeon.msi=0 radeon.pcie_gen2=0 pcie_aspm=off radeon.aspm=0 radeon.runpm=0 radeon.dpm=0
```
