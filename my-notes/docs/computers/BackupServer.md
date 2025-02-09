## Backup server config

- Hostname: backup-server
- Debian 12 installed without GUI desktop
- allow root login with password via ssh: Edit `/etc/ssh/sshd_config`, change the line for `PermitRootLogin` to say `yes`. Reboot.
- Add sudo to debian (see linux howtos)
- Added user shiva to suoers list (see Linux howtos)   
- mdns set up
- Mounted 3 disks   under media as d1-1tb, d1-4ttb and d2-4tb
- setup nfs shares under /exports
