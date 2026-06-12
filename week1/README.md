# Week 1 Project: Environment Setup and Medical IoT Device Simulation

## Project Title

Medical IoT Device Honeypot Simulation using Cowrie and Docker

---

## Objective

The objective of this project is to create a deception-based cybersecurity environment that simulates a medical IoT device. The honeypot is deployed inside an isolated Docker environment using Cowrie and customized to imitate a medical patient monitoring device.

The environment captures unauthorized access attempts while presenting attackers with a realistic device interface.

---

# Architecture

```text
Attacker
    |
    v
+------------------+
| Docker Host      |
|                  |
| Cowrie Honeypot  |
| SSH : 2222       |
| Telnet : 2223    |
| Fake Filesystem  |
+------------------+
```

---

# System Requirements

* Ubuntu 22.04 LTS
* Docker
* Docker Compose
* 4 GB RAM Minimum
* Internet Connection

---

# Step 1: Prepare the Environment

## Update System

```bash
sudo apt update
sudo apt upgrade -y
```

## Install Docker and Docker Compose

```bash
sudo apt install docker.io docker-compose -y
```

## Start Docker Service

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

## Verify Installation

```bash
docker --version
docker-compose --version
```

Expected Output:

```text
Docker version xx.xx.x
docker-compose version xx.xx.x
```

---

# Step 2: Create Project Directory

Create project folder:

```bash
mkdir medical-honeypot
cd medical-honeypot
```

Create required directories:

```bash
mkdir cowrie-data
mkdir logs
```

Project structure:

```text
medical-honeypot/
├── docker-compose.yml
├── cowrie-data/
└── logs/
```

---

# Step 3: Deploy Cowrie Honeypot

Create Docker Compose configuration:

```bash
nano docker-compose.yml
```

Paste:

```yaml
version: '3'

services:
  cowrie:
    image: cowrie/cowrie:latest
    container_name: cowrie
    restart: unless-stopped

    ports:
      - "2222:2222"
      - "2223:2223"

    volumes:
      - ./cowrie-data:/cowrie/cowrie-git/var
      - ./logs:/cowrie/cowrie-git/log
```

Start the container:

```bash
docker-compose up -d
```

Verify:

```bash
docker ps
```

Expected:

```text
cowrie running
```

---

# Step 4: Test Honeypot

View logs:

```bash
docker logs cowrie
```

Test SSH access:

```bash
ssh root@localhost -p 2222
```

Expected:

```text
login:
```

Cowrie should display a fake login prompt.

---

# Step 5: Simulate a Medical IoT Device

Device Name:

```text
MedCare Patient Monitor X200
```

Access container:

```bash
docker exec -it cowrie bash
```

Navigate to Cowrie directory:

```bash
cd /cowrie/cowrie-git
```

Create configuration:

```bash
cp etc/cowrie.cfg.dist etc/cowrie.cfg
```

Edit:

```bash
nano etc/cowrie.cfg
```

Add:

```ini
[honeypot]
hostname = medcare-x200
```

Save file.

---

# Step 6: Customize SSH Banner

Edit configuration:

```bash
nano etc/cowrie.cfg
```

Add:

```ini
[ssh]
version = SSH-2.0-MedCare_X200_v3.4
```

Expected banner:

```text
SSH-2.0-MedCare_X200_v3.4
```

This helps the honeypot appear as a medical monitoring device.

---

# Step 7: Create Medical Device Filesystem

Create directories:

```bash
mkdir -p honeyfs/opt/medcare
mkdir -p honeyfs/etc
mkdir -p honeyfs/var/log
```

Create device configuration:

```bash
nano honeyfs/opt/medcare/device.conf
```

Contents:

```text
DEVICE_NAME=MedCare_X200
DEVICE_ID=MCX200-001
FIRMWARE=3.4.1
LOCATION=ICU
```

Create patient monitoring log:

```bash
nano honeyfs/opt/medcare/patient_monitor.log
```

Contents:

```text
Heart Rate: 72 bpm
SpO2: 98%
BP: 120/80
```

Create version file:

```bash
nano honeyfs/etc/version
```

Contents:

```text
MedCare Monitor OS 3.4.1
```

---

# Step 8: Simulate Additional Services

Medical devices commonly expose:

| Service | Port |
| ------- | ---- |
| SSH     | 22   |
| Telnet  | 23   |
| HTTP    | 80   |
| HTTPS   | 443  |

Deploy Nginx:

```bash
docker run -d \
--name medweb \
-p 8080:80 \
nginx
```

Enter container:

```bash
docker exec -it medweb bash
```

Create landing page:

```bash
echo "<h1>MedCare X200 Monitoring System</h1>" > /usr/share/nginx/html/index.html
```

Verify:

```bash
curl http://localhost:8080
```

Expected:

```html
<h1>MedCare X200 Monitoring System</h1>
```

---

# Step 9: Configure Telnet Emulation

Edit Cowrie configuration:

```ini
[telnet]
enabled = true
listen_endpoints = tcp:2223:interface=0.0.0.0
```

Restart container:

```bash
docker restart cowrie
```

Verify:

```bash
telnet localhost 2223
```

Expected:

```text
MedCare X200 Login
```

---

# Step 10: Verify Device Fingerprint

Install Nmap if necessary:

```bash
sudo apt install nmap -y
```

Check SSH banner:

```bash
nmap -sV localhost -p 2222
```

Expected:

```text
SSH-2.0-MedCare_X200_v3.4
```

Check Web Interface:

```bash
curl http://localhost:8080
```

Expected:

```text
MedCare X200 Monitoring System
```

---

# Step 11: Verify Logging

Generate login attempts:

```bash
ssh admin@localhost -p 2222
```

Try several usernames and passwords.

Monitor logs:

```bash
docker exec -it cowrie bash
```

```bash
tail -f var/log/cowrie/cowrie.log
```

Expected entries:

```text
Login Attempt
Username
Password
Source IP
Timestamp
```

---

# Project Validation Checklist

| Task                        | Status |
| --------------------------- | ------ |
| Docker Installed            | ✓      |
| Docker Compose Installed    | ✓      |
| Cowrie Running              | ✓      |
| SSH Honeypot Working        | ✓      |
| Telnet Honeypot Working     | ✓      |
| Medical Hostname Configured | ✓      |
| SSH Banner Modified         | ✓      |
| Medical Filesystem Created  | ✓      |
| Web Interface Available     | ✓      |
| Logging Verified            | ✓      |

---

# Screenshots Required

1. Docker Installation
2. Running Containers (`docker ps`)
3. SSH Login Screen
4. Telnet Login Screen
5. Nmap Scan Output
6. Medical Web Interface
7. Cowrie Log File
8. Project Folder Structure

---

# Learning Outcomes

After completing this project, the following concepts will be understood:

* Docker Containerization
* Honeypot Deployment
* Deception Technology
* SSH Service Emulation
* Telnet Emulation
* Network Fingerprinting
* Logging and Monitoring
* Medical IoT Device Simulation
* Cyber Threat Intelligence Collection

---

# Conclusion

A fully functional medical IoT honeypot environment was successfully deployed using Docker and Cowrie. The environment simulates a medical patient monitoring device by customizing SSH banners, filesystem structures, and exposed services while collecting attacker interaction logs for security analysis.
