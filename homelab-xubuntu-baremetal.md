// **only on baremetal**
```bash
sudo nano /etc/systemd/logind.conf
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

// **setup static ip using nmtui**
```bash
sudo nmtui
```

// **xrdp optional for gui**
```bash
sudo apt update
sudo apt install xrdp -y
sudo adduser xrdp ssl-cert
systemctl restart xrdp
sudo ufw allow from 192.168.1.0/24 to any port 3389
```

// **install gnome-disks app**
```bash
sudo apt update && sudo apt install gparted gnome-disk-utility -y
```

// **install flatpak for PeaZip or any other flatpaks**
```bash
sudo apt install flatpak gnome-software-plugin-flatpak -y && flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo

sudo flatpak install PeaZip
```

// **assign disks using rdp use /mnt/bkp1 mount setup automount enabled**
```bash
sudo reboot now
```

// **initial setup complete final step**
```bash
sudo apt update && sudo apt upgrade -y
```

// **Free File Sync** 
goto [https://freefilesync.org/download.php](https://freefilesync.org/download.php)

// **chrome (optional)**
goto https://www.google.com/intl/en_in/chrome/

// **installing docker**
```bash
sudo apt install curl -y
curl -fsSL https://get.docker.com | bash
mkdir ~/docker
```

// **installing samba via docker**
```bash
mkdir ~/docker/samba && cd ~/docker/samba
sudo nano ./docker-compose.yml
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
sudo nano ./docker-compose.yml
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
      - ./config:/config
      - ./transcode:/transcode
      - /mnt/bkp1/Media:/data
```

```bash
sudo docker compose up -d
```

// **installing qbittorrent-nox via docker (!!! IGNORE IF SETTING UP ARR STACK)**
```bash
mkdir ~/docker/qbittorrent-nox && cd ~/docker/qbittorrent-nox
sudo nano docker-compose.yml
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
      - ./config:/config
      - /mnt/bkp1/Downloads:/downloads
```

// **setup password on gui with temporary password shown in cli**
// **press d to detach**
```bash
sudo docker compose up
```

// **jellyfin docker (needed for remote connections tailscale etc plex needs plexpass)**
```bash
mkdir ~/docker/jellyfin && cd ~/docker/jellyfin
sudo nano ./docker-compose.yml
```

```docker
services:
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    ports:
      - 8096:8096/tcp
      - 7359:7359/udp
    volumes:
      - ./config:/config                                                  
      - ./cache:/cache                                                    
      - type: bind
        source: /mnt/bkp1/Media
        target: /media
    restart: 'unless-stopped'
```

```bash
sudo docker compose up -d
```

// **node-exporter + prometheus docker (also need to setup grafana see next)**
https://github.com/KATT07/Devops-projects/tree/main/node-exporter-localhost-main
```bash
mkdir ~/docker/node-exporter && cd ~/docker/node-exporter
sudo nano ./docker-compose.yml
```

```docker
networks:
  monitoring:
    driver: bridge

volumes:
  prometheus_data: {}

services:
  node-exporter:
    image: prom/node-exporter:latest
    container_name: node-exporter
    restart: unless-stopped
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    expose:
      - 9100
    networks:
      - monitoring

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/etc/prometheus/console_libraries'
      - '--web.console.templates=/etc/prometheus/consoles'
      - '--web.enable-lifecycle'
    expose:
      - 9090
    networks:
      - monitoring

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    ports:
      - '3000:3000'
    networks:
      - monitoring
```

```bash
sudo nano ./prometheus.yml
```

```yml
global:
  scrape_interval: 1m

scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['node-exporter:9100']
```

```bash
sudo docker compose up -d
```

// **setup additional in gui for panel in grafana default login admin:admin**
// **use grafana > connections > datasources > add prometheus at**
// http://prometheus:9090  also set scrape interval to same as config: 1m
// **use grafana > dashboards > new > import dashboard**  
// **use id: 1860**

https://github.com/KATT07/Devops-projects/blob/main/node-exporter-localhost-main/custom-grafana-dash.json


// **additional cli tools (optional)**
```bash
sudo apt install tealdeer fastfetch htop nvtop cowsay nmap -y && tldr -u
```

// **installing tailscale**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --advertise-exit-node
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```


webui-ports:
8080-qbit
32400-plex
8096-jellyfin
3000-grafana


// **updating and maintaining system**
```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
sudo flatpak update
sudo snap refresh --list #(if installed)
#in same directory as docker-compose.yml
sudo docker compose down && sudo docker compose pull && sudo docker compose up -d && sudo docker image prune -f
```
