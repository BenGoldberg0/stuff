## in general:
- change all passwords
- disable unnecessary users
- check running processes
- remove autoruns entries
- make backups of important files
- make new users, restrict needed but not malicious users.
- change all passwords
make a user group for the users you need for the comp (can be used to help with the above task)
list running processes with given keywords (eg "malware")
set up firewall rules (varies per comp and the organizer's rules but always important) - things like blocking IPs, blocking ports, and removing remote connections - specifically block basically everything but scored services
remove autoruns
backup important files

## Windows
- Tools
	- SysInternals
	- EVERYTHING
	- .sh files
	- Windows Defender Firewall
- Persistence (Registry Keys)
	- service configs
	- current user vs local machine
	- task scheduler
	- startup folder: ``%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup``
	- task manager startups
	- firewall rules
- What to do
	- Change passwords
	- Disable (NOT remove) unnecessary users
		- ``Disable-LocalUser -Name "[username]"``
		- ``Disable-ADAccount -Identity "[username]"``
	- Check running processes
	- remove autoruns
	- make backups

## linux:
- make new users, restrict needed but not malicious users. 
	- fedora/redhat: root= wheel
	- ubuntu/debian: root = sudo
- check cron jobs, crontabs, `/etc/cron.allow` and `/etc/cron.deny` 
- **systemd**
	- services and timer
	- lock down systemd directories once confirmed they are safe
	- `sudo systemd listutils`
	- `sudo systemctl cat <suspicious_service>`
	- `sudo systemctl disable <service>` then delete. stop service before deleteing
- awk, cut, grep, sed, **LEARN BASH JESUS FUCKING CHRIST**
- use wazuh and configure it to get an alert when any files change.
- pfsense rules
- ipsec/firewalld/ufw rules
- use chatter command to make files immutable.
- check bashrc and other rc files
	- `find /home -name "\.*rc"`
- debug and prompt commands
- monitor authorized keys
- harden `/etc/ssh/sshd_config`
	- `PermitRootLogin no`
	- `AllowedUsers`
	- etc.
- dont log in as root (obviously)
- **users and priciple of least privilage:**
	- system users should not have a logic shell
	- check: `/etc/sudoers` , `/etc/shadow`, `/etc/passwd`
	- enforce UIDs and GIDs
- reinstall packages, coreutils, integrity checks: `debsums`, `rpm`, `apt`
- 
## before comp:
- test all scripts in vms
- make a list of ips to whitelist (see datadog link)
- 