# Zero Trust Implementation Roadmap

> A 24-month phased implementation plan mapped to CISA Zero Trust Maturity Model stages

**Framework basis:** CISA Zero Trust Maturity Model v2.0 (April 2023), NIST SP 800-207

---

## How to Use This Roadmap

This roadmap is a reference architecture, not a prescription. Every organization starts from a different baseline, has different resource constraints, and has different regulatory requirements. Use this as a framework for sequencing your own program, not as a fixed schedule.

The phases are sequenced to deliver value at each stage rather than requiring completion of the full program before any benefit is realized. Each phase builds on the previous one.

Before starting Phase 1, complete a baseline assessment using the [ZT Maturity Assessment](./zt-maturity-assessment.md) to understand your current state. The gaps identified in the assessment determine where to focus effort in each phase.

---

## Phase 0: Foundation (Months 1 to 2)

**Objective:** Establish the governance, visibility, and baseline inventory required to make informed ZT decisions.

**CISA Target:** Traditional to Initial transition point.

### Deliverables

| Deliverable | Description |
|---|---|
| ZT steering committee | Executive ownership for the ZT program. CISO, CTO, and business stakeholders. Defines risk tolerance and approves investment. |
| Baseline maturity assessment | Complete the ZT Maturity Assessment across all five pillars to understand starting point. |
| Complete asset inventory | Inventory all devices, applications, users, and data stores. This is the prerequisite for every subsequent phase. |
| Network traffic baseline | Map existing east-west and north-south traffic flows. Required for segmentation planning. |
| Application inventory and classification | Classify all applications by sensitivity tier. Required for access policy design. |
| Data classification baseline | Apply classification framework to existing data. Identify regulated data categories and locations. |
| Identity audit | Audit all user accounts, privileged accounts, service accounts, and group memberships. Identify orphaned accounts. |

### Success Criteria

- Asset inventory complete for at least 90% of known devices
- All applications classified into sensitivity tiers
- Network traffic baseline documented
- ZT steering committee meeting cadence established
- Baseline maturity assessment completed and documented

---

## Phase 1: Identity Foundation (Months 1 to 4)

**Objective:** Establish identity as the primary ZT control. Enforce MFA, eliminate legacy authentication, and deploy baseline Conditional Access.

**CISA Target:** Initial stage for Identity pillar.

**Why identity first:** Credential-based attacks are the dominant initial access vector. Identity controls have the highest impact per unit of investment and do not require significant infrastructure changes.

### Deliverables

| Control | Target | Completion Criteria |
|---|---|---|
| MFA enforcement | All users, all cloud applications | Zero users authenticating without MFA to any cloud app |
| Number matching on push MFA | All Microsoft Authenticator users | Number matching enabled across all tenants |
| Legacy authentication block | All protocols | Exchange ActiveSync and Other Clients blocked in Conditional Access |
| Baseline Conditional Access policies | All users | Policies 1-4 from Identity Pillar document active |
| Privileged access inventory | All admin accounts | All admin accounts documented with owner, scope, and justification |
| PAM deployment | Tier 0 and Tier 1 admin access | All Tier 0 access through PAM vault with session recording |
| Service account inventory | All service accounts | Owner, purpose, password age, and scope documented |
| SSO for top 10 SaaS apps | Priority applications | IdP-integrated, no direct credential accounts |

### Key Risks in Phase 1

**Break-glass accounts:** Define and secure break-glass (emergency access) accounts before enforcing MFA everywhere. Without break-glass accounts, a configuration error can lock out all administrators.

**Legacy application dependencies:** Some applications may break when legacy authentication is blocked. Audit legacy authentication usage before blocking. Work with application owners to migrate.

**Service account disruption:** Password rotations on service accounts can break automated processes. Inventory and test before rotating.

---

## Phase 2: Device Trust (Months 3 to 6)

**Objective:** Establish device compliance as the second ZT signal. Enforce managed device requirement for sensitive application access.

**CISA Target:** Initial stage for Devices pillar.

**Why device trust second:** Once identity is established as a control, device posture is the next signal. An authenticated user on a compromised device is a high-risk access event.

### Deliverables

| Control | Target | Completion Criteria |
|---|---|---|
| MDM enrollment | All corporate Windows and macOS devices | Enrollment rate greater than 95% |
| Compliance policy definition | All device types | Policies defined for Windows, macOS, iOS, Android |
| EDR deployment | All managed endpoints | EDR active on greater than 95% of enrolled devices |
| Compliance enforcement in CA | Tier 1 and Tier 2 applications | Non-compliant devices blocked from sensitive apps |
| BYOD policy | All personal devices | MAM policy applied to all corporate apps on personal devices |
| Device risk signal integration | CA policy engine | EDR device risk fed to Conditional Access |
| Machine certificate deployment | All managed devices | Certificate-based device authentication available |

### Sequencing Consideration

Do not enforce device compliance in Conditional Access until MDM enrollment is at least 90%. Enforcing compliance when enrollment is incomplete will block legitimate users. Run enforcement in report-only mode first and review the impact before switching to enforce.

---

## Phase 3: Network Visibility and DNS Security (Months 5 to 8)

**Objective:** Establish network visibility baseline and implement DNS security as the first network ZT control.

**CISA Target:** Initial stage for Networks pillar.

### Deliverables

| Control | Target | Completion Criteria |
|---|---|---|
| DNS security deployment | All users | DNS queries routed through filtering service |
| Malware domain blocking | DNS layer | Threat intelligence-fed domain blocking active |
| DNS query logging | All users | DNS queries logged and exported to SIEM |
| Network traffic visibility | Core segments | NetFlow or packet capture at key network points |
| NDR deployment | East-west core traffic | NDR sensor deployed in server segment |
| Shadow IT discovery | All user web traffic | SaaS discovery report generated from proxy or DNS logs |
| VPN usage audit | All VPN users | Document which applications are accessed via VPN |

---

## Phase 4: Application Segmentation and ZTNA Pilot (Months 7 to 12)

**Objective:** Begin replacing VPN access with ZTNA for target applications. Implement application-level access controls.

**CISA Target:** Advanced stage for Applications pillar; Initial for Networks.

### Deliverables

| Control | Target | Completion Criteria |
|---|---|---|
| ZTNA vendor selection | Decision made | Proof of concept completed and scored |
| ZTNA pilot | Tier 3 applications | At least 3 applications accessible via ZTNA with no VPN |
| Application-level access policies | Tier 1 and Tier 2 apps | RBAC enforced at application layer |
| API inventory | All internal APIs | All APIs documented with authentication method |
| API authentication | All production APIs | No unauthenticated API endpoints |
| SaaS CASB baseline | Top 20 SaaS apps | CASB policies active for data loss prevention |

---

## Phase 5: Data Protection and DLP (Months 9 to 15)

**Objective:** Implement data classification across the estate and deploy DLP on email and web egress channels.

**CISA Target:** Advanced stage for Data pillar.

### Deliverables

| Control | Target | Completion Criteria |
|---|---|---|
| Sensitivity label deployment | All Microsoft 365 users | Labels available and auto-classification active |
| Auto-classification policies | Regulated data types | PII, PCI, PHI patterns classified automatically |
| Email DLP | All outbound email | Regulated data types blocked from external email |
| Web upload DLP | All users via SWG | Labeled Confidential and Restricted files blocked from upload |
| Rights management | Confidential documents | IRM applied to documents classified Confidential and above |
| CASB API coverage | Top SaaS apps | Data visibility and DLP active via CASB API |

---

## Phase 6: Network Segmentation (Months 12 to 18)

**Objective:** Implement application-level and workload-level network segmentation. Reduce east-west exposure.

**CISA Target:** Advanced stage for Networks pillar.

### Deliverables

| Control | Target | Completion Criteria |
|---|---|---|
| Application tier segmentation | Production applications | Web, app, and database tiers segmented with explicit allow rules |
| Microsegmentation pilot | Most critical production segment | Microsegmentation deployed in monitor mode, then enforce |
| Management network isolation | All admin access | Management traffic separated from user and server traffic |
| Encrypted east-west traffic | Application tiers | TLS enforced between application tiers |
| VPN replacement (Phase 2) | Tier 2 applications | Tier 2 applications accessible via ZTNA; VPN usage reducing |

---

## Phase 7: Advanced ZT Controls (Months 16 to 24)

**Objective:** Achieve Advanced to Optimal maturity across all pillars. Automate policy responses and eliminate remaining ZT gaps.

**CISA Target:** Advanced to Optimal across all pillars.

### Deliverables

| Control | Target | Completion Criteria |
|---|---|---|
| Phishing-resistant MFA | All privileged roles | FIDO2 or Windows Hello for Business deployed for all admin access |
| VPN decommission | All applications | Zero users accessing applications via traditional VPN |
| Just-in-time PAM | Tier 0 and Tier 1 | All privileged access granted on-demand for defined time windows |
| Full microsegmentation | All production segments | East-west traffic governed by default-deny policies |
| Automated incident response | SOAR integration | Non-compliant devices automatically quarantined; risky sessions terminated |
| Continuous access evaluation | All cloud applications | CAE enabled for real-time token revocation on risk signals |
| ZT posture dashboard | Leadership reporting | Real-time ZT maturity visible to security leadership |

---

## Common Implementation Failures

**Failure 1: Skipping the baseline assessment**
Organizations start deploying MFA or ZTNA without understanding their current state. Without a baseline, there is no way to measure progress or identify the gaps that matter most.

**Failure 2: Big bang deployments**
Attempting to enforce compliance, MFA, and device requirements simultaneously without a gradual rollout. This guarantees user disruption and rollbacks that damage program credibility.

**Failure 3: Technology without governance**
Deploying ZT technology without updating access policies, review processes, and accountability structures. Technology enforces policies. If the policies are wrong, the technology enforces wrong policies.

**Failure 4: Ignoring legacy systems**
ZT programs often stall when they encounter legacy systems that cannot participate in identity or device-based access controls. These systems need a documented exception process with compensating controls, not an indefinite exemption from the program.

**Failure 5: Treating ZT as a destination**
Zero Trust is a continuous improvement program, not a project with a completion date. Organizations that treat ZT as "done" after implementing MFA and ZTNA miss the ongoing operational discipline required to maintain posture.

---
---

# Zero Trust Maturity Assessment

> Self-assessment template based on CISA Zero Trust Maturity Model v2.0

**Reference:** CISA Zero Trust Maturity Model v2.0, April 2023

---

## How to Use This Assessment

Complete each section with your current maturity stage. Use the definitions in each row to determine which stage most accurately describes your current state. Be honest: overestimating maturity produces an inaccurate gap analysis and a misleading roadmap.

For each control area, select one of: Traditional (T), Initial (I), Advanced (A), or Optimal (O).

---

## Identity Pillar Assessment

| Control Area | Traditional | Initial | Advanced | Optimal | Current Stage |
|---|---|---|---|---|---|
| MFA | Password only | MFA on some apps or privileged users | MFA enforced for all users and all apps | Phishing-resistant MFA enforced everywhere | |
| Conditional Access | No dynamic access policies | Basic CA policies for some scenarios | Risk-based CA, device compliance enforced | Fully dynamic, continuously evaluated policies | |
| Privileged Access | Shared admin accounts, no PAM | PAM deployed, partial use | PAM enforced, session recording, JIT starting | JIT PAM everywhere, zero standing privilege | |
| SSO | Minimal, siloed authentication | SSO for major SaaS apps | SSO for all enterprise apps | Universal SSO including legacy apps | |
| Identity Governance | Manual provisioning, no reviews | Lifecycle management started | Automated provisioning and deprovisioning | Continuous access reviews and certifications | |
| Service Accounts | Unmanaged, over-privileged | Inventory complete, rotation starting | Managed secrets, reduced scope | Workload identities, no long-lived credentials | |

**Identity Pillar Current Stage:** _______________

---

## Devices Pillar Assessment

| Control Area | Traditional | Initial | Advanced | Optimal | Current Stage |
|---|---|---|---|---|---|
| Asset Inventory | Unknown or partial | MDM enrollment starting | All corporate devices in MDM | Real-time posture for all devices including BYOD | |
| Compliance Policies | No enforcement | Policies defined, not enforced in CA | Compliance enforced for sensitive apps | Compliance enforced universally, auto-remediation | |
| EDR Coverage | AV only or partial | EDR on most managed devices | EDR on all managed devices | EDR with behavioral analytics, hunting capability | |
| Device Trust Signal | Not fed to policy | Device risk available but not used | Device risk in CA policy | Automated response based on device risk changes | |
| Certificate Auth | Password only | Machine certs on some devices | Cert-based auth for sensitive resources | mTLS for all device-to-resource communication | |

**Devices Pillar Current Stage:** _______________

---

## Networks Pillar Assessment

| Control Area | Traditional | Initial | Advanced | Optimal | Current Stage |
|---|---|---|---|---|---|
| Segmentation | Flat network | Basic VLAN segmentation | Application-level segmentation | Full microsegmentation per workload | |
| Encryption | Perimeter only | Encryption on external traffic | Sensitive east-west traffic encrypted | All traffic encrypted | |
| DNS Security | None or basic | DNS filtering deployed | DNS with threat intel, query logging | DNS as ZT policy signal, automated response | |
| Traffic Visibility | Perimeter firewall logs | NetFlow or basic monitoring | NDR in critical segments | Full east-west visibility with behavioral analytics | |
| Remote Access | VPN with network access | VPN with MFA | ZTNA for some apps | VPN eliminated, full ZTNA | |

**Networks Pillar Current Stage:** _______________

---

## Applications and Workloads Pillar Assessment

| Control Area | Traditional | Initial | Advanced | Optimal | Current Stage |
|---|---|---|---|---|---|
| Application Inventory | Unknown estate | Discovery started | All apps inventoried and classified | Continuous discovery and risk assessment | |
| Access Control | Broad VPN-based access | App-level authentication on some apps | Role-based, least privilege access | JIT, context-aware, session-level access | |
| ZTNA | No ZTNA | Pilot in progress | ZTNA for majority of apps | All apps via ZTNA, VPN eliminated | |
| Session Monitoring | None | Basic logging | Session recording for sensitive apps | Continuous behavioral analysis in session | |
| API Security | Unmanaged APIs | API gateway deployed | Auth on all APIs | API behavior monitoring, anomaly detection | |

**Applications Pillar Current Stage:** _______________

---

## Data Pillar Assessment

| Control Area | Traditional | Initial | Advanced | Optimal | Current Stage |
|---|---|---|---|---|---|
| Data Discovery | Unknown data estate | Structured data discovery started | Structured and most unstructured classified | Continuous real-time classification | |
| Data Classification | None or manual | Tool-assisted, partial coverage | Auto-classification for regulated types | ML-based classification at creation | |
| DLP | None or basic email | DLP on some channels | Inline DLP on web, email, cloud | Unified DLP across all egress channels | |
| Rights Management | None | Applied to some document types | Applied to all sensitive data | Automatic rights application | |
| Encryption at Rest | Partial | Full disk encryption | Field-level for sensitive data | Envelope encryption, managed keys | |

**Data Pillar Current Stage:** _______________

---

## Cross-Cutting Capabilities Assessment

| Capability | Traditional | Initial | Advanced | Optimal | Current Stage |
|---|---|---|---|---|---|
| Visibility and Analytics | Siloed visibility, no correlation | Some centralized logging | SIEM with cross-pillar correlation | Unified real-time visibility with AI analytics | |
| Automation and Orchestration | Manual response only | Some automated alerts | Automated response for known scenarios | Dynamic policy adjustment, full SOAR | |
| Governance | No formal ZT governance | ZT program defined | Active governance, metrics tracked | Continuous improvement with exec visibility | |

---

## Assessment Summary

| Pillar | Current Stage | Target Stage (12 months) | Priority |
|---|---|---|---|
| Identity | | | |
| Devices | | | |
| Networks | | | |
| Applications and Workloads | | | |
| Data | | | |
| Visibility and Analytics | | | |
| Automation and Orchestration | | | |
| Governance | | | |

**Scoring:**
- Traditional = 1
- Initial = 2
- Advanced = 3
- Optimal = 4

**Overall maturity score:** Sum of all pillar scores / number of pillars = average score

A score of 1.0 to 1.5 indicates Traditional maturity overall. A score of 1.5 to 2.5 indicates Initial. A score of 2.5 to 3.5 indicates Advanced. A score of 3.5 to 4.0 indicates Optimal.

---

## Gap Prioritization Matrix

After completing the assessment, use the gap between current and target stage for each pillar to prioritize your roadmap:

| Priority | Criteria |
|---|---|
| P1: Immediate | Gap of 2 or more stages in Identity or Devices pillar |
| P2: High | Gap of 1 stage in Identity or Devices; gap of 2 stages in any other pillar |
| P3: Medium | Gap of 1 stage in Networks, Applications, or Data pillar |
| P4: Planned | Already at Advanced; optimizing toward Optimal |
