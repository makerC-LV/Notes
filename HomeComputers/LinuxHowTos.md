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

---
#### Mounting disk

---
#### Mounting SMB disk

---
#### Disk performance with dd


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


