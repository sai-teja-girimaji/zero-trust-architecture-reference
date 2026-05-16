# Data Pillar

> Zero Trust implementation guide for the Data pillar: classification, protection, and access governance

**CISA Pillar:** Data
**Core Question:** Is data classified, is access restricted to authorized subjects, and is data protected regardless of where it resides?

See full data pillar documentation in [device-network-application-data-pillars.md](./device-network-application-data-pillars.md).

## Quick Reference: Data Maturity Stages

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Data discovery | Unknown data estate | Discovery started for structured data | All structured and most unstructured data classified | Continuous data discovery and classification |
| Data classification | Manual, inconsistent | Tool-assisted classification | Automated classification at creation | Real-time classification with ML |
| DLP | None or basic email DLP | DLP on some channels | Inline DLP on web, email, and cloud | Unified DLP across all egress channels |
| Rights management | None | Applied to some document types | Applied to all sensitive data | Automatic rights application based on classification |
| Encryption at rest | Partial | Full disk encryption | Field-level encryption for sensitive data | Envelope encryption with managed keys |

## Data Classification Framework

| Classification | Examples | Minimum Controls |
|---|---|---|
| Public | Marketing materials, published documentation | None beyond integrity |
| Internal | Internal policies, meeting notes, general business data | Access controls, encryption in transit |
| Confidential | Financial forecasts, contracts, personnel files | Access controls, encryption at rest and in transit, DLP |
| Restricted | PII, PHI, PCI data, trade secrets, credentials | Strict access controls, encryption, DLP, rights management, audit logging |

## Data Pillar Checklist

- [ ] Data inventory initiated for structured data (databases, file shares)
- [ ] Data classification framework documented and approved
- [ ] Classification tool deployed with sensitivity labels
- [ ] Automatic classification policies covering PII, PCI, and PHI patterns
- [ ] DLP policies active on email for regulated data types
- [ ] CASB inline or API mode active for cloud storage DLP
- [ ] Endpoint DLP deployed for USB and clipboard controls on sensitive endpoints
- [ ] Rights management applied to Confidential and Restricted documents
- [ ] Encryption at rest enabled for all classified data stores
- [ ] Customer-managed keys deployed for regulated cloud workloads
- [ ] Key management policy documented with rotation schedule
- [ ] Data access reviews running on defined cadence
