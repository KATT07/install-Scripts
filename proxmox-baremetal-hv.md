INSTALL PROXMOX SETUP STATIC IP FOR PROXMOX
WEBUI UPLOAD .ISO file
INSTALL DISTRO and enable autostart etc
do not touch or install anything to proxmox 
disable enterprise and enable no subscription and update
also disable sleep

// **disable enterprise and enable no subscription and update**
```bash
apt update && apt upgrade -y
```

// **also disable sleep**
```bash
systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
nano /etc/systemd/logind.conf
```

// **passthrough hdds**
```bash
ls /dev/disk/by-id/ -l
```

```bash
qm set <VM-ID> -scsi<n> /dev/disk/by-id/<DISK-ID>,serial=DISK01
```

// **Example:**
```bash
qm set 100 -scsi1 /dev/disk/by-id/usb-disk-0:0,serial=DISK01
```

// **Note**
important set serial numbers for disks else they will get mixed up

use debian setup script from here do not mod proxmox itself