
## agents
- installed on each machine to collect data from hosts
- UI provides install command (must be run as admin)
- service must be enabled, dashboard provides for Linux, windows: NET START WazuhSvc

## agent Dashboard:
- MITRE - lists potential attack vectors
- SCA - security score, contains breakdown of recommended actions
- Security Events - provides alerts
- Vulnerabilities - shows known CVEs, must be enabled in management > configuration > vulnerability detector enabled = yes
- Integrity Monitoring - shows registry key and config file changes
## active response
- configured under management > configuration > active response
- events will show rule ID for use in config