# 🚀 Secure Observability Stack

**Prometheus • Grafana • Loki • Promtail • Node Exporter • Host Security Hardening**

A fully integrated, security-focused observability platform for Linux servers, homelab environments, and cloud instances.
This repository provides a production-grade setup combining **metrics**, **logs**, and **system hardening** in a unified, maintainable and extensible solution.

Designed for platform engineers, SREs, and cybersecurity-oriented infrastructures.


## ✨ Features

### 📊 **Metrics & Telemetry**

* **Prometheus** for metrics collection
* **Node Exporter (bare-metal)** for system-level telemetry
* Optional hardware exporters (CPU, disks, sensors, network)
* Dashboards for:

  * Server performance
  * Docker containers
  * Storage & IO
  * System health and uptime

### 📝 **Centralized Logging**

* **Loki** for high-performance log aggregation
* **Promtail** to ingest host and application logs
* **Docker → Loki log driver** for container logs
* Preconfigured log retention & anti-spam rate limits

### 📈 **Visual Dashboards**

* **Grafana** with curated dashboards:

  * Host overview
  * Logs explorer
  * Alerts panel
  * Docker monitoring

### 🛡️ **Security & Hardening**

* Docker security best practices:

  * rootless containers
  * `cap_drop: all`
  * `no-new-privileges: true`
  * isolated networks
  * read-only file systems
* OS hardening:

  * UFW rules
  * fail2ban profiles
  * sysctl hardening
  * service isolation
* Minimal exposed surface area
* Principle of least privilege everywhere


## 📁 Repository Structure

```
secure-observability-stack/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
├── loki/
│   └── config.yml
├── promtail/
│   └── config.yml
├── grafana/
│   ├── dashboards/
│   └── provisioning/
├── exporters/
│   ├── node_exporter.service
│   └── hardware_exporter/
├── security/
│   ├── ufw-rules.txt
│   ├── sysctl.conf
│   └── fail2ban/
│       └── jail.local
└── README.md
```


## ⚙️ Installation

### 1️⃣ Clone the repository

```bash
git clone https://github.com/abonnivard/secure-observability-stack.git && cd secure-observability-stack
```


## 2️⃣ Install Node Exporter (bare-metal)

This ensures robust host-level metrics without container limitations.

```bash
sudo useradd --no-create-home --shell /usr/sbin/nologin node_exporter

wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-1.8.1.linux-arm64.tar.gz
tar xvf node_exporter-*.tar.gz

sudo cp node_exporter-*/node_exporter /usr/local/bin/
sudo cp exporters/node_exporter.service /etc/systemd/system/

sudo systemctl daemon-reload
sudo systemctl enable --now node_exporter
```


## 3️⃣ Configure Docker Logging (Loki driver)

Edit `/etc/docker/daemon.json`:

```json
{
  "log-driver": "loki",
  "log-opts": {
    "loki-url": "http://127.0.0.1:3100/loki/api/v1/push"
  }
}
```
If the file doesn't exist, create it.


Restart Docker:

```bash
sudo systemctl restart docker
```


## 4️⃣ Deploy the stack

```bash
docker compose up -d
```


## 🌐 Access the UI

### Grafana

```
http://<server-ip>:3000  
user: admin  
pass: admin (change immediately)
```

### Prometheus

```
http://<server-ip>:9090
```

### Loki API

```
http://127.0.0.1:3100/loki/api/v1/query
```


## 🔐 Security Guidelines

This project ships with a set of hardened defaults, but additional recommendations include:

### System

* Disable SSH password login
* Enable UFW with default deny
* Keep system packages up to date
* Disable unused kernel modules

### Docker

* Run all services as non-root
* Restrict filesystem mounts
* Enforce read-only root FS
* Use private networks per service
* Limit container CPU/RAM if necessary

### Logs & Metrics

* Limit Loki ingestion rate
* Rotate logs via Promtail rules
* Restrict access to Prometheus and exporters


## 📦 Roadmap

* [ ] Add a Tailscale configuration to securely create a VPN
* [ ] Provide Ansible playbooks for full automation
* [ ] Include Slack/Discord alerting rules


## 👤 Maintainer

**Adrien Bonnivard**
*Site Reliability Engineer – Security & Identity*
Focus on DevOps, Platform Engineering, Infrastructure Security & Observability.
