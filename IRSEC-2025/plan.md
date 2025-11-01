## **Phase 1: Initial Access & Situational Awareness (0-5 minutes)**

###
   **run the following on linux systems to have permisisons to run the scripts.
   ```bash
   chmod +x IRSeC-2025/linux/scripts/*.sh
   chmod +x IRSeC-2025/wazuh/setup/*.sh
   ```

### All Systems
1. **Change default passwords immediately**
   - Admin/root accounts
   - Service accounts
   - Application passwords

### Central Management
2. **Access Wazuh Dashboard** (Dino Asteroid server)
   - Verify all agents are reporting
   - Check for existing alerts/incidents
   - Note any missing agents

## **Phase 2: Quick Wins - Automated Hardening (5-15 minutes)**

### Linux Systems
Run scripts in this order:
```bash
# 1. Audit and remove unauthorized users
sudo ./user_audit.sh

# 2. Scan for suspicious files/processes
sudo ./file_scanner.sh
sudo ./process_scanner.sh
sudo ./user_scanner.sh

# 3. Check bash integrity
sudo ./check_bash_integrity.sh

# 4. Audit cron jobs
sudo ./cron_audit.sh
```

### Windows Systems
Run PowerShell scripts as Administrator:
```powershell
# 1. Remove unauthorized domain users
.\disable_unauthorized_users.ps1

# 2. Reset passwords for authorized users
.\harden_domain_passwords.ps1  # For domain controllers
.\harden_local_passwords.ps1   # For local systems

# 3. Scan for threats
.\process_scanner.ps1
.\service_scanner.ps1
.\Sus_DLL_Hunter.ps1
.\sus_registry_hunter.ps1
```

## **Phase 3: Wazuh Deployment & Monitoring (15-25 minutes)**

### If Wazuh Not Installed
```bash
# On Wazuh server (modify automated_install.sh first)
cd /wazuh/setup/
# Edit WAZUH_SERVER_IP_ADDRESS in automated_install.sh
sudo ./automated_install.sh
```

### Configure Centralized Monitoring
1. **Deploy agent configurations**
   ```bash
   # Copy agent configs to Wazuh server
   scp agent_conf/linux/agent.conf wazuh-server:/var/ossec/etc/shared/linux/
   ```

2. **Create agent groups**
   - Group: 'windows' for all Windows hosts
   - Group: 'linux' for all Linux hosts

3. **Monitor critical areas via Wazuh**
   - File integrity monitoring on system directories
   - Authentication logs
   - Network connections
   - Process creation

## **Phase 4: Network Security (25-35 minutes)**

### Firewall Configuration
1. **Review and harden firewall rules**
   - Block unnecessary outbound connections
   - Whitelist only required ports:
     - Wazuh: 1514, 1515, 55000, 443 (dashboard)
     - SSH: 22 (restrict to jump hosts if possible)
     - RDP: 3389 (Windows, restrict source IPs)
     - Application-specific ports only as needed

2. **Enable logging on firewall**
   - Forward logs to Wazuh server

### Linux iptables/ufw
```bash
# Quick lockdown (adjust based on services)
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from <admin_network> to any port 22
sudo ufw allow 1514/tcp  # Wazuh agent
sudo ufw enable
```

### Windows Firewall
```powershell
# Enable Windows Firewall
netsh advfirewall set allprofiles state on
# Block all inbound by default except required services
netsh advfirewall set allprofiles firewallpolicy blockinbound,allowoutbound
```

## **Phase 5: Service Hardening (35-45 minutes)**

### Linux
1. **Disable unnecessary services**
   ```bash
   systemctl list-units --type=service --state=running
   # Disable unnecessary services
   systemctl disable --now <service_name>
   ```

2. **Secure SSH**
   ```bash
   # Edit /etc/ssh/sshd_config
   PermitRootLogin no
   PasswordAuthentication no  # If keys are setup
   AllowUsers <specific_users>
   ```

### Windows
1. **Review services** (use service_scanner.ps1 output)
2. **Disable unnecessary services**
3. **Set service recovery options** to prevent automatic restart of suspicious services

## **Phase 6: Persistence Hunting (45-55 minutes)**

### Linux
1. **Check for backdoors**
   ```bash
   # Review results from earlier scans
   cat /var/log/blue_team_report_*.txt
   
   # Quarantine suspicious files
   sudo ./quarantine.sh <suspicious_file>
   ```

2. **Review scheduled tasks**
   - Check cron_audit.sh output
   - Remove unauthorized cron jobs
   - Check systemd timers: `systemctl list-timers`

### Windows
1. **Registry persistence** (review sus_registry_hunter.ps1 output)
2. **Scheduled tasks**
   ```powershell
   schtasks /query /fo LIST /v
   ```
3. **WMI persistence**
   ```powershell
   Get-WmiObject -Class __EventFilter -Namespace root\subscription
   ```

## **Phase 7: Continuous Monitoring & Response**

### Monitoring Dashboard Setup
1. **Wazuh Dashboard** - Keep open for real-time alerts
2. **Create custom alerts** for:
   - New user creation
   - Service/daemon installation
   - Firewall rule changes
   - Critical file modifications

### Quick Response Procedures
```bash
# Linux incident response
tail -f /var/log/auth.log          # Authentication monitoring
tail -f /var/log/syslog            # System events
ss -tulpan                         # Network connections
ps auxf                            # Process tree

# Windows incident response (PowerShell)
Get-EventLog -LogName Security -Newest 100
Get-NetTCPConnection | Where-Object {$_.State -eq "Established"}
Get-Process | Sort-Object CPU -Descending | Select-Object -First 20
```

## **Phase 8: Documentation & Communication**

### Track Everything
1. **Create tracking spreadsheet**:
   - Host | Status | Issues Found | Actions Taken | Assigned To

2. **Document all changes**:
   ```bash
   # Create change log
   echo "$(date) - $HOSTNAME - $ACTION" >> /var/log/blue_team_changes.log
   ```

3. **Communication channels**:
   - Designate primary communicator
   - Regular status updates every 15 minutes
   - Immediate notification of compromises

## **Priority Order by System Type**

1. **Domain Controllers/Auth Servers** - Highest priority
2. **Wazuh Server** - Central monitoring 
3. **Web Servers/Database Servers** - Public-facing services
4. **File Servers** - Data repositories
5. **Workstations** - User endpoints

## **Emergency Response Playbook**

If active compromise detected:
1. **Isolate** - Disconnect from network if possible
2. **Identify** - Determine attack vector and scope
3. **Contain** - Stop lateral movement
4. **Eradicate** - Remove malware/backdoors using `quarantine.sh`
5. **Recover** - Restore services
6. **Document** - Record all actions for scoring

## **Tools Quick Reference**

### Linux Commands
- Check users: `cat /etc/passwd`
- Network connections: `ss -tulpan`
- Running processes: `ps auxww`
- Check logs: `/var/log/`

### Windows Commands  
- Check users: `net user`
- Network connections: `netstat -ano`
- Running processes: `tasklist /v`
- Services: `sc query`

Remember: **Document everything**, work systematically through systems by priority, and maintain constant communication with your team!

# Immediately backup critical files
mkdir -p /root/competition_backups
cp -r /etc/passwd /etc/shadow /etc/group /etc/sudoers /root/competition_backups/

# Windows
mkdir C:\CompetitionBackups
xcopy C:\Windows\System32\config\SAM* C:\CompetitionBackups\