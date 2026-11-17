# Raspberry Pi homelab

Ansible setup for Pi-hole with Unbound and Beszel on the Raspberry Pi. Ansible
updates packages, installs Docker, and starts the services. The Pi's IP and SSH
settings are stored in the encrypted inventory vars.

Run with `ansible-playbook playbooks/site.yml`.

Pi-hole: `http://192.168.0.30:8080/admin`  
DNS: port `53`  
Pi-hole data: `/opt/homelab/dns/pihole`  
Compose file: `/opt/homelab/dns/compose.yml`

Beszel Hub: `http://192.168.0.30:8090`

Beszel data: `/opt/homelab/beszel/data`

Compose file: `/opt/homelab/beszel/compose.yml`

The playbook creates the initial admin account from `vault_beszel_email` and
`vault_beszel_pass`, enables Beszel's permanent agent registration token,
registers the Raspberry Pi, and starts its Agent. The Compose file containing
these credentials is stored with root-only permissions. The Agent listens on
port `45876` and mounts the Docker socket with `:ro` to collect container
metrics.

To set a new Pi-hole web password, run on the Pi. The command prompts for it
without displaying the password as you type:

```sh
sudo docker-compose -f /opt/homelab/dns/compose.yml exec pihole pihole setpassword
```
