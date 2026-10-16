# Windows Log Monitoring SIEM Using the Elastic Stack

## Overview
This project documents a basic SIEM lab built with Windows 11, Filebeat, Elasticsearch, Kibana, Ubuntu Server, and VirtualBox.

The lab collects Windows Security, System, and Application event logs, forwards them to Elasticsearch, and uses Kibana to search and visualize the data.

## Architecture

```text
Windows 11 Event Logs
        |
        v
     Filebeat
        |
        | HTTPS :9200
        v
Elasticsearch (Ubuntu Server)
        |
        v
Kibana :5601
        |
        v
Discover and Dashboard
```

## Technologies
- Windows 11: log source
- Ubuntu Server: SIEM server
- Filebeat 9.5.5: log collection and forwarding
- Elasticsearch 9.5.5: indexing, storage, and search
- Kibana: searching, analysis, and visualization
- Oracle VirtualBox: virtual lab

## Lab network
| Machine | Address |
|---|---|
| Ubuntu SIEM server | `192.168.56.104` |
| Windows 11 | `192.168.56.101` |
| Host-only subnet | `192.168.56.0/24` |

These are private lab addresses and may differ in another environment.

## Implemented
- Configured Filebeat Windows Event Log inputs for Security, System, and Application.
- Forwarded events to Elasticsearch over HTTPS.
- Tested Filebeat configuration and output connectivity.
- Verified that Filebeat documents were indexed in Elasticsearch.
- Created the `Windows SIEM` Kibana data view with `filebeat-*` and `@timestamp`.
- Used Discover to inspect event records.
- Created an event-count-over-time visualization and a `Windows SIEM Dashboard`.

## Repository guide
- `docs/installation-and-configuration.md`: setup and configuration notes
- `docs/architecture.md`: component roles and data flow
- `docs/troubleshooting.md`: issue and resolution notes
- `docs/interview-notes.md`: project explanation and interview practice
- `configs/filebeat.yml.example`: sanitized configuration example

## Security notes
Never commit real passwords, API keys, tokens, private certificates, or sensitive event data. The lab temporarily disabled TLS certificate verification to work around a self-signed certificate. For production, configure trusted certificates and certificate verification.

## Scope and limitations
This lab demonstrates log collection, indexing, searching, and visualization. Automated threat-detection rules and alerting are not claimed as implemented.

## Author
Cybersecurity learner focused on defensive security, log monitoring, and SIEM technologies.
