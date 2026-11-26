# Linux Network Administration

A Linux-based network administration project integrating Web Server, DNS Server, DHCP Server, and Wireless Access Point services within a Local Area Network (LAN).

This project demonstrates the deployment and integration of multiple network services using Ubuntu Linux, allowing wireless clients to obtain network configurations automatically and access locally hosted websites through custom domain names.

## Table of Contents

- [Project Overview](#project-overview)
- [Technologies Used](#technologies-used)
- [Network Architecture](#network-architecture)
- [Network Configuration](#network-configuration)
- [Implemented Services](#implemented-services)
- [Additional Security Features](#additional-security-features)
- [Monitoring Scripts](#monitoring-scripts)
- [Challenges and Troubleshooting](#challenges-and-troubleshooting)
- [Project Structure](#project-structure)
- [Learning Outcomes](#learning-outcomes)

## Project Overview

The objective of this project was to build an integrated Linux-based network environment that provides essential network services to connected client devices.

A single Ubuntu machine was configured to host multiple network services, including Apache2, BIND9, ISC DHCP Server, and Hostapd.

A network bridge was configured to connect the wireless access point with the internal network services.

Wireless clients can connect to the configured Wi-Fi network, receive IP addresses automatically through DHCP, resolve local domain names through DNS, and access websites hosted on the Apache2 web server.

## Technologies Used

| Technology | Purpose |
|---|---|
| Ubuntu Linux | Operating system |
| Apache2 | Web hosting |
| BIND9 | DNS services |
| ISC DHCP Server | Automatic IP allocation |
| Hostapd | Wireless Access Point |
| Bridge Utils | Network bridging |
| UFW | Firewall management |
| Bash | Monitoring scripts |
| Netplan | Network interface configuration |
| VirtualBox | Virtualized environment |

## Network Architecture

The project uses a single Ubuntu machine to provide multiple network services.

The services are logically separated through multiple IP addresses assigned to the network bridge (`br0`).

### Network Services

1. **Web Server:** Hosts multiple websites using Apache2 virtual hosting.
2. **DNS Server:** Resolves local domain names into IP addresses.
3. **DHCP Server:** Automatically assigns IP addresses and network settings to clients.
4. **Wireless Access Point:** Provides wireless connectivity through Hostapd.
5. **Firewall:** Controls incoming network traffic using UFW.

### Network Workflow

1. A client connects to the wireless network.
2. DHCP assigns an IP address and provides DNS server information.
3. The client requests a local domain name.
4. BIND9 resolves the domain name to the web server IP address.
5. Apache2 receives the HTTP request and delivers the requested website.

## Network Configuration

| Configuration | Value |
|---|---|
| Network Address | 192.168.26.0/24 |
| Web Server | 192.168.26.10 |
| DNS Server | 192.168.26.20 |
| Gateway | 192.168.26.1 |
| DHCP Range | 192.168.26.100 – 192.168.26.200 |
| Wireless SSID | NetworkAdmin_26 |
| Wireless Channel | 11 |
| Authentication | WPA2 |

## Implemented Services

### 1. Web Server — Apache2

Apache2 was configured to host multiple websites using name-based virtual hosting.

**Implemented Features:**

- Virtual hosting for multiple websites using one server.
- Maximum of two simultaneous Apache request workers.
- Basic authentication for a restricted website directory.
- Separate access and error logs.
- Automatic service startup.

**Hosted Websites:**

| Domain | Description |
|---|---|
| www.aeson.local | Aeson Cybersecurity Lab |
| www.cyberlab.local | Cyberlab Portal |

### 2. DNS Server — BIND9

BIND9 was configured to provide local domain name resolution.

**Implemented Features:**

- Forward lookup zones.
- Reverse lookup zone.
- DNS domain aliases using CNAME records.
- DNS forwarding to external DNS servers.
- Restricted DNS queries to the local subnet.
- Automatic service startup.

**Configured Domains:**

- `aeson.local`
- `cyberlab.local`

**DNS Testing:**

```bash
sudo named-checkconf

sudo named-checkzone aeson.local /etc/bind/db.aeson.local

nslookup www.aeson.local
```

### 3. DHCP Server — ISC DHCP

ISC DHCP Server was configured to automatically distribute IP addresses and network settings to connected clients.

**Implemented Features:**

- Dynamic IP allocation between 192.168.26.100 and 192.168.26.200.
- Reserved IP addresses for the Web Server and DNS Server.
- DNS server advertisement to DHCP clients.
- Configurable lease duration.
- Automatic service startup.

**DHCP Lease Configuration:**

| Setting | Duration |
|---|---|
| Default Lease Time | 5 hours |
| Maximum Lease Time | 10 hours |

### 4. Wireless Access Point — Hostapd

Hostapd was configured to turn a wireless network adapter into a wireless access point.

**Implemented Features:**

- Custom SSID configuration.
- Wireless channel selection.
- WPA2 authentication.
- Network bridging through `br0`.
- Wireless throughput monitoring.
- Automatic service startup.

**Wireless Configuration:**

| Setting | Value |
|---|---|
| SSID | NetworkAdmin_26 |
| Channel | 11 |
| Security | WPA2 |
| Bridge Interface | br0 |

Wireless clients can access the internal network services after successfully connecting to the access point.

## Additional Security Features

### Uncomplicated Firewall (UFW)

UFW was implemented to control incoming and outgoing network traffic.

The firewall uses a default-deny incoming policy and allows outgoing connections by default.

**Allowed Services:**

| Port | Protocol | Service |
|---|---|---|
| 22 | TCP | SSH |
| 53 | TCP/UDP | DNS |
| 67–68 | UDP | DHCP |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |

**Firewall Management:**

```bash
sudo ufw default deny incoming

sudo ufw default allow outgoing

sudo ufw enable

sudo ufw status verbose
```

## Monitoring Scripts

Two Bash scripts were developed to monitor network and server performance.

### 1. Web Server Monitoring

File: `monitor_web.sh`

The script monitors:

- Current connected HTTP clients.
- CPU utilization.
- Network download speed.
- Network upload speed.

### 2. Wireless Access Point Monitoring

File: `monitor_ap_throughput.sh`

The script monitors:

- Wireless interface incoming traffic.
- Wireless interface outgoing traffic.
- Network throughput in KB/s.
- Real-time bandwidth changes.

Both scripts use Linux system information to calculate performance metrics.

## Challenges and Troubleshooting

During implementation, several technical challenges were encountered.

**1. Wireless Adapter Compatibility**

The initial wireless adapter was not supported by Ubuntu running in VirtualBox. A different wireless adapter was obtained, and its driver was installed successfully.

**2. NetworkManager Interface Conflict**

NetworkManager repeatedly reclaimed the wireless interface, preventing Hostapd from maintaining access point mode.

The issue was resolved by configuring NetworkManager to treat the wireless interface as unmanaged.

**3. DNS Configuration Errors**

Incorrect DNS configuration caused the BIND9 service to fail.

Commands such as `named-checkconf` and `named-checkzone` were used to validate configuration files before restarting the service.

**4. DHCP Address Allocation**

New client devices initially received automatically assigned addresses outside the intended DHCP range.

IP release and renewal were performed to obtain the correct DHCP configuration.

**5. Firewall Configuration**

The default-deny incoming policy initially created potential connectivity issues for legitimate services.

The required firewall rules were configured before enabling UFW to ensure that SSH, DNS, DHCP and web services remained accessible.

## Project Structure

```text
linux-network-administration/
│
├── README.md
│
├── apache/
│   ├── aeson.conf
│   ├── cyberlab.conf
│   └── mpm_event.conf.example
│
├── dns/
│   ├── named.conf.local
│   ├── named.conf.options.example
│   ├── db.aeson.local
│   ├── db.cyberlab.local
│   └── db.192
│
├── dhcp/
│   └── dhcpd.conf.example
│
├── wireless/
│   └── hostapd.conf.example
│
├── firewall/
│   └── ufw-configuration.md
│
├── scripts/
│   ├── monitor_web.sh
│   └── monitor_ap_throughput.sh
│
└── screenshots/
    ├── network-architecture.png
    ├── apache-virtual-hosts.png
    ├── dns-configuration.png
    ├── dhcp-configuration.png
    ├── wireless-access-point.png
    └── firewall-configuration.png
```

## Learning Outcomes

Through this project, I gained practical experience in:

- Linux server installation and administration.
- Configuring Apache2 virtual hosting and authentication.
- Implementing DNS forward and reverse lookup zones.
- Configuring DHCP address allocation and reservation.
- Deploying a wireless access point using Hostapd.
- Integrating multiple network services through network bridging.
- Managing network security using UFW.
- Developing Bash scripts for network monitoring.
- Troubleshooting network connectivity and service configuration issues.

---

**Project Information**

- Course: NET2103 – Network & System Administration
- Project Type: Group Coursework
- Environment: Ubuntu Linux / VirtualBox

**Note:** This repository contains sanitized configuration examples and documentation. Credentials, private authentication data and hardware-specific identifiers are excluded. Some configurations may require adaptation to the target environment.
