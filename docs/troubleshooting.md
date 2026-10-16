# Troubleshooting Notes

## Kibana could not connect to Elasticsearch

### Symptom
Kibana logs reported that it could not retrieve Elasticsearch version information and that there was a self-signed certificate in the certificate chain.

### Checks
```bash
sudo systemctl status elasticsearch
curl -k -u elastic https://localhost:9200/
sudo systemctl status kibana
sudo journalctl -u kibana -n 50 --no-pager
```

### Resolution used in this lab
1. Confirmed Elasticsearch responded over HTTPS.
2. Changed Kibana's `elasticsearch.hosts` from `http://localhost:9200` to `https://localhost:9200`.
3. Temporarily set `elasticsearch.ssl.verificationMode: none` for the self-signed lab certificate.
4. Restarted Kibana.
5. Checked the service logs and tested port 5601.

Kibana then became accessible and login succeeded.

**Security note:** This bypass is only a lab workaround. Production deployments should use a trusted certificate authority and proper certificate verification.

## Filebeat event ingestion checks

The following tests were used on Windows:

```powershell
.\filebeat.exe test config -c .\filebeat.yml
.\filebeat.exe test output -c .\filebeat.yml
```

The configuration and output tests passed. Filebeat logs showed inputs initializing and events being published. The Elasticsearch document count also increased.

## Events not visible in Kibana

If this happens:
1. Confirm Filebeat is running and its output test passes.
2. Check the Elasticsearch document count for `filebeat-*`.
3. In Kibana Discover, select the `Windows SIEM` data view.
4. Expand the time range and refresh.
5. Inspect the timestamp field and available event fields.

## Useful commands

```bash
sudo systemctl status elasticsearch
sudo systemctl status kibana
sudo journalctl -u kibana -n 50 --no-pager
curl -k -u elastic 'https://localhost:9200/filebeat-*/_count?pretty'
```
