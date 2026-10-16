# Installation and Configuration Notes

These notes document the configuration performed in the lab. Package repository setup and some installation commands can differ by operating-system image and Elastic Stack version.

## 1. Lab environment
- Kali Linux host
- Oracle VirtualBox
- Ubuntu Server VM: `192.168.56.104`
- Windows 11 VM: `192.168.56.101`
- Host-only subnet: `192.168.56.0/24`
- Elasticsearch: Latest version*
- Filebeat: Latest version *

Ubuntu also had a NAT adapter for internet access.

## 2. Check Elasticsearch on Ubuntu

```bash
sudo systemctl status elasticsearch
```

Start or enable the service if needed:

```bash
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```

Test the local HTTPS endpoint:

```bash
curl -k -u elastic https://localhost:9200/
```

The `-k` option bypasses certificate verification. It was used for this isolated lab's self-signed certificate and is not recommended for production.

Check the number of matching Filebeat documents:

```bash
curl -k -u elastic 'https://localhost:9200/filebeat-*/_count?pretty'
```

A count of 1,702 was observed during one test session; this is a historical test result, not a permanent expected value.

## 3. Filebeat on Windows

Filebeat 9.5.5 was downloaded and extracted on Windows 11. The relevant input configuration in `filebeat.yml` was:

```yaml
filebeat.inputs:
  - type: winlog
    name: Security

  - type: winlog
    name: System

  - type: winlog
    name: Application
```

The Elasticsearch output was configured to use the Ubuntu server:

```yaml
output.elasticsearch:
  hosts: ["https://192.168.56.104:9200"]
  username: "elastic"
  password: "${ELASTIC_PASSWORD}"
  ssl.verification_mode: none
```

`ELASTIC_PASSWORD` is a placeholder variable in this example. The exact way environment variables are set depends on how Filebeat is launched on Windows. Replace it with a secure secret-management method appropriate for your environment; do not publish your real password.

The `ssl.verification_mode: none` setting reflects the temporary lab workaround. For production, use a trusted CA certificate and verify the server certificate.

### Test Filebeat configuration

Run PowerShell in the Filebeat directory:

```powershell
.\filebeat.exe test config -c .\filebeat.yml
```

### Test the output connection

```powershell
.\filebeat.exe test output -c .\filebeat.yml
```

Both tests succeeded during the lab session. Filebeat logs showed Windows Event Log inputs initializing and events being published.

## 4. Kibana on Ubuntu

The relevant settings in `/etc/kibana/kibana.yml` were:

```yaml
server.host: "0.0.0.0"
elasticsearch.hosts: ["https://localhost:9200"]
elasticsearch.username: "kibana_system"
elasticsearch.ssl.verificationMode: none
```

The internal `kibana_system` user is for Kibana-to-Elasticsearch service communication. Use the `elastic` account or another appropriately authorized user for browser login, not `kibana_system`.

Restart and check Kibana:

```bash
sudo systemctl restart kibana
sudo systemctl status kibana
sudo journalctl -u kibana -n 50 --no-pager
curl -I http://localhost:5601
```

Open Kibana from a machine that can reach the host-only network:

```text
http://192.168.56.104:5601
```

Kibana became accessible after its Elasticsearch URL was changed from HTTP to HTTPS and certificate verification was temporarily disabled for the self-signed certificate.

## 5. Kibana data view and Discover

A data view was created with:
- Name: `Windows SIEM`
- Index pattern: `filebeat-*`
- Timestamp field: `@timestamp`

In **Analytics → Discover**, select `Windows SIEM`, choose a suitable time range (for example, Last 24 hours), refresh, and inspect available event fields. Fields can include `@timestamp`, `winlog.channel`, `event.code`, `message`, and `host.name`, depending on the event.

## 6. Visualization and dashboard

A Lens line chart was saved as `Windows Events Over Time`:
- Data view: `Windows SIEM`
- X-axis: `@timestamp` date histogram
- Y-axis: Count of records

A dashboard named `Windows SIEM Dashboard` was created and the visualization was added.

## 7. Security reminders
- Never commit passwords or secrets.
- Do not publish raw event exports if they contain private information.
- The `ssl.verification_mode: none` workaround weakens TLS authentication and should not be used in production.
- Restrict network access to Elasticsearch and Kibana to trusted systems.
