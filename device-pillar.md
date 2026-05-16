# Device Pillar

> Zero Trust implementation guide for the Device pillar: device trust, endpoint compliance, and certificate-based authentication

**CISA Pillar:** Devices
**Core Question:** Is this device known, managed, compliant, and healthy enough to be trusted with the resource being accessed?

See full device pillar documentation in [device-network-application-data-pillars.md](./device-network-application-data-pillars.md).

## Quick Reference: Device Maturity Stages

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Inventory | Partial, unmanaged | MDM inventory in progress | All managed devices inventoried | Real-time asset inventory with posture data |
| MDM enrollment | None or minimal | Partial enrollment | All corporate devices enrolled | All devices including BYOD managed via MAM |
| Compliance policies | None | Basic policies defined | Policies enforced at access time | Automated remediation on non-compliance |
| EDR coverage | Partial AV | EDR on most managed devices | EDR on all managed devices | EDR with behavioral analytics |
| Certificate auth | Passwords only | Machine certificates on some | Cert-based auth for sensitive resources | Mutual TLS everywhere |
| Device risk signals | None | Manual review | Real-time posture fed to policy engine | Automated policy adjustment on device risk |

## Device Pillar Checklist

- [ ] Complete asset inventory with ownership, OS, compliance, and EDR state
- [ ] MDM enrollment enforced for all corporate devices
- [ ] Compliance policies defined and enforced for Windows, macOS, and mobile
- [ ] Non-compliant devices blocked from accessing enterprise applications
- [ ] EDR deployed on all managed endpoints with tamper protection
- [ ] Device risk signal integrated with Conditional Access policy engine
- [ ] BYOD strategy defined with MAM policies for personal devices
- [ ] Machine certificate deployed to managed devices for certificate-based authentication
- [ ] Device lifecycle management process defined (procurement to decommission)
- [ ] Automated remediation for common compliance violations
