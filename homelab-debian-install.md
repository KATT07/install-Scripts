// **choose xfce at startup defaults to x11**

// **only on baremetal**
```bash
sudo nano /etc/systemd/logind.conf
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

// **xrdp optional**
```bash
sudo apt install xrdp -y
sudo adduser xrdp ssl-cert
systemctl restart xrdp
```
// **not default by default no ufw installed**
```bash
sudo ufw allow from 192.168.1.0/24 to any port 3389
```

// **install gnome-disks app**
```bash
sudo apt update && sudo apt install gnome-disk-utility -y
```

// **assign new static ip after login with rdp/webui**
// **assign disks using rdp/webui use /mnt/bkp1 mount setup autmount enabled**
```bash
sudo reboot now
```

// **initial setup complete final step**
```bash
sudo apt update && sudo apt upgrade -y
```

// **!!! not latest ver use latest version when installing**
```bash
wget https://freefilesync.org/download/FreeFileSync_14.12_Linux_x86_64.tar.gz && gunzip ./FreeFileSync_14.12_Linux_x86_64.tar.gz && tar xvf ./FreeFileSync_14.12_Linux_x86_64.tar
./Freefilesync.run
```

// **installing docker**
```bash
sudo apt install curl -y
curl -fsSL https://get.docker.com | bash
mkdir ~/docker
```

// **installing samba via docker**
```bash
mkdir ~/docker/samba && cd ~/docker/samba
nano ./docker-compose.yml
```

```docker
services:
  samba:
    image: dockurr/samba
    container_name: samba
    environment:
      NAME: "Shared"
      USER: "***************"
      PASS: "***************"
    ports:
      - 445:445
    volumes:
      - /mnt/bkp1:/shared
    restart: unless-stopped

```

```bash
sudo docker compose up -d
```

// **installing plex via docker GOTO** https://www.plex.tv/claim
```bash
mkdir ~/docker/plexmediaserver && cd ~/docker/plexmediaserver
nano ./docker-compose.yml
```

```docker
services:
  plex:
    container_name: plex
    image: plexinc/pms-docker
    restart: unless-stopped
    environment:
      - TZ=UTC
      - PLEX_CLAIM=<claimToken>
    network_mode: host
    volumes:
      - ~/docker/plexmediaserver/config:/config
      - ~/docker/plexmediaserver/transcode:/transcode
      - /mnt/bkp1/Media:/data
```

```bash
sudo docker compose up -d
```

// installing qbittorrent-nox via docker
```bash
mkdir ~/docker/qbittorrent-nox && cd ~/docker/qbittorrent-nox
nano docker-compose.yml
```

```docker
services:
  qbittorrent-nox:
    container_name: qbittorrent-nox
    image: qbittorrentofficial/qbittorrent-nox:latest
    restart: unless-stopped
    environment:
      - QBT_LEGAL_NOTICE=CONFIRM
    ports:
      # for bittorrent traffic
      - 6881:6881/tcp
      - 6881:6881/udp
      # for WebUI
      - 8080:8080/tcp
    read_only: true
    stop_grace_period: 30m
    tmpfs:
      - /tmp
    tty: true
    volumes:
      - ~/docker/qbittorrent-nox/config:/config
      - /mnt/bkp1/Downloads:/downloads
```

// **setup password on gui with temporary password shown in cli**
// **press d to detach**
```bash
sudo docker compose up
```

// **additional cli tools (optional)**
```bash
sudo apt install tealdeer nala fastfetch nmap -y && tldr -u
```

// **installing tailscale**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --advertise-exit-node
```

webui-ports:
8080-qbit
32400-plex

TODO:
ADD INSTALL ARR SUITE
