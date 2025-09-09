# Monitoring-Dashboards-using-Prometheus-Grafana
Production-ready monitoring setup using Prometheus and Grafana for Linux infrastructure

📑 **Table of Contents**
- [Prerequisites](#prerequisites)
- [Install Prometheus](#install-prometheus)
- [Configuration Prometheus to Scrape metrics](#configuration-prometheus-to-scrape-metrics)
- [Create Prometheus Systemd Service](#create-prometheus-systemd-service)
- [Install Node Exporter on ALL Nodes](#disable-unnecessary-services)
- [Install Grafana and Connect Prometheus](#install-grafana-and-connect-prometheus)
- [Build Dashboards in Grafana](#build-dashboards-in-grafana)
- [Open Specific Ports on Firewall](#open-sepecific-ports-on-firewall)


  ## Prerequisites
  
- Linux-based Monitoring Server (RHEL/CentOS/AlmaLinux)
- Root access or sudo privileges
- Network access to all nodes (port 9100 open)
- Optional: DNS configured or IP addresses noted

## Install Prometheus

<details> <summary>Click to expand</summary>
  
**1.1 Create Prometheus User**
  
sudo useradd --no-create-home --shell /bin/false prometheus

**1.2 Create Necessary Directories**

sudo mkdir /etc/prometheus /var/lib/prometheus

**1.3 Download Prometheus**

cd /tmp
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.52.0/prometheus-2.52.0.linux-amd64.tar.gz
tar -xvf prometheus-2.52.0.linux-amd64.tar.gz
cd prometheus-2.52.0.linux-amd64

**1.4 Move Binaries**

sudo cp prometheus promtool /usr/local/bin/
sudo chmod +x /usr/local/bin/prometheus /usr/local/bin/promtool

**1.5 Move Configuration Files**

sudo cp -r consoles/ console_libraries/ /etc/prometheus/
sudo cp prometheus.yml /etc/prometheus/

**1.6 Set Ownership**

sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus

</details>

## Configuration Prometheus to Scrape metrics

<details> <summary>Click to expand</summary>

Edit /etc/prometheus/prometheus.yml

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'nodes'
    static_configs:
      - targets:
          - node001.example.com:9100
          - node002.example.com:9100
          - node003.example.com:9100
          ** ... add all server nodes here**

Make sure Node Exporter is running on all nodes.
</details>


## Create Prometheus Systemd Service

<details> <summary>Click to expand</summary>

**Create systemd service file**

**Create /etc/systemd/system/prometheus.service**

[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus/ \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target

**Reload & Start Service**

sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable --now prometheus

**Verify**
- http://IP_address_of_server:9090
- http://FQDN_of_server:9090

</details>

## Install Node Exporter on ALL Nodes

<details> <summary>Click to expand</summary>

**Download Node Exporter**

cd /tmp
curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
tar -xvf node_exporter-1.8.1.linux-amd64.tar.gz
cd node_exporter-1.8.1.linux-amd64
sudo cp node_exporter /usr/local/bin/
sudo useradd --no-create-home --shell /bin/false node_exporter

**Create systemd service**

- Create /etc/systemd/system/node_exporter.service

[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=default.target

**Start Node Exporter**

sudo systemctl daemon-reload
sudo systemctl enable --now node_exporter

**Verify**

curl http://node001.example.com:9100/metrics

</details>

## Install Grafana and Connect Prometheus

<details> <summary>Click to expand</summary>

**Add Grafana Repo**

sudo tee /etc/yum.repos.d/grafana.repo <<EOF
[grafana]
name=Grafana OSS
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
EOF

**Install Grafana**

sudo dnf install grafana -y

**Start Grafana**

sudo systemctl enable --now grafana-server

**Access Grafana**

http://server_hostname:3000

Username: admin

Password: admin (change on first login)

</details>

## Build Dashboards in Grafana

<details> <summary>Click to expand</summary>

- Add Prometheus as a data source.

- Import dashboard templates (e.g., Node Exporter Full - ID 1860)

- Create custom dashboards for: CPU, Memory, Disk

- Alerts on thresholds

- Heatmaps or table views of all nodes

  </details>
