# SIEM Lab Architecture

## Purpose
This document describes the data flow in the Windows log-monitoring lab.

## Data flow
1. Windows 11 generates Windows Event Log records.
2. Filebeat reads the configured Security, System, and Application channels.
3. Filebeat sends events to Elasticsearch over HTTPS on TCP port 9200.
4. Elasticsearch indexes the incoming documents and makes them searchable.
5. Kibana connects to Elasticsearch and provides Discover, visualizations, and dashboards over TCP port 5601.

## Components
| Component | Role |
|---|---|
| Windows 11 | Source of event logs |
| Filebeat | Collects and forwards logs |
| Elasticsearch | Indexes, stores, and searches events |
| Kibana | Search interface and visualization |
| Ubuntu Server | Hosts Elasticsearch and Kibana |
| VirtualBox host-only network | Allows the lab VMs to communicate privately |

## Lab addressing
- Ubuntu SIEM server: `192.168.56.104`
- Windows 11: `192.168.56.101`
- Host-only network: `192.168.56.0/24`

Confirm these addresses with `ip addr` on Ubuntu and `ipconfig` on Windows if the lab is moved or reconfigured.

## Ports
- Elasticsearch HTTPS: TCP `9200`
- Kibana web interface: TCP `5601`

## Important distinction
Filebeat is a log shipper; it does not independently classify every event as malicious. Detection rules, alerting, and incident-response workflows require additional configuration.
