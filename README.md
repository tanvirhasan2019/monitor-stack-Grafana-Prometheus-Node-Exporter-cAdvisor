# Monitoring Stack with Prometheus, Node Exporter, cAdvisor, and Grafana

A guide to setting up a monitoring stack on an Ubuntu machine using Docker Compose.

![Grafana Dashboard](https://github.com/tanvirhasan2019/monitor-stack-Grafana-Prometheus-Node-Exporter-cAdvisor-/blob/main/grafana%20dashboard.png)

## Table of Contents

1. [Prerequisites](#1-prerequisites)
2. [Install Docker & Docker Compose](#2-install-docker--docker-compose)
3. [Create the Docker Compose File](#3-create-the-docker-compose-file)
4. [Create Prometheus Configuration File](#4-create-prometheus-configuration-file)
5. [Directory Structure](#5-directory-structure)
6. [Running the Stack](#6-running-the-stack)
7. [Accessing the Services](#7-accessing-the-services)
8. [Importing Grafana Dashboards](#8-importing-grafana-dashboards)
9. [Final Notes](#9-final-notes)

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

## 4. Create Prometheus Configuration File

### Create a prometheus.yml configuration file for Prometheus:

```bash
mkdir -p prometheus
nano prometheus/prometheus.yml
```

### Add the following content to the prometheus.yml file:

```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['node_exporter:9100']

  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
```

## 5. Directory Structure
After creating the docker-compose.yml and Prometheus configuration, your directory structure should look like this:

### Description of Directories and Files:
- **`docker-compose.yml`**: Defines the services for Prometheus, Node Exporter, cAdvisor, and Grafana.
- **`prometheus/`**: Contains the configuration for Prometheus.
  - **`prometheus.yml`**: Configuration file for scraping metrics from the services.
- **`grafana/`**: Contains Grafana related files.
  - **`dashboards/`**: Stores the Grafana dashboard JSON files.
  - **`provisioning/`**: Configuration files for provisioning.
    - **`datasources/`**: Configurations for connecting Grafana to Prometheus.
    - **`dashboards/`**: Dashboard provisioning configurations for Grafana.


## 6. Running the Stack

Now that the configuration files are ready, you can start the monitoring stack.

### In the ~/monitoring-stack directory, run:

```bash
docker-compose up -d
```
This command will download the necessary Docker images and start the containers in detached mode.

### To check if the containers are running:

```bash
docker-compose ps
```
You should see all four services (prometheus, node_exporter, cadvisor, grafana) running.


## 7. Accessing the Services

You can now access the services using the following URLs in your web browser:

- **Prometheus**: [http://your-server-ip:9090](http://your-server-ip:9090)
- **Node Exporter**: [http://your-server-ip:9100/metrics](http://your-server-ip:9100/metrics)
- **cAdvisor**: [http://your-server-ip:8080](http://your-server-ip:8080)
- **Grafana**: [http://your-server-ip:3000](http://your-server-ip:3000)

### Grafana Default Credentials:
- **Username**: `admin`
- **Password**: Use the value you set in the `GF_SECURITY_ADMIN_PASSWORD` environment variable.


## 8. Importing Grafana Dashboards
Once Grafana is up and running, you can import ready-made dashboards for Prometheus, Node Exporter, and cAdvisor:

### Log into Grafana:
- Go to [http://your-server-ip:3000]().
- Log in with your credentials.

### Import Dashboards:
1. Go to the Dashboard menu (left sidebar) and click **Import**.
2. You can search for the following dashboards by their IDs:
   - **Node Exporter Full Dashboard (ID: 1860)**: For system metrics.

### Configure Datasource:
- Ensure that **Prometheus** is set as the default datasource for the imported dashboards.

## 9. Final Notes

### Security Considerations:
- Consider securing your Grafana instance with a more robust authentication system and using **HTTPS** with a reverse proxy.
- Restrict access to **Prometheus**, **cAdvisor**, and **Node Exporter** endpoints for security reasons.

### Backup & Persistence:
- Ensure the volumes in the Docker Compose file are set up for data persistence across container restarts.

### Scaling:
- This setup can be scaled by adding more Prometheus targets or adding alerting rules to Prometheus as your infrastructure grows.

