# debian-firewall

Ansible roles to deploy stateful perimeter firewalls on Debian 13 (Trixie), with optional HA clustering.

## Features

- nftables stateful firewall with sane defaults
- High Availability via Keepalived VRRP
- Session synchronization via conntrackd
- WireGuard VPN (standalone or HA-aware)
- IPsec VPN via strongSwan (standalone or HA-aware)
- Dynamic routing via FRRouting (OSPF, BGP)
- systemd-networkd network configuration
- Fully idempotent Ansible roles
- ansible-lint and yamllint compliant

## Requirements

- Debian 13 (Trixie)
- Ansible >= 2.15
- Python >= 3.11

## Roles

| Role | Description |
|------|-------------|
| `system` | Base system checks and assertions |
| `network` | Interfaces, routes and sysctl via systemd-networkd |
| `nftables` | Stateful firewall rules with built-in protection |
| `keepalived` | VRRP high availability |
| `conntrackd` | Connection state synchronization |
| `wireguard` | WireGuard VPN tunnels |
| `strongswan` | IPsec VPN tunnels |
| `frr` | Dynamic routing (OSPF, BGP) |

## Quick Start

```bash
git clone https://github.com/ecritel/debian-firewall
cd debian-firewall
cp inventory.yml.example inventory.yml
cp group_vars/example.yml group_vars/all.yml
ansible-playbook -i inventory.yml site.yml
```

## Architecture

### Standalone firewall

```yaml
keepalived_enabled: false
wireguard_enabled: true
nftables_enabled: true
```

### HA cluster

```yaml
keepalived_enabled: true
conntrackd_enabled: true
wireguard_enabled: true
nftables_enabled: true
```

When `keepalived_enabled: true`:
- WireGuard tunnels are managed by Keepalived notify scripts
- strongSwan tunnels are managed by Keepalived notify scripts
- conntrackd synchronizes connection states between firewalls
- The MASTER firewall starts VPN services, the BACKUP stops them

## nftables Default Rules

The `nftables` role automatically injects the following rules before any custom rules:

**input chain:**
- Drop invalid packets
- Accept established/related connections
- Accept loopback
- ICMP rate limit (10/s burst 20)
- Drop IP fragments
- SYN flood protection (100/s burst 200)
- SSH rate limit (10/min burst 5)

**forward chain:**
- Drop invalid packets
- Accept established/related connections
- Drop IP fragments

## Adding a NAT rule

In `group_vars` or `host_vars`:

```yaml
nftables_config:
  tables:
    firewall:
      chains:
        prerouting:
          rules:
            - raw: >-
                iifname $wan_interface tcp dport 443
                dnat ip to 10.0.0.10:443
        forward:
          rules:
            - raw: >-
                iifname $wan_interface oifname $lan_interface
                tcp dport 443 ip daddr 10.0.0.10 accept
```

## License

MIT
