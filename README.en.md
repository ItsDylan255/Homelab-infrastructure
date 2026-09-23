# Homelab Infrastructure

[🇩🇪 Deutsche Version](README.md)

Self-hosted, multi-tier server infrastructure for networking services, monitoring,
automation, and virtualization. Built and maintained as a personal project alongside
a Computer Science degree (focus: technical systems).

![Homarr Dashboard](docs/images/homarr-overview.png)
*Central dashboard providing quick access to all running services*

## Skills demonstrated

Through operating this infrastructure, I gained practical experience with:

- Linux system administration
- Docker and container lifecycle management
- Virtual machines and virtual networking
- DNS, DHCP and reverse proxies
- VPN-based remote access
- Monitoring and observability
- Infrastructure automation with Ansible
- Troubleshooting using logs and system-level diagnostics
- Backup strategy and recovery planning
- Technical documentation

## Why this project?

I built this homelab as a hands-on environment to apply concepts from my Computer Science studies and gain practical experience with Linux, networking, virtualization, automation, monitoring, and self-hosted services.

The infrastructure serves as both a learning environment and a practical IT platform for running services such as Home Assistant, game servers, monitoring, and internal tools. Operating the infrastructure also provides opportunities to troubleshoot real-world issues, evaluate technical solutions, and document the results.

## Overview

The infrastructure consists of two physical hosts currently in operation, with a third host planned to expand the environment with dedicated storage and backup capabilities:

| Host      | Status      | Role                             | Platform      | Key Technologies                         |
| --------- | ----------- | -------------------------------- | ------------- | ---------------------------------------- |
| **Zeta**  | Running     | Network & service infrastructure | Debian 13     | Docker, Prometheus, Grafana, NPM         |
| **Alpha** | Running     | Virtualization & compute         | Proxmox VE    | VMs, Docker, Home Assistant, Pterodactyl |
| **Gamma** | **Planned** | Storage & backup                 | TrueNAS SCALE | ZFS, SMB/NFS                             |



![Rack Layout](docs/images/rack-layout.png)
*Physical rack layout, managed with [Rackula](https://github.com/RackulaLives/Rackula)*

## Architecture

The infrastructure architecture is documented and visualized using RackPeek,
covering the physical hardware, operating systems, virtualization layer,
containers, services, and their relationships.


![RackPeek Logical Architecture](docs/images/rackpeek-logical.png)
*Logical architecture showing the relationship between hardware, systems, containers, and services.*


![RackPeek Network Topology](docs/images/rackpeek-topology.png)
*Network topology showing the connections between infrastructure components and network services.*


![Proxmox VM Overview](docs/images/proxmox-vm-overview.png)
*Proxmox overview: VMs for Home Assistant, Pterodactyl, and an isolated Arch Linux lab*

## Technologies & Skills

| Area | Technologies |
|---|---|
| Operating Systems | Debian, Linux |
| Virtualization | Proxmox VE |
| Containers | Docker, Docker Compose |
| Networking | TCP/IP, DNS, DHCP, Tailscale, Nginx Proxy Manager |
| Monitoring | Prometheus, Grafana, Node Exporter, cAdvisor, Uptime Kuma |
| Automation | Ansible, n8n, Watchtower |
| Security | Tailscale, TLS, mkcert, network segmentation |
| Documentation | Wiki.js, RackPeek, Rackula |

## Core Components

### Infrastructure Management

Termix provides a centralized interface for managing and accessing the
Linux systems in the infrastructure via SSH. It provides a single overview
of host availability and resource usage across multiple systems.

![Termix Infrastructure Dashboard](docs/images/termix-dashboard.png)
*Centralized management and monitoring of the infrastructure hosts.*

### Monitoring Stack
Prometheus collects metrics from all hosts (Node Exporter, cAdvisor, plus a
specialized Proxmox exporter for VM/cluster metrics). Grafana visualizes the data
across multiple dashboards; Uptime Kuma additionally monitors the availability of
all web services and sends alerts via ntfy.

<img src="docs/images/grafana-dashboard.png" alt="Grafana Dashboard" width="49%"> <img src="docs/images/uptime-kuma-status.png" alt="Uptime Kuma Status" width="49%">

*Left: Grafana Node Exporter dashboard (CPU/RAM/network per host) · Right: Uptime Kuma status overview of all monitored services*

### Networking & Access
All services are reachable via readable local domains (`*.homelab.com`, secured with
a self-signed mkcert wildcard certificate) through Nginx Proxy Manager, instead of
raw IP:port combinations. Remote access relies exclusively on Tailscale (mesh VPN) —
deliberately avoiding router port-forwarding to keep the attack surface minimal.

### Automation
Ansible manages repeatable setup steps (e.g. installing Docker on new hosts) over
SSH, with no agent required on target systems. n8n connects monitoring events to
notification workflows.

### Documentation
The complete infrastructure documentation lives in Wiki.js and is automatically
synced to a separate Git repository via Wiki.js's built-in Git storage integration —
every documentation change is version-controlled and traceable.

![Wiki.js Page Graph](docs/images/wikijs.png)
*Wiki.js view of linked pages/notes and their relationships*

## Challenges & Solutions

A selection of real-world problems encountered while operating the infrastructure.

### DNS conflict caused by Tailscale
Tailscale's MagicDNS modified the host DNS configuration, causing Docker
containers to lose external DNS resolution. I diagnosed the issue across
the host and container network stack and resolved it by configuring an
independent DNS resolver for Docker.

### Data loss caused by automatic container updates
A container lost its configuration after Watchtower updated it because
its data directory was not persisted correctly. I identified the missing
volume mapping, corrected the deployment configuration, and excluded
critical services from automatic updates.

### Proxmox VM network connectivity
A VM repeatedly failed to obtain a DHCP lease despite a correctly
configured virtual bridge. I narrowed the issue down through host-side
network diagnostics and identified the physical network configuration
as the likely cause.

## Security

Security decisions are treated as part of the infrastructure design:

- No direct port forwarding to the public internet
- Remote access through Tailscale
- TLS for internal web services
- Service isolation through dedicated virtual machines where appropriate
- Separate service credentials
- Restricted service exposure through the reverse proxy
- Automatic updates selectively enabled rather than globally trusted

## Roadmap

### Gamma — Storage & Backup Server

**Status:** Planning

Planned capabilities:

- ZFS-based redundant storage
- Automated snapshots
- SMB/NFS file shares
- Centralized backups
- Document and media management
- Monitoring integration
