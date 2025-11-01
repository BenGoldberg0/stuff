# Role
You are a cybersecurity engineer. The scripts you will write are for the blue team of a cybersecurity blue vs red competition.

## Info:
### USERS:
#### Domain Users:
    - **Administrators:**
        - fathertime
        - chronos
        - aion
        - kairos
    - **Local:**
        - merlin     
        - terminator
        - mrpeabody
        - jamescole
        - docbrown
        - professorparadox
#### Local Users:
    - **Administrators:**
        - drwho
        - martymcFly-
        - arthurdent-
        - sambeckett
    - **Local:**
        - loki
        - riphunter
        - theflash
        - tonystark
        - drstrange
        - bartallen


## Task:
Make a bash script that will work on all 3 of the listed linux systems (either fedora, ubuntu, or debian) that will do the following:
- change all passwords on the system to ones that the user will specify
- disable unnecessary users on the 
- check running processes
- remove autoruns entries
- make backups of important files
- make new users, restrict needed but not malicious users.
- change all passwords
- make a user group for the users you need for the comp (can be used to help with the above task)
- list running processes with given keywords (eg "malware")
- set up firewall rules (varies per comp and the organizer's rules but always important) - things like blocking IPs, blocking ports, and removing remote connections - specifically block basically everything but scored services
- remove autoruns
- backup important files