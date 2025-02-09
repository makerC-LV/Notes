## Proxmox main server setup (pve.local) 


#### General config
- 2TB WD nvme for OS and VMs, Proxmox manages it.
- 4TB Klevv Cras nvme for nfs share via file-server VM
- mDns configured on server, name pve.local
- run [post pve install script](https://community-scripts.github.io/ProxmoxVE/scripts?id=post-pve-install) Disables enterprise repo, enables no-subscription repo, disables high-availability, etc.
- d2-4tb configured as backup storage for VZDump
- backups - not scheduled yet
- backups for VMs
- file server backs up 4TB disk to backup-server
- Download containers [as described here](https://pve.proxmox.com/wiki/Linux_Container#pct_container_images)
  ```
  pveam update
  pveam available
  pveam download local <filename>
  ```
- After downloading the containers are available in the GUI: `pve -> local -> CT templates`

---
### Templates

#### deb12Template 
- Debian configured with mDns, sudoer and root login.
- NFS client libs installed (nfs-common).
- scripts directory under `/home/shiva/` with some utils.
- To create a VM from this: `GUI->right-click->Clone`. 
- If not a temporary VM, select `Full clone` in the next dialog.
- After startup, run `sudo ~/scripts/change_hostname.sh`. After entering the new hostname, at the prompt, reboot. The VM will be accessible at `<hostname>.local`
- Optionally, to mount the 4tb disk, run `~/scripts/mount_shared_drive.sh`. This will mount the drive at `/mnt/nfs/fs/disk4tb`.

#### win10ProTemplate

Based on Win 10 Pro, and has `/fs.local/exports/disk4tb` mounted as drive Z:. <br>
[Guide](https://pve.proxmox.com/wiki/Windows_10_guest_best_practices)
for Proxmox configuration steps. When going through the installer, at a stage, it insists that you create 
a Microsoft account. [See this video](https://www.youtube.com/watch?v=45n6_7DnrWc)
for a good explanation. Essentially:

  - when the installer asks for your microsoft account, in ProxMox, go to 
`VM->Hardware->Network` and doubple click, which brings up the edit dialog. Select the `Disconnected` checkbox. 
  - Then, in the installer, select `Create account`. At this point, the installer will just give you the option to create a username for the computer user. Once you get past this stage, reconnect the disconnected network interface.
  - After installation, [mount the nfs drive](https://www.dell.com/support/kbdoc/en-us/000019642/ecs-how-to-mount-nfs-share-on-windows-client)
and [make it persistent](https://www.opswat.com/docs/mdss/knowledge-base/windows-deployment--how-to-create-a-persistent-mount-for-smb-and)


---
### VMs

#### fs (file server)
Full clone of [deb12Template](#deb12template). Serves the 4tb nvme disk to the network via nfs and smb (for Windows machines). Using a VM instead of a container because nfs-kernel-server doesn't work well with containers.

- **PCIe passthrough** Has the disk passed using pcie passthrough.After creating the VM,
```root@pve:~# qm set 101 -scsi1 /dev/disk/by-id/nvme-KLEVV_CRAS_C910_M.2_NVMe_SSD_4TB_2024051002002470```

- Mounted in /etc/fstab with 
```
UUID=cbf3cf4a-38b6-4d91-8f90-e06d0202af3d  /media/disk4tb  ext4  defaults  0 1
```

- Shared via `/exports/disk4tb`. `/media/disk4tb` is [bind mounted](./LinuxHowTos.md#bind-mount) to `exports/disk4tb` in `/etc/fstab`: 
```
/media/disk4tb /exports/disk4tb none bind,rw
```

- **Important:** exclude this disk from VM backup. We backup this disk manually. In gui select `fs -> hardware -> Hard disk (scsi1)` then click `Edit` and then `Advanced`. Deselect the `Backup` checkbox. 

- A nightly cron job is scheduled that backs up disk4tb to [backup-server](./BackupServer.md).
```
0 2 * * * rsync -avz /media/disk4tb/data/ /mnt/nfs/backup-server/d1-4tb/backup-disk4tb/data/
```

- To nfs mount the 4TB disk on another machine
    - install nfs-common: `sudo apt install nfs-common`
    - add the following line to /etc/fstab:<br>
    ```
    fs.local:/exports/disk4tb /mnt/nfs/fs/disk4tb nfs defaults 0 0
    ``` 

- **Backup for the fs VM:**  Scheduled via GUI: `Datacenter->Backups`

- **SMB share** `/exports/disk4tb` is also shared via SMB, only for Windows and only to user `shiva`. 
    - Follows [this guide](https://ubuntu.com/tutorials/install-and-configure-samba#2-installing-samba) for installation, and 
[this guide](https://www.virtono.com/community/tutorial-how-to/samba-sharing-with-authentication/) on how to set up SMB shares *with authentication*.


    - Only one samba user, `shiva`, and all access must be made using this user: `sudo smbpasswd -a shiva`

    - **wsdd** - Allows the shared directories to be discoverable by Windows. Install with `apt install wsdd`, [Github page](https://github.com/christgau/wsdd/tree/master)


- A nightly cron job is scheduled that backs up disk4tb to backup-server.
```
0 2 * * * rsync -avz /media/disk4tb/data/ /mnt/nfs/backup-server/d1-4tb/backup-disk4tb/data/
```
---
#### ms (Media server)
Cloned from `fs` template. Has 4tb disk mounted at `/media/disk4tb`
- Jellyfin installed from website installation instr.   


picoreplayer + pirate audio: [this post](https://forums.lyrion.org/forum/user-forums/linux-unix/108174-jivelite-on-a-pirate-audio-240x240-screen/page24?111502-Jivelite-on-a-Pirate-Audio-240x240-screen=&viewfull=1#post1421255)




















