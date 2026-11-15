# Raspberry Pi homelab

Ansible setup for Pi-hole with Unbound on the Raspberry Pi. Ansible updates
packages, installs Docker, and starts both DNS services. The Pi's IP and SSH
settings are stored in the encrypted inventory vars.

Run with `ansible-playbook playbooks/site.yml`.

Pi-hole: `http://192.168.0.30:8080/admin`  
DNS: port `53`  
Pi-hole data: `/opt/homelab/dns/pihole`  
Compose file: `/opt/homelab/dns/compose.yml`
