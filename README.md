# Monitoring Stack with Prometheus, Node Exporter, cAdvisor, and Grafana

A comprehensive guide to setting up a monitoring stack on an Ubuntu machine using Docker Compose.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Install Docker & Docker Compose](#install-docker--docker-compose)
3. [Create the Docker Compose File](#create-the-docker-compose-file)
4. [Create Prometheus Configuration File](#create-prometheus-configuration-file)
5. [Directory Structure](#directory-structure)
6. [Running the Stack](#running-the-stack)
7. [Accessing the Services](#accessing-the-services)
8. [Importing Grafana Dashboards](#importing-grafana-dashboards)
9. [Final Notes](#final-notes)

---

## 1. Prerequisites

Ensure your system meets the following:

- Ubuntu 20.04 or later
- Docker installed (`docker --version`)
- Docker Compose installed (`docker-compose --version`)

## 2. Install Docker & Docker Compose

If Docker and Docker Compose are not installed, you can install them using the following commands:

### Install Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

### Install Docker Compose

```bash
sudo apt update
sudo apt install -y docker-compose
```

## 3. Create the Docker Compose File

Now, let's create a Docker Compose configuration file to set up Prometheus, Node Exporter, cAdvisor, and Grafana.

### Create a new directory for your project:

```bash
mkdir ~/monitoring-stack
cd ~/monitoring-stack
```
### Create the docker-compose.yml file:

```bash
nano docker-compose.yml
```
### Add the following content to your docker-compose.yml file:

```bash
version: '3.8'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    ports:
      - "9090:9090"
    networks:
      - monitoring

  node_exporter:
    image: prom/node-exporter:latest
    container_name: node_exporter
    restart: unless-stopped
    ports:
      - "9100:9100"
    networks:
      - monitoring

  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    networks:
      - monitoring

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=your_secure_password
      - GF_USERS_ALLOW_SIGN_UP=false
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning/datasources:/etc/grafana/provisioning/datasources
      - ./grafana/provisioning/dashboards:/etc/grafana/provisioning/dashboards
      - ./grafana/dashboards:/var/lib/grafana/dashboards
    networks:
      - monitoring

networks:
  monitoring:
    driver: bridge

volumes:
  prometheus_data:
  grafana_data:
```


