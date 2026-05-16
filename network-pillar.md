# Network Pillar

> Zero Trust implementation guide for the Network pillar: segmentation, visibility, and east-west traffic control

**CISA Pillar:** Networks
**Core Question:** Is network access segmented, monitored, encrypted, and restricted to what is necessary?

See full network pillar documentation in [device-network-application-data-pillars.md](./device-network-application-data-pillars.md).

## Quick Reference: Network Maturity Stages

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Segmentation | Flat network, single VLAN | Basic VLAN segmentation | Application-level segmentation | Microsegmentation per workload |
| Encryption | Perimeter encryption only | Internal encryption starting | All sensitive traffic encrypted | All network traffic encrypted |
| DNS filtering | None or basic | DNS security service deployed | DNS filtering with threat intelligence | DNS as a signal source for ZT policy |
| Traffic visibility | Perimeter firewall logs | NetFlow or basic monitoring | Full packet inspection at key points | Complete east-west visibility |
| Remote access | Traditional VPN | VPN with MFA | ZTNA for some applications | VPN eliminated, full ZTNA |
| Network detection | IDS at perimeter | IDS/IPS at key segments | NDR deployed | NDR with behavioral analytics |

## Network Pillar Checklist

- [ ] Network zone segmentation documented and enforced
- [ ] East-west traffic flows mapped and baselined
- [ ] Microsegmentation policy in monitoring mode for critical segments
- [ ] DNS security deployed with malware domain blocking and query logging
- [ ] All user-to-resource communication encrypted (TLS 1.2 minimum)
- [ ] East-west traffic encrypted between application tiers
- [ ] Traditional VPN usage documented with ZTNA replacement roadmap
- [ ] Network Detection and Response (NDR) deployed for east-west visibility
- [ ] NetFlow or equivalent exported to SIEM for correlation
- [ ] Management network isolated from user and server networks
- [ ] OT/ICS networks air-gapped or strictly segmented
