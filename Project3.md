# Project 3 – WireGuard VPN using Docker on DigitalOcean
**By: Jake Spencer**

## Overview
This project documents the setup and deployment of a WireGuard VPN server running inside Docker on a DigitalOcean Droplet. It includes step‑by‑step instructions, commands used, the docker‑compose.yml file, client configuration steps, and required proof of functionality.

---

# 1. Creating the DigitalOcean Droplet
1. Created a DigitalOcean account using the 60‑day student credit.
2. Added a credit card for verification (no charge during trial).
3. Clicked **Create → Droplet**.
4. Selected:
   - Ubuntu 22.04 LTS  
   - Basic $6/month droplet  
   - 1 CPU, 1GB RAM, 25GB SSD  
5. Selected **Password Authentication** and created a strong root password.
6. Launched the droplet and copied the **public IP address**.

---

# 2. Logging Into the Droplet
Connected from Windows PowerShell:

```
ssh root@143.198.142.215
```

Entered the root password.

---

# 3. Updating the Server
```
apt update && apt upgrade -y
```

---

# 4. Installing Docker & Docker Compose
```
apt install -y ca-certificates curl gnupg lsb-release

mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
> /etc/apt/sources.list.d/docker.list

apt update
apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verified:
```
docker --version
docker compose version
```

---

# 5. Creating WireGuard Directory
```
mkdir /root/wireguard
cd /root/wireguard
```

---

# 6. Creating docker-compose.yml
Created the file:
```
nano docker-compose.yml
```

Inserted:

```yaml
version: '3.8'

services:
  wireguard:
    image: linuxserver/wireguard
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=YOUR_DROPLET_IP
      - SERVERPORT=51820
      - PEERS=2
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.13.13.0
    volumes:
      - ./config:/config
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
    restart: unless-stopped
```

Saved with **Ctrl+O**, Enter, **Ctrl+X**.

---

# 7. Starting WireGuard
```
docker compose up -d
docker ps
```

---

# 8. Retrieving Client Config Files
WireGuard generated configs in:

```
/root/wireguard/config/
```

Downloaded to my computer using password login:

```
scp root@143.198.142.215:/root/wireguard/config/peer1/peer1.conf .
scp root@143.198.142.215:/root/wireguard/config/peer2/peer2.conf .
```

---

# 9. Connecting Phone to VPN
1. Installed the WireGuard app.
2. Sent `peer1.conf` to my phone.
3. Imported the file into the WireGuard app.
4. Activated the VPN.
5. Verified IP address changed to the Droplet's public IP.

---

# 10. Connecting PC to VPN
1. Installed WireGuard for Windows.
2. Imported `peer2.conf`.
3. Activated the tunnel.
4. Verified IP changed from my ISP to Droplet IP.


---

