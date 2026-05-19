# Cloud Honeypot SOC Lab

**A cloud-native Security Operations Center (SOC) detection engineering lab built in Microsoft Azure, designed to capture real-world attack telemetry, develop reusable KQL detection logic, and demonstrate end-to-end SOC workflows applicable to critical infrastructure defense.**

This project is openly shared for use by SOC analysts, detection engineers, and security operations teams seeking to build or improve behavioral detection capabilities against real-world brute force and credential-based attack patterns.

---

## Why This Project Exists

The most consequential threats targeting critical infrastructure today do not announce themselves. Nation-state actors and advanced persistent threat groups operate slowly, reuse legitimate credentials, and deliberately blend into normal network activity to avoid triggering traditional rule-based detection.

This lab was built to work with real attacker behavior, not simulated traffic, in order to develop detection logic grounded in authentic attack patterns. Every KQL query, enrichment technique, and visualization methodology in this repository was developed against live attack telemetry captured from the public internet.

The goal is not just to demonstrate what was built, but to provide a reusable framework that other defenders can adapt for their own environments.

---

## What Is in This Repository

| Folder | Contents |
|---|---|
| `kql/` | Reusable KQL detection queries for brute force, GeoIP enrichment, IP frequency, and country-level analysis |
| `watchlists/` | Sample GeoIP dataset for Sentinel Watchlist integration |
| `documentation/` | Full lab report, architecture diagrams, and annotated screenshots |

---

## Lab Architecture

**Core Components**

- Azure Virtual Machine (Windows 11 Enterprise) with intentionally exposed RDP port (TCP 3389)
- Log Analytics Workspace for centralized log collection
- Microsoft Sentinel SIEM for detection, alerting, and investigation
- GeoIP Watchlist (55,000+ entries) for attacker IP enrichment
- Sentinel Workbook for global attack visualization

**How It Works**

The honeypot VM was deployed with a public IP and an open network security group to attract real-world scanning and brute force activity. Windows Security Event ID 4625 (failed logon attempts) were forwarded to the Log Analytics Workspace via the Azure Monitor Agent. Custom KQL queries were developed to extract, enrich, and analyze attacker behavior. A GeoIP watchlist was joined against the raw logs to add geographic context. Results were visualized in a Sentinel Workbook displaying a global attack map, top attacking IPs, and time-based attack frequency.

---

## Real-World Attack Telemetry

Within the first 10 hours of deployment, the honeypot recorded:

- **12,000+ failed login attempts**
- **Activity from 37 countries**
- Persistent brute force waves using common username lists
- Repeated attacker IPs demonstrating automated, infrastructure-backed campaigns

This is live attacker behavior, not simulated traffic. The telemetry reflects the actual scanning and credential-stuffing activity that production SOC environments encounter daily.

---

## KQL Detection Queries

The queries in this repository are written to be adapted for production SOC environments. Each targets a specific detection objective.

### Failed Login Detection

Extracts failed logon events with attacker IP, targeted account, and timestamp for triage and escalation workflows.

```kql
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Account, IPAddress = IpAddress, Activity
```

### GeoIP Enrichment

Joins raw failed logon events against the GeoIP watchlist to add geographic context, enabling country-level threat profiling and attack origin mapping.

```kql
SecurityEvent
| where EventID == 4625
| project IPAddress = IpAddress, TimeGenerated, Account
| join kind=leftouter Watchlist_GeoIP on $left.IPAddress == $right.ip
| project TimeGenerated, IPAddress, Account, country, region, city, latitude, longitude
```

### IP Frequency Analysis

Identifies the highest-volume attacking IPs to support threat prioritization and blocklist enrichment.

```kql
SecurityEvent
| where EventID == 4625
| summarize AttemptCount = count() by IpAddress
| sort by AttemptCount desc
```

### Country-Level Summary

Aggregates attack volume by country of origin to support geographic threat profiling and inform detection rule tuning.

```kql
SecurityEvent
| where EventID == 4625
| project IPAddress = IpAddress, TimeGenerated
| join kind=leftouter Watchlist_GeoIP on $left.IPAddress == $right.ip
| summarize Attempts = count() by country
| sort by Attempts desc
```

---

## How to Use This in Your Environment

These queries and methodologies are designed to be adapted for production SOC environments. To deploy this lab or apply these detection patterns:

1. **Deploy a Log Analytics Workspace and Microsoft Sentinel** in your Azure environment.
2. **Import the GeoIP watchlist** from the `watchlists/` folder into Sentinel as a custom watchlist named `Watchlist_GeoIP`.
3. **Copy the KQL queries** from the `kql/` folder into Sentinel Analytics Rules or the Log Analytics query editor.
4. **Adjust the join key** (`ip`) if your watchlist uses a different column name for IP addresses.
5. **Build a Sentinel Workbook** using the geographic coordinates returned by the GeoIP enrichment query to visualize attack origin on a world map.

The full setup walkthrough, architecture diagrams, and annotated screenshots are available in the `documentation/` folder.

---

## Detection Engineering Observations

Working against live attack telemetry produced several findings relevant to SOC detection design:

- A single exposed RDP port attracted global automated scanning within minutes of deployment, confirming that internet-facing assets require immediate detection coverage regardless of how recently they were provisioned.
- Attackers demonstrated clear patterns of IP reuse and common username targeting, making IP frequency analysis and username-based detection rules high-value additions to any SOC ruleset.
- GeoIP enrichment significantly improved triage efficiency by enabling rapid geographic filtering, allowing analysts to prioritize high-risk origin countries and deprioritize known research or scanning infrastructure.
- The volume and persistence of brute force activity against a single endpoint underscores why behavioral baseline detection is a necessary complement to rule-based controls. Rule-based systems can flag individual events; behavioral analytics surface the pattern that reveals campaign-level activity.

---

## Repository Structure

```
cloud-honeypot-soc-lab/
|
|-- README.md
|-- LICENSE
|
|-- documentation/
|   |-- SOC-Lab-Report.pdf
|   |-- SOC-Lab-Report.docx
|   |-- images/
|   |   |-- lab-setup/
|   |   |-- log-collection/
|   |   |-- sentinel-config/
|   |   |-- enrichment/
|   |   |-- visualization/
|   |   |-- results/
|   |-- diagrams/
|       |-- soc-architecture.png
|
|-- kql/
|   |-- failed-logins.kql
|   |-- geoip-enrichment.kql
|   |-- country-summary.kql
|   |-- ip-frequency-analysis.kql
|
|-- watchlists/
    |-- geoip-dataset-sample.csv
```

---

## Connect

- LinkedIn: [linkedin.com/in/william-omolade](https://www.linkedin.com/in/william-omolade/)
- GitHub: [github.com/02William](https://github.com/02William)
