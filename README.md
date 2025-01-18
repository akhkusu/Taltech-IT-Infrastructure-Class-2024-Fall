# Taltech IT Infrastructure Class 2024 Fall

---

## Key Components

- **`infra.yaml`**: Main playbook to set up all services.
- **`hosts`**: Inventory file listing servers.
- **`group_vars/all.yaml`**: Configuration file for global variables.

### Services Managed:
- **Agama**: Application setup.
- **DNS**: Configure BIND9 and manage DNS records.
- **Docker**: Install Docker.
- **Grafana**: Monitoring dashboards.
- **HAProxy**: Load balancer.
- **InfluxDB**: Database for time-series data.
- **MySQL**: Database setup and replication.
- **Prometheus**: Monitoring system.
- **Nginx**: Reverse proxy setup.
- **Keepalived**: High-availability configuration.

---