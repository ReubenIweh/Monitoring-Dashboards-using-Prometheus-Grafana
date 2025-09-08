# Monitoring-Dashboards-using-Prometheus-Grafana
Production-ready monitoring setup using Prometheus and Grafana for Linux infrastructure

📑 **Table of Contents**
- [Prequisites](#prequisites)
- [Install Prometheus](#install-prometheus)
- [Configuration Prometheus to Scrape metrics](#configuration-prometheus-to-scrape-metrics)
- [Create Prometheus Systemd Service](#create-prometheus-systemd-service)
- [Install Node Exporter on ALL Nodes](#disable-unnecessary-services)
- [Install Grafana and Connect Prometheus](#install-grafana-and-connect-prometheus)
- [Build Dashboards in Grafana](#build-dashboards-in-grafana)
- [Open Specific Ports on Firewall](#open-sepecific-ports-on-firewall)


  ## Prerequisites
  
Linux-based Monitoring Server (RHEL/CentOS/AlmaLinux)
Root access or sudo privileges
Network access to all nodes (port 9100 open)
Optional: DNS configured or IP addresses noted
