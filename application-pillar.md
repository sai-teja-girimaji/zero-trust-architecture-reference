# Application and Workload Pillar

> Zero Trust implementation guide for the Application and Workload pillar: ZTNA, application segmentation, and least-privilege access

**CISA Pillar:** Applications and Workloads
**Core Question:** Is application access granted on a per-session, least-privilege basis with continuous validation?

See full application pillar documentation in [device-network-application-data-pillars.md](./device-network-application-data-pillars.md).

## Quick Reference: Application Maturity Stages

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Remote access | VPN with network access | VPN with MFA | ZTNA for some apps, VPN residual | VPN eliminated, full ZTNA |
| Application visibility | Unknown application estate | SaaS discovery started | All apps inventoried and classified | Continuous app discovery and risk assessment |
| Access control | Broad network access | App-level authentication | Role-based, least privilege | Just-in-time, context-aware access |
| Session monitoring | None | Basic logging | Session recording for sensitive apps | Continuous behavioral monitoring in session |
| API security | Unmanaged APIs | API gateway deployed | Auth enforced on all APIs | API behavior monitoring and anomaly detection |

## Application Pillar Checklist

- [ ] Complete application inventory with sensitivity classification
- [ ] Shadow IT discovery process in place
- [ ] ZTNA deployed for at least one application category (pilot complete)
- [ ] VPN-to-ZTNA migration roadmap documented with timeline
- [ ] Application access policies enforcing MFA and device compliance
- [ ] API inventory complete with authentication enforced on all endpoints
- [ ] Privileged application access reviewed and reduced to minimum required
- [ ] Session monitoring enabled for Tier 1 and Tier 2 applications
- [ ] SaaS application access governed through IdP (no direct credential accounts)
- [ ] Application access reviews on documented cadence
