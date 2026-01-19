# cloud-honeypot-soc-lab
A cloud-based SOC lab built in Azure using a honeypot VM, Microsoft Sentinel, KQL analytics, GeoIP enrichment, and global attack visualization.

Cloud SOC Honeypot Lab Using Microsoft Sentinel
A cloud-based Security Operations Center (SOC) lab built in Azure to capture real bruteforce attack traffic, enrich logs with GeoIP data, and simulate end-to-end SOC workflows, including detection, analysis, and visualization.
This project demonstrates practical experience with Microsoft Sentinel, KQL analytics, log enrichment, and global attack mapping, all utilizing real attacker telemetry collected from the public internet.

Project Overview
This lab was designed to:
* Capture real-world brute force login attempts against a honeypot VM
* Centralize Windows Security Event logs in a Log Analytics Workspace
* Enrich attacker IPs with geolocation data using a Sentinel Watchlist
* Visualize global attack patterns through Sentinel Workbooks
* Simulate SOC workflows such as detection, investigation, and reporting
The result is a fully documented, cloud-native SOC environment that showcases hands-on security engineering and threat-hunting skills.

Architecture
Core components:
* Azure Virtual Machine (Windows 11 Enterprise)
* Public IP + intentionally exposed RDP port (TCP 3389)
* Network Security Group with open inbound access
* Log Analytics Workspace (LAW)
* Microsoft Sentinel SIEM
* GeoIP Watchlist (55K+ entries)
* Sentinel Workbook for global attack visualization
Attackers scanned and targeted the VM within minutes of deployment.

Key Features
1. Real Attack Telemetry
The honeypot VM recorded:
* 12,000+ failed login attempts in the first 10 hours
* Activity from 37 countries
* Persistent bruteforce attempts using common usernames
2. Centralized Logging
Windows Security Event ID 4625 (failed logons) was forwarded to LAW using the Azure Monitor Agent.
3. KQLDriven Analysis
Custom KQL queries extracted:
* Attacker IPs
* Targeted accounts
* Timestamps
* Event metadata

4. GeoIP Enrichment
A 55Krow GeoIP CSV was imported as a Sentinel Watchlist and joined with SecurityEvent logs to add:
* Country
* Region
* City
* Latitude/Longitude
5. Global Attack Visualization
A Sentinel Workbook displayed:
* Interactive world map of attacker locations
* Bar charts of top attacking IPs
* Country-level summaries
* Time-based attack frequency

Sample KQL Queries

Filter Failed Login Attempts
	SecurityEvent
	| where EventID == 4625
	| project TimeGenerated, Account, IPAddress = IpAddress, Activity

Join With GeoIP Watchlist

	SecurityEvent
	| where EventID == 4625
	| project IPAddress = IpAddress, TimeGenerated, Account
	| join kind=leftouter Watchlist_GeoIP on $left.IPAddress == 	$right.ip
	| project TimeGenerated, IPAddress, Account, country, region, city, 	latitude, longitude

Results Summary

* Total Failed Logins: 12,000+
* Countries Involved: 37
* Attack Behavior: Waves of attempts, repeated IPs, common usernames
* Visualization: Global attack map + supporting charts

Key Learnings
Technical
* KQL is essential for threat hunting and log analysis
* GeoIP enrichment transforms raw logs into actionable intelligence
* Sentinel Workbooks provide powerful real-time visualization
Operational
* A single exposed RDP port attracts global attackers within minutes
* Attackers reuse IPs and target predictable usernames
Professional
* Building a cloud SOC lab demonstrates initiative and practical security skills
* Documenting workflows improves communication with technical and non-technical audiences

Repository Structure
azure-sentinel-soc-lab/
?
??? README.md
??? documentation/
?   ??? SOC-Lab-Report.pdf
?   ??? SOC-Lab-Report.docx
?   ??? images/
?   ?   ??? lab-setup/
?   ?   ??? log-collection/
?   ?   ??? sentinel-config/
?   ?   ??? enrichment/
?   ?   ??? visualization/
?   ?   ??? results/
?   ??? diagrams/
?       ??? soc-architecture.png
?
??? kql/
?   ??? failed-logins.kql
?   ??? geoip-enrichment.kql
?   ??? country-summary.kql
?   ??? ip-frequency-analysis.kql
?
??? watchlists/
    ??? geoip-dataset-sample.csv

Contact

LinkedIn: https://www.linkedin.com/in/william-omolade/
GitHub: https://github.com/02William
