# Zero Trust Principles and Framework

> Foundational principles, logical components, and architectural models for enterprise Zero Trust Architecture

**Primary Reference:** NIST SP 800-207: Zero Trust Architecture (August 2020)
**Supporting Reference:** CISA Zero Trust Maturity Model v2.0 (April 2023)

---

## What Zero Trust Is

Zero Trust is a set of cybersecurity principles and an architectural strategy built on the assumption that no user, device, or network segment should be trusted by default, regardless of whether they are inside or outside the traditional network perimeter.

The term was coined by John Kindervag at Forrester Research in 2010. The authoritative technical standard was published by NIST in August 2020 as Special Publication 800-207.

The core shift Zero Trust demands:

**From:** Trust based on network location. If you are inside the perimeter, you are trusted.

**To:** Trust based on continuous verification of identity, device posture, and context. Every access request is evaluated every time.

---

## The Seven Tenets of Zero Trust (NIST SP 800-207)

These seven principles are the foundation of all ZT architecture decisions. Every control, policy, and technology choice in a ZT program should map to one or more of these tenets.

### Tenet 1: All data sources and computing services are considered resources

Every device, service, and data store is a resource that requires protection. This includes employee-owned devices used to access enterprise resources, cloud-hosted services, SaaS applications, and on-premises infrastructure. The boundary of the enterprise is not the network edge. It is wherever enterprise resources exist.

### Tenet 2: All communication is secured regardless of network location

Network location does not grant trust. Communication between an enterprise device and an enterprise resource must be authenticated and encrypted whether the device is on a corporate LAN, on a home network, at a coffee shop, or in a cloud environment. The network path is never assumed to be safe.

### Tenet 3: Access to individual enterprise resources is granted on a per-session basis

Access is granted to specific resources for specific sessions. A user authenticating to one application does not implicitly receive access to any other application. Each session is independently evaluated and authorized. Prior trust does not carry forward.

### Tenet 4: Access to resources is determined by dynamic policy

Access decisions are made based on observable state, not static rules. The policy engine evaluates the requesting user's identity, the device's compliance state, the sensitivity of the resource being accessed, behavioral signals, and environmental context at the time of each request. Policy is dynamic, not a one-time configuration.

### Tenet 5: The enterprise monitors and measures the integrity and security posture of all owned and associated assets

The enterprise cannot make a meaningful access decision without knowing the state of the requesting asset. Device posture (patch level, EDR status, encryption state, compliance policy adherence) is continuously monitored. An asset that was compliant at 8 AM may not be compliant at 2 PM after a security tool was disabled.

### Tenet 6: All resource authentication and authorization is dynamic and strictly enforced before access is allowed

Authentication is not a one-time gate at the beginning of a session. Zero Trust requires continuous authentication and re-evaluation. A session that was authenticated one hour ago is re-evaluated. If the device posture changes or an anomaly is detected, the session is re-evaluated and may be terminated.

### Tenet 7: The enterprise collects as much information as possible about the current state of assets, network infrastructure, and communications and uses it to improve its security posture

ZT generates significantly more telemetry than perimeter-based architecture. Every access request, authentication event, and resource interaction is logged. This telemetry feeds the policy engine, supports threat detection, and drives continuous improvement of access policies.

---

## The Logical Components of ZTA (NIST SP 800-207)

NIST defines the following logical components that together form the Zero Trust control plane:

### Policy Engine (PE)

The Policy Engine makes the final access decision: grant, deny, or revoke. It evaluates the requesting entity (user, device, service) against enterprise policy, threat intelligence, CDM system data, and compliance requirements. The PE is the brain of the ZTA. In practice, this may be implemented as a cloud-based access control service (for example, Azure AD Conditional Access, Okta, or a SASE platform's policy engine).

### Policy Administrator (PA)

The Policy Administrator executes the decision made by the Policy Engine. It establishes or terminates the communication path between a subject and a resource. It generates authentication tokens or session credentials and communicates with the Policy Enforcement Point. The PA is the execution arm of the PE decision.

### Policy Enforcement Point (PEP)

The Policy Enforcement Point is the component that enables, monitors, and terminates connections between subjects and enterprise resources. It receives policy from the PA and enforces it at the point of access. In practice, PEPs include ZTNA connectors, SWG proxies, API gateways, and network enforcement agents deployed in front of resources.

### Supporting Systems

| System | Role in ZTA |
|---|---|
| CDM System | Continuous Diagnostics and Mitigation: provides real-time device posture data to the PE |
| Identity Management System | Source of truth for user identity, group membership, and entitlements |
| PKI | Provides certificates for device authentication and encrypted communication |
| Threat Intelligence | Feeds the PE with known-bad indicators, reputation data, and risk signals |
| SIEM/SOAR | Aggregates ZT telemetry, supports detection, and enables automated response |
| Data Access Policies | Defines which data resources are accessible under which conditions |

---

## The Three Deployment Variations (NIST SP 800-207)

NIST defines three primary ZT deployment approaches. Most enterprise implementations use elements of all three.

### Variation 1: Enhanced Identity Governance

The identity system is the primary policy engine. Access decisions are made based on user identity, group membership, and per-user/per-application access policies. Device posture, network location, and behavioral signals augment the identity-centric decision.

**Best fit:** Organizations with a mature identity platform (Azure AD/Entra ID, Okta) and predominantly SaaS or cloud-hosted workloads.

**Limitations:** Relies heavily on identity being the correct signal. Does not address east-west traffic between workloads. Weaker for environments with significant on-premises or legacy application estates.

### Variation 2: Micro-Segmented Network

The network is divided into small segments, each requiring separate authentication and authorization. Resources within the same network segment do not inherit trust from each other. Each segment has its own PEP.

**Best fit:** Organizations with significant on-premises infrastructure, sensitive production environments (OT/ICS proximity), or regulatory requirements for network isolation.

**Limitations:** Complex to implement and maintain. Requires significant network re-architecture. Higher operational overhead than identity-centric approaches.

### Variation 3: Network Infrastructure and Software-Defined Perimeters (SASE/SDP)

A software-defined overlay creates a ZT network that abstracts from the physical network. Users and devices connect to a cloud-delivered access layer that enforces policy before connecting them to resources. This is the model underpinning modern SASE (Secure Access Service Edge) platforms.

**Best fit:** Cloud-first or hybrid organizations with a distributed workforce, reliance on SaaS and cloud workloads, and a desire to eliminate traditional VPN.

**Limitations:** Dependent on cloud platform availability. Data residency constraints may apply in some regions. Requires careful integration with on-premises systems.

---

## The CISA Zero Trust Maturity Model v2.0

CISA published the Zero Trust Maturity Model v2.0 in April 2023 as a practical framework for federal agencies and enterprises to assess and progress their ZT posture.

### Five Pillars

| Pillar | Core Question |
|---|---|
| Identity | Is the right person authenticated with the right level of assurance? |
| Devices | Is the requesting device known, compliant, and trusted? |
| Networks | Is network access controlled, segmented, and encrypted? |
| Applications and Workloads | Are applications accessed with least privilege and continuous validation? |
| Data | Is data classified, protected, and accessed only by authorized subjects? |

### Three Cross-Cutting Capabilities

These capabilities span all five pillars and mature in parallel with them:

**Visibility and Analytics:** The ability to observe, correlate, and understand activity across all five pillars. Drives the policy engine and supports threat detection. Immature organizations have siloed visibility. Optimal organizations have unified, real-time visibility.

**Automation and Orchestration:** The ability to respond to signals automatically. Immature organizations rely on manual investigation and response. Optimal organizations automatically adjust policy, quarantine devices, and revoke access in response to detected anomalies.

**Governance:** The policies, processes, and accountability structures that govern the ZT program. Includes risk management, compliance alignment, and executive ownership.

### Four Maturity Stages

| Stage | Identity | Devices | Networks | Apps/Workloads | Data |
|---|---|---|---|---|---|
| Traditional | Password auth. No MFA. | Unmanaged devices permitted. | Flat network. Implicit trust. | VPN-based access. | Undiscovered, unclassified. |
| Initial | MFA on some systems. | MDM deployed, partial enforcement. | Some segmentation. DNS filtering. | Some app-based access controls. | Basic classification applied. |
| Advanced | Phishing-resistant MFA. Risk-based CA. | All devices managed and compliant. | Microsegmentation in progress. | ZTNA replacing VPN. | DLP in-line. |
| Optimal | Continuous risk-based auth. Just-in-time access. | Automated remediation on non-compliance. | Full microsegmentation. | All apps zero-trust accessed. | Full data lifecycle protection. |

---

## Why Perimeter Security Fails

The traditional castle-and-moat model fails for four reasons that are structural, not just technical:

**1. The perimeter no longer exists as a bounded surface.** Users work from homes, cafes, airports, and branch offices. Data lives in SaaS applications, IaaS platforms, and on-premises systems simultaneously. A single perimeter cannot contain this.

**2. Lateral movement is the attacker's default path.** Once inside the perimeter, an attacker has largely unrestricted access. The average dwell time between initial compromise and detected lateral movement is measured in days to weeks, not minutes.

**3. Insider threats are invisible to perimeter controls.** A malicious or compromised insider already has perimeter access. Perimeter controls provide no protection against this threat vector.

**4. Implicit trust is operationally untenable at scale.** As environments grow more complex, maintaining perimeter rules that accurately reflect who should access what becomes impossible. The result is overpermissive access that persists long after it is needed.

Zero Trust does not eliminate these risks. It reduces the blast radius of a breach, limits lateral movement, and makes unauthorized access visible.
