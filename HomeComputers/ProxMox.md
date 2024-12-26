## Proxmox setup etc  (proxve.local)


#### General info 
- Naming convention for VMs and CTs: <os>-<purpose>-<container_type>, e.g. deb-nas-ct, ub-media-vm
- set up mdns on proxve (See mDNS below)
- List of disks:
    ```
    /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D62K61TY -> ../../sda
    /dev/disk/by-id/ata-ST31000528AS_9VP9397Y -> ../../sdb
    /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D6232HXC -> ../../sdc
    /dev/disk/by-id/ata-ST31000528AS_9VP93P1W -> ../../sdd
    /dev/disk/by-id/ata-Samsung_SSD_840_Series_S14GNEBCC25241K -> ../../sde
    ```



#### LXC for NAS (deb-nas-ct)
- Create CT from the debian ct iso 2048/1096 mem/swap, 1 cpu, 8G disk, DHCP
- edit "Options" to set "Console mode" to "shell" (This ensures that the console has a prompt)
- open a console and edit `/etc/ssh/sshd_config`. Change
`PermitRootLogin without-password`
to
`PermitRootLogin yes`. Then `service sshd restart`. (This allows ssh from a remote machine.)

- Pass through physical disks:
    - On proxve
    ````{verbatim}
       root@proxve:~# lsblk |awk 'NR==1{print $0" DEVICE-ID(S)"}NR>1{dev=$1;printf $0" ";system("find /dev/disk/by-id -lname \"*"dev"\" -printf \" %p\"");print "";}'|grep -v -E 'part|lvm' 
        NAME                           MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS DEVICE-ID(S)
        sda                              8:0    0   3.6T  0 disk   /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D62K61TY /dev/disk/by-id/wwn-0x50014ee2155c5fae
        sdb                              8:16   0 931.5G  0 disk   /dev/disk/by-id/ata-ST31000528AS_9VP9397Y /dev/disk/by-id/wwn-0x5000c500275c40fc
        sdc                              8:32   0   3.6T  0 disk   /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D6232HXC /dev/disk/by-id/wwn-0x50014ee2155c0b9b
        sdd                              8:48   0 931.5G  0 disk   /dev/disk/by-id/ata-ST31000528AS_9VP93P1W /dev/disk/by-id/wwn-0x5000c5002760d8aa
        sde                              8:64   0 232.9G  0 disk   /dev/disk/by-id/wwn-0x50025385501600f3 /dev/disk/by-id/ata-Samsung_SSD_840_Series_S14GNEBCC25241K

    ````

#### Assign static IP to a VM/CT

---
#### mDNS (see LinuxHowTos)

---
#### Pass through physical disks to a VM/CT

To get disk info:
```
root@proxve:~# lsblk |awk 'NR==1{print $0" DEVICE-ID(S)"}NR>1{dev=$1;printf $0" ";system("find /dev/disk/by-id -lname \"*"dev"\" -printf \" %p\"");print "";}'|grep -v -E 'part|lvm' 
```

For a short list:
```
find /dev/disk/by-id/ -type l|xargs -I{} ls -l {}|grep -v -E '[0-9]$' |sort -k11|cut -d' ' -f9,10,11,12
```


