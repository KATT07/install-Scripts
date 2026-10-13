// **update system first after install use rpi imager to setup wireless and user accounts**
```bash
sudo apt update && sudo apt upgrade -y && sudo reboot now
```

// **setup static ip for pi**
```bash
sudo nmtui
sudo systemctl restart NetworkManager
```

// **install optional**
```bash
sudo apt install fastfetch -y
```

// **setup tailscale**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --advertise-exit-node
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```