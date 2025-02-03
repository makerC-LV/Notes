## Typical Linux command line stuff

#### Bind mount
Kernel feature, mounts a directory somewhere else in the directory tree.
```
mount --bind /some/where /else/where
mount -o bind /some/where /else/where
```

Bind mounts in `/etc/fstab`: `/media/d1-1tb /exports/d1-1tb none bind,rw`


#### Assign static IP (Debian/Ubuntu)
Use the Network Manager app. (Search "network" in startmenu)

---
#### Find IP address
`ip addr`

---
#### See disks with id `ls /dev/by-id/`

#### NFS file sharing
[A simple guide](https://bluexp.netapp.com/blog/azure-anf-blg-linux-nfs-server-how-to-set-up-server-and-client) - open access.<br>
**Server side**<br>
```
sudo apt install nfs-kernel-server
sudo mkdir /mnt/myshareddir
sudo chown nobody:nogroup /mnt/myshareddir #no-one is owner
sudo chmod 777 /mnt/myshareddir #everyone can modify files
```

Add line to `/etc/exports`
```
/mnt/mysharedir  *.local(rw,sync,no_root_squash,no_subtree_check) 192.168.68.1/24(rw,sync,no_root_squash,no_subtree_check)
```

```
sudo exportfs -a #making the file share available
sudo systemctl restart nfs-kernel-server #restarting the NFS kernel
```

**Client side**
```
sudo apt install nfs-common
```
Check if mount is available: `sudo showmount -e <server_hostname>`<br>
```
sudo mkdir /mnt/locally-mounted
#Mount syntax below
#sudo mount -t nfs {IP of NFS server}:{folder path on server} /mnt/locally-mounted
sudo mount -t nfs backup-server.local:/mnt/myshareddir /mnt/locally-mounted
```
Temporary mount:
```
sudo mount -t nfs4 backup-server.local:/exports/test /mnt/nfs/ -o defaults,user,exec,_netdev
```

Mount at boot: `/etc/fstab` entry:
```
{IP of NFS server}:{folder path on server} /var/locally-mounted nfs defaults 0 0
```


---
#### Samba notes
Unable to set up smb so that (a) the share is not public and (b) the written files have the same user permissions, regardless of which machine is writing them. 

Follow these [simple installation instructions](https://ubuntu.com/tutorials/install-and-configure-samba#3-setting-up-samba) and configure `smb.conf` following [these worked examples](https://www.samba.org/~ab/output/htmldocs/Samba3-HOWTO/FastStart.html#id2580815) (CAUTION, they don't quite work as advertised)


---
#### Mounting disk
Follow: https://www.wikihow.com/Linux-How-to-Mount-Drive
- edit /etc/fstab, add lines like `UUID=<...> /mount/point ext4 defaults 0 0`
- find UUID of each mounted partition via `ls -l /dev/disk/by-uuid/`
- For temporary mount `mount /dev/sdb /mount/point` suffices
- Unmounting: `umount /mount/point`
- Current `/etc/fstab` entries for all 4 disks:
    ```
    UUID=db23fd57-5a7b-421e-b96e-0d235eb5b354  /media/disk4tb  ext4  defaults 0 0
    UUID=d8984d8f-0128-4791-b299-69f26b5f6eaa /media/disk1tb ext4 defaults 0 0
    UUID=bf75d5b6-6531-48b3-8b52-a155c96e013e /media/backupdisk4tb ext4 defaults 0 0
    UUID=82108674-0fa7-4d58-85b3-0c9371a99b84 /media/backupdisk1tb ext4 defaults 0 0
    ```


---
#### Mounting SMB disk
Follow: https://www.linode.com/docs/guides/linux-mount-smb-share/
- Install cifs: `apt install cifs-utils psmisc`
- Check with `mount -t cifs`

Add the following lines to `/etc/fstab`
```
//deb-fs-vm/smb-disk4tb /mnt/smbdisk4tb cifs x-systemd.automount,rw,relatime,vers=3.1.1,cache=strict,credentials=/home/shiva/.credentials,uid=1000,gid=1000,file_mode=0777,dir_mode=0777 0 0
//deb-fs-vm/smb-disk1tb /mnt/smbdisk1tb cifs x-systemd.automount,rw,relatime,vers=3.1.1,cache=strict,credentials=/home/shiva/.credentials,uid=1000,gid=1000,file_mode=0777,dir_mode=0777 0 0
```
The `x-systemd.automount` mounts the disk at first access. (This may cause issues with programs that need the disks to be mounted at boot time)\
Create a `/home/shiva/.credentials` file that looks like:
```
username=shiva
password=<pw>
```


---
#### Disk performance with dd
throughput.sh
```
#!/bin/sh
dd if=/dev/zero of=./throughput.img bs=1G count=1 oflag=dsync
```
latency.sh
```
#!/bin/sh
dd if=/dev/zero of=./latency.img  bs=512 count=1000 oflag=dsync
```

---
#### Network performance with iperf [from here](https://askubuntu.com/questions/7976/how-do-you-test-the-network-speed-between-two-boxes)
`sudo apt install iperf`
on one machine:  `iperf -s  # start iperf server`<br>
on the other machine: `iperf -c <address of other computer>`

---
#### Set up mDNS  (Get xxx.local DNS values)
```
apt-get install avahi-daemon
#### Firewall (here uncomplicated firewall on ubuntu)
ufw allow mdns
```
Fix /etc/nsswitch.conf
```
CHANGE  hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4
TO      hosts:          files mdns4_minimal dns mdns4 [NOTFOUND=return]
```
/etc/avahi/avahi-daemon.conf
[publish]
publish-workstation=yes
```
#### If you don't like applications to publish their services
disable-user-service-publishing=yes
```
/etc/avahi/services/
```
Remove services (files) that you don't want to publish
```

Restart daemon
`service avahi-daemon restart`

---
#### View all mDns services on the network
```
avahi-browse --all       - or - 
mdns-scan
```

---
#### Add sudo to Debian (not installed by default)
Change to root using `su -`
```
apt install sudo
usermod -aG sudo [username] 
```
reboot.

---
#### Allow root login with password on Debian
Follow: https://linuxconfig.org/enable-ssh-root-login-on-debian-linux-server
Edit `/etc/ssh/sshd_config`, change the line for `PermitRootLogin` to say `yes`. Reboot.

---
#### Disk formatting and partitioning (dd, wipefs, fdisk)
- list disks: `lsblk`
- Delete all partitions: 
  - Option 1: `wipefs -a /dev/sdx`
  - Option 2: `dd if=/dev/zero of=/dev/[disk device] bs=512 count=1`  (only remove MBR, fast)
  - Option 3: `dd if=/dev/zero of=/dev/[disk device] bs=100M oflag=direct status=progress`  (writes zeros to the whole disk, oflag and bs speed things up considerably)
- Create partitions with `fdisk` Follow [this guide](https://docs.otc.t-systems.com/elastic-volume-service/umn/getting_started/initialize_an_evs_data_disk/initializing_a_linux_data_disk_fdisk.html). `fdisk` is an interactive program and quite intuitive.

---
#### See NFS shares from an IP or hostname
`showmount -e <IP or hostname>`



