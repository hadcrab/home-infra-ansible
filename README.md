# Home infrastructure

Ansible manages Debian nodes and assigns services through inventory groups.
The Raspberry Pi currently runs Docker, Pi-hole with Unbound, the Beszel Hub,
and a Beszel Agent. The ASUS node runs a Beszel Agent only.

Run the full site playbook with:

```sh
ansible-playbook playbooks/site.yml
```

Run one service on one node with tags and a host limit. For example, deploy
only the Beszel Agent to the ASUS node:

```sh
ansible-playbook playbooks/site.yml --limit morituri --tags beszel_agent
```

## Assigning services

Edit `inventory/hosts.ini` to assign each node to the service groups it should
run:

- `debian`: common Debian package maintenance.
- `container_hosts`: derived group; Docker Engine and Compose are installed on
  every node assigned a containerized service.
- `dns_nodes`: Pi-hole with Unbound.
- `beszel_hub`: the single Beszel Hub.
- `beszel_agents`: nodes monitored by that Hub.

To add a Debian node, add its inventory alias under `[debian]`, add a matching
`inventory/host_vars/<alias>.yml` with its address, SSH user, and sudo password
variable, then add it to the groups for the services it should run. Membership
in `dns_nodes`, `beszel_hub`, or `beszel_agents` automatically adds the host to
`container_hosts`, so Docker is installed as a prerequisite. The private SSH
key stays on the Ansible control machine; every node uses its public key.

Per-node secrets live in the encrypted `inventory/group_vars/server/vault.yml`.
Use the existing `vault_pi_*` and `vault_morituri_*` names as examples when
adding host variables. Beszel Hub credentials are `vault_beszel_email` and
`vault_beszel_pass`.

## Current service addresses and data

- Pi-hole: `http://<rasp-ip>:8080/admin`, DNS port `53`.
- Pi-hole data: `/opt/homelab/dns/pihole`.
- DNS Compose file: `/opt/homelab/dns/compose.yml`.
- Beszel Hub: `http://<rasp-ip>:8090`.
- Beszel Hub data: `/opt/homelab/beszel/data`.
- Beszel Agent data on the Pi: `/opt/homelab/beszel/agent`.

The existing Raspberry Pi data paths are retained so a playbook run does not
move or recreate application data. New nodes use `/opt/services` by default.

The Beszel Hub role creates the first account from the vaulted credentials,
enables a permanent universal token, and stores the Hub key and token in a
root-only file so Agent-only runs can retrieve them over SSH. The Agent mounts
the Docker socket on Agent hosts to report container metrics.

To set a new Pi-hole web password, run on the Pi:

```sh
sudo docker-compose -f /opt/homelab/dns/compose.yml exec pihole pihole setpassword
```
