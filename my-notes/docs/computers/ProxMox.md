## Proxmox setup etc  (proxve.local)

#### General info
<details>
<summary> Naming convention, types of VMs, disks</summary>

- Naming convention for VMs and CTs: <os>-<purpose>-<container_type>, e.g. deb-nas-ct, ub-media-vm
- proxve has the static address 192.168.1.10. All the VMs use mDNS.
- proxve can be accessed at proxve.local (See mDNS below)
- List of disks:      
    ```
    /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D62K61TY -> ../../sda
    /dev/disk/by-id/ata-ST31000528AS_9VP9397Y -> ../../sdb
    /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D6232HXC -> ../../sdc
    /dev/disk/by-id/ata-ST31000528AS_9VP93P1W -> ../../sdd
    /dev/disk/by-id/ata-Samsung_SSD_840_Series_S14GNEBCC25241K -> ../../sde
    ```
- There are only two VMs that have the 4 physical disks passed through to them (see Pass through below). `deb-fs-vm` and `lmint-direct_disk-vm`. These two should not be started at the same time, for disk safety. 
- `deb-fs-vm` has two functions:
    - provide samba shares for other VMs identified by:
        ```
        //deb-fs-vm/smb-disk4tb
        //deb-fs-vm/smb-disk1tb
        ```  
    - backup the disks to their backup counterparts.
- `lmint-direct-disk-vm` mounts all 4 disks, but does not provide any samba shares, it is for disk maintenance and operations that may require a GUI program.

</details>

#### Templates
<!-- - debian-basic-template : only with user shiva configured -->
- deb-template: User shiva, sudo, mdns configured. Has a script: 
`/home/shiva/mount_smb.sh` for mounting the two samba disks.
- lmint-template: User shiva configured. Has a script:
`/home/shiva/mount_smb.sh` for mounting the two samba disks.


#### Create VM from template
- Clone the template and change the file `/etc/hostname`. Reboot. Optionally,  run `apt update` and `apt upgrade`.

---
#### VM for file server (deb-fs-vm, VM ID 107)
- Create VM from the debian template iso 2048/1096 mem/swap, 1 cpu, 8G disk, DHCP
- Set up mDNS (See LinuxHowTos)
- Pass through physical disks:
    - On proxve, run:\
     `find /dev/disk/by-id/ -type l|xargs -I{} ls -l {}|grep -v -E '[0-9]$' |sort -k11|cut -d' ' -f9,10,11,12 | grep -v wwn`\
    The output is:\
    ```
    /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D62K61TY -> ../../sda
    /dev/disk/by-id/ata-ST31000528AS_9VP9397Y -> ../../sdb
    /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D6232HXC -> ../../sdc
    /dev/disk/by-id/ata-ST31000528AS_9VP93P1W -> ../../sdd
    /dev/disk/by-id/ata-Samsung_SSD_840_Series_S14GNEBCC25241K -> ../../sde

    ```
    Execute command below using the scsi2 - scsi5, for the 4 disks:\
    `qm link 107 -scsi2 /dev/disk/by-id/ata-WDC_WD40EZAZ-00SF3B0_WD-WX62D62K61TY`
- Automount the disks: (See LinuxHowTos)
    - Mount by UUID. Find the UUID's by executing `ls -l /dev/disk/by-uuid/`. The line is `UUID=<...> /mount/point ext4 defaults 0 0`
    - The mount points are in /media/, disk4tb, backupdisk4tb, disk1tb, backupdisk1tb.
    - **Note:** The 1 TB disks have some residual partition on them. Clean them out once the data is organized.

- Samba: (see LinuxHowTos)

    - Install Samba: `apt install samba`
    - add user 'shiva' to the samba group: `smbpasswd -a shiva`
    - Add entries in `/etc/samba/smb.conf` for the two shared disks


---
#### Torrent downloader (lmint-torrent_vm)
- NordVPN, qTorrent, installed, smb disks mounted
- While connected to VPN, cannot access smb disks
- Torrent downloads stored in ~/TDownload/
- Occasionally move downloaded files to disk4tb


#### Mount SMB shares: (see LinuxHowTos)


---
#### mDNS (see LinuxHowTos)

---
#### Pass through physical disks to a VM/CT
Follow: https://pve.proxmox.com/wiki/Passthrough_Physical_Disk_to_Virtual_Machine_(VM)

To get disk info:
```
root@proxve:~# lsblk |awk 'NR==1{print $0" DEVICE-ID(S)"}NR>1{dev=$1;printf $0" ";system("find /dev/disk/by-id -lname \"*"dev"\" -printf \" %p\"");print "";}'|grep -v find /dev/disk/by-id/ -type l|xargs -I{} ls -l {}|grep -v -E '[0-9]$' |sort -k11|cut -d' ' -f9,10,11,12 | grep -v wwn-E 'part|lvm' 
```

For a short list:
```
find /dev/disk/by-id/ -type l|xargs -I{} ls -l {}|grep -v -E '[0-9]$' |sort -k11|cut -d' ' -f9,10,11,12
```

Hot plug hhysical device as a virtual scsi disk:\
`qm set <VM #> -scsi2 (or 3, 4, etc) /dev/disk/by-id/ata-ST3000DM001-1CH166_Z1F41BLC`

To remove:\
`qm unlink <VM #> --idlist scsi2`

## ToDo

- Create deb-fs-vm from deb-template
    - publish samba shares
    - LATER: change the file and dir permissions to 755 for all the disks
    - LATER: add backup cron job

- Create lmint-template 
    - Create a vm from from lmint iso
    - add mdns
    - add scripts for smb manual mounting, adding smb to /etc/fstab

- Create lmint-direct-disk-vm 
    - create vm from lmint-template
    - Pass through physical disks
    -

- Now I'll type a program   


