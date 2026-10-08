# install tools

// **Ghidra,Seclists Optional large download size**
```bash
sudo apt update && sudo apt install fastfetch tealdeer feh gedit ghex stegseek obsidian flatpak gnome-software-plugin-flatpak ghidra seclists -y && tldr -u
```

```bash
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

// **to make flatpak use system themes**
```bash
mkdir -p ~/.themes && cp -a /usr/share/themes/* ~/.themes/ && sudo flatpak override --filesystem=~/.themes/
```

```bash
sudo flatpak install bitwarden
```

# Disable sleep mode

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
sudo nano /etc/systemd/logind.conf
```

# install docker if needed

```bash
sudo apt update && sudo apt install -y docker.io && sudo systemctl enable docker --now 
```

# install nvidia drivers and cuda toolkit 
// **cuda toolkit removed from kali repo !!!!!!!** 
// https://pkg.kali.org/pkg/nvidia-cuda-toolkit
```bash
grep "contrib non-free" /etc/apt/sources.list.d/kali.sources
sudo apt update && sudo apt -y full-upgrade -y 
sudo apt install -y linux-headers-amd64 nvidia-driver nvidia-cuda-toolkit
```

// **since nvidia-cuda-toolkit removal use this**
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/debian13/x86_64/cuda-keyring_1.1-1_all.deb
sudo apt install ./cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt install -y linux-headers-$(uname -r) build-essential dkms
sudo apt -y install cuda-toolkit cuda-drivers nvidia-kernel-dkms
```

// **john by default is not compiled with opencl to use gpu cores run following**
```bash
sudo apt update
sudo apt install git build-essential libssl-dev zlib1g-dev libgmp-dev libpcap-dev libnss3-dev libkrb5-dev opencl-c-headers ocl-icd-opencl-dev clinfo -y
cd && git clone https://github.com/openwall/john.git
cd ./john/src && ./configure --enable-opencl
make -s clean && make -sj$(nproc)
```

# installing tailscale (optional)

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

# setup battery limit (asus laptops)

```bash
cd ~/Documents && git clone https://github.com/sreejithag/battery-charging-limiter-linux && cd ./battery-charging-limiter-linux
chmod +x ./limitd.sh && sudo ./limitd.sh 60
```

# additional optional timesavers

// **for unzip rockyou:**
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

// **updating metasploit cache:**
```bash
msfconsole
```

# get ovpn file from thm and htb

```bash
mkdir ~/Documents/ovpn
```

# common bugs:
// **if kali fails to boot with blank screen no output**
// **shift + f2**
// **login and type following**

```bash
sudo systemctl restart lightdm
```