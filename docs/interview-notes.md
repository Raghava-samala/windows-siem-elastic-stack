# SIEM Project — Interview Notes

## 60-second project explanation
I built a Windows log-monitoring SIEM lab using Windows 11, Filebeat, Elasticsearch, and Kibana. I configured Filebeat to collect Security, System, and Application Windows Event Logs and forward them to Elasticsearch running on Ubuntu Server. I verified the Filebeat configuration and output connection, confirmed that events were indexed, created a Kibana data view, inspected events in Discover, and created an event-activity visualization and dashboard.

## Component roles
- **Filebeat:** Collects and forwards configured log events.
- **Elasticsearch:** Indexes, stores, and searches the events.
- **Kibana:** Provides the interface for searching, analyzing, and visualizing events.

## Questions and answers

### What is SIEM?
SIEM stands for Security Information and Event Management. It helps centralize and analyze logs to support security monitoring and investigations.

### Which logs did you collect?
I configured Filebeat inputs for Windows Security, System, and Application Event Log channels.

### How did you verify ingestion?
I tested Filebeat's configuration and output connectivity, checked Filebeat logs for published events, and queried Elasticsearch for the Filebeat document count.

### What is Kibana Discover?
Discover is used to search and inspect individual documents and their fields.

### What is a data view?
A data view tells Kibana which Elasticsearch indices or data streams to search. I created `Windows SIEM` with the `filebeat-*` pattern and `@timestamp`.

### Does Filebeat detect attacks?
Filebeat collects and forwards events; it does not independently detect every attack. Detection rules and alerting require additional configuration.

### What would you check if events stopped appearing?
I would check whether Windows is generating events, inspect Filebeat status and logs, test the Elasticsearch output, verify the documents in Elasticsearch, and confirm the Kibana data view and time range.

## Scope statement
The completed lab demonstrates event collection, forwarding, indexing, searching, and visualization. Do not claim automated detection rules, alerting, or incident response unless those capabilities are implemented and tested.
