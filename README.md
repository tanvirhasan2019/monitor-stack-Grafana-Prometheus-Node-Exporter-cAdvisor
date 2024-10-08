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


