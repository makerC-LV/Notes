## Typical Linux command line stuff


#### Assign static IP (Debian/Ubuntu)
Use the Network Manager app. (Search "network" in startmenu)

---
#### Find IP address
`ip addr`

---
#### See disks with id

---
#### Samba
Follow:  https://ubuntu.com/tutorials/install-and-configure-samba#3-setting-up-samba


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
#### Set up mDNS  (Get xxx.local DNS values)
```
apt-get install avahi-daemon
# Firewall (here uncomplicated firewall on ubuntu)
ufw allow mdns
```
Fix /etc/nsswitch.conf
```
CHANGE  hosts:          files mdns4_minimal [NOTFOUND=return] dns mdns4
TO      hosts:          files mdns4_minimal dns mdns4 [NOTFOUND=return]
```
/etc/avahi/avahi-daemon.conf
```
[publish]
publish-workstation=yes
# If you don't like applications to publish their services
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

