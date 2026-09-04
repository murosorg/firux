# debian-firewall

Ansible roles to deploy stateful perimeter firewalls on Debian 13.

This project was born from a simple observation: most open-source firewall projects either rely on proprietary appliances, complex web UIs, or opinionated distributions that hide what is actually happening underneath.

This project takes a different approach. It uses plain Debian 13 with nftables, and exposes the full configuration through standard Ansible roles. No abstraction layer, no GUI, no magic. Just a clean, auditable, reproducible firewall that you control entirely.

It supports both standalone and HA cluster deployments, with WireGuard and IPsec VPN, dynamic routing via FRRouting, and stateful session synchronization via conntrackd.

## Roles

| Role | Description |
|------|-------------|
| `network` | Base Debian 13 assertions, interfaces, routes and sysctl via systemd-networkd |
| `nftables` | Stateful firewall with built-in protection defaults |
| `keepalived` | VRRP high availability |
| `conntrackd` | Connection state synchronization |
| `wireguard` | WireGuard VPN, HA-aware |
| `strongswan` | IPsec VPN, HA-aware |
| `frr` | Dynamic routing: OSPF, BGP |

## Requirements

- Debian 13 (Trixie)
- Ansible >= 2.15

## License

MIT
