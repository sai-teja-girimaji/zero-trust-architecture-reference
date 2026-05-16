# Zero Trust Architecture Reference

> A practitioner-built reference for designing, implementing, and validating enterprise Zero Trust Architecture

<p>
  <img src="https://img.shields.io/badge/NIST_SP_800--207-Aligned-0078D4?style=flat-square"/>
  <img src="https://img.shields.io/badge/CISA_ZT_Maturity-v2.0-E21B24?style=flat-square"/>
  <img src="https://img.shields.io/badge/Pillars_Documented-5-10B981?style=flat-square"/>
  <img src="https://img.shields.io/badge/License-MIT-94A3B8?style=flat-square"/>
</p>

---

## Purpose

Zero Trust is not a product. It is not a single vendor's platform. It is an architectural strategy grounded in one principle: never trust, always verify.

Most Zero Trust resources fall into two categories: vendor marketing that reduces ZT to a feature checklist, or academic frameworks that stop short of telling you what to actually build. This reference fills the gap between those two.

Every document in this repository is grounded in NIST SP 800-207, CISA Zero Trust Maturity Model v2.0, and real-world enterprise deployments across Zscaler, Netskope, Cloudflare, Palo Alto Networks, Microsoft Entra ID, and CrowdStrike.

---

## What This Reference Covers

| Document | Description |
|---|---|
| [ZT Principles and Framework](./zt-principles-framework.md) | NIST SP 800-207 tenets, CISA maturity model, logical ZT components |
| [Identity Pillar](./identity-pillar.md) | Identity as the new perimeter: MFA, PAM, Conditional Access, SSO |
| [Device Pillar](./device-pillar.md) | Device trust, MDM, compliance policies, certificate-based authentication |
| [Network Pillar](./network-pillar.md) | Microsegmentation, network visibility, east-west traffic controls |
| [Application and Workload Pillar](./application-pillar.md) | ZTNA, legacy VPN elimination, application segmentation |
| [Data Pillar](./data-pillar.md) | Data classification, DLP, rights management, secure access to data at rest and in transit |
| [SASE Vendor Comparison](./sase-vendor-comparison.md) | Zscaler vs Netskope vs Cloudflare vs Palo Alto Prisma: architecture, capability, fit |
| [ZT Implementation Roadmap](./zt-implementation-roadmap.md) | 24-month phased implementation plan mapped to CISA maturity stages |
| [ZT Maturity Assessment](./zt-maturity-assessment.md) | Self-assessment template based on CISA ZT Maturity Model v2.0 |
| [Architecture Decision Records](./architecture-decision-records.md) | Key ZT design decisions with rationale, tradeoffs, and consequences |

---

## The Five Pillars

This reference is organized around the five pillars defined in the CISA Zero Trust Maturity Model v2.0:

```
┌─────────────┬─────────────┬─────────────┬──────────────────────┬──────────┐
│  IDENTITY   │   DEVICES   │  NETWORKS   │  APPLICATIONS AND    │   DATA   │
│             │             │             │     WORKLOADS        │          │
│  Who is     │  What is    │  Where is   │  What can they       │  What    │
│  accessing? │  accessing? │  traffic    │  access and how?     │  are     │
│             │             │  coming     │                      │  they    │
│             │             │  from?      │                      │  accessing│
└─────────────┴─────────────┴─────────────┴──────────────────────┴──────────┘
                      Cross-Cutting Capabilities
┌─────────────────────────────────────────────────────────────────────────────┐
│  Visibility and Analytics  │  Automation and Orchestration  │  Governance  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Four Maturity Stages (CISA ZT Maturity Model v2.0)

| Stage | Description |
|---|---|
| Traditional | Perimeter-based security. Implicit trust inside the network. Little visibility. |
| Initial | Attribute-based access. Manual configurations. Basic visibility. |
| Advanced | Cross-pillar integration. Centralized policy. Automated response starting. |
| Optimal | Dynamic policy. Fully automated responses. Continuous validation across all pillars. |

Most enterprise organizations fall between Traditional and Initial. Optimal is the target architecture, not the starting point.

---

## Who This Is For

**Security Architects** designing enterprise ZT programs from the ground up or evolving an existing perimeter-based architecture.

**SOC and Detection Engineering Teams** understanding how ZT telemetry changes detection requirements and what new signals become available.

**CISOs and Security Leaders** communicating ZT posture, investment requirements, and maturity progression to executive and board audiences.

**Network Security Engineers** implementing SASE, ZTNA, and microsegmentation in production environments.

---

## Key References

| Reference | Source |
|---|---|
| NIST SP 800-207: Zero Trust Architecture | NIST, August 2020 |
| CISA Zero Trust Maturity Model v2.0 | CISA, April 2023 |
| DoD Zero Trust Strategy | DoD, November 2022 |
| NIST SP 800-63B: Digital Identity Guidelines | NIST |

---

## Author

Sai Teja Girimaji
Network Security Capability Lead, NTT DATA Cloud Services Division

[LinkedIn](https://www.linkedin.com/in/girimaji-saiteja-569b356a) | [Portfolio](https://saiteja-security.netlify.app) | [Sentinel KQL Detection Rules](https://github.com/sai-teja-girimaji/sentinel-kql-detection-rules) | [AI Security Governance Framework](https://github.com/sai-teja-girimaji/ai-security-governance-framework)

---

This reference is maintained and updated as NIST, CISA, and vendor ZT capabilities evolve.
