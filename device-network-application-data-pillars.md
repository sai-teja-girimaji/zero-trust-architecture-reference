# Device Pillar

> Zero Trust implementation guide for the Device pillar: device trust, endpoint compliance, and certificate-based authentication

**CISA Pillar:** Devices
**Core Question:** Is this device known, managed, compliant, and healthy enough to be trusted with the resource being accessed?

---

## Why Devices Matter in ZTA

Identity is necessary but not sufficient. A valid set of credentials used from a compromised, unmanaged, or non-compliant device represents a significant risk. Zero Trust requires that both the identity and the device meet defined standards before access is granted.

The device is the physical and logical context for the user's session. If the device is compromised by malware, missing critical patches, running without endpoint detection, or not owned and managed by the enterprise, then the authentication event is unreliable regardless of MFA.

---

## Device Maturity Stages (CISA Model)

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Inventory | Partial, unmanaged | MDM inventory in progress | All managed devices inventoried | Real-time asset inventory with posture data |
| MDM enrollment | None or minimal | Partial enrollment | All corporate devices enrolled | All devices including personal (BYOD) managed via MAM |
| Compliance policies | None | Basic policies defined | Policies enforced at access time | Automated remediation on non-compliance |
| EDR coverage | Partial AV | EDR on most managed devices | EDR on all managed devices | EDR with behavioral analytics, threat hunting |
| Certificate auth | Passwords only | Machine certificates on some devices | Certificate-based device auth for sensitive resources | Mutual TLS everywhere |
| Device risk signals | None | Manual review | Real-time posture fed to policy engine | Automated policy adjustment based on device risk |

---

## Control 1: Asset Inventory

You cannot govern what you do not know exists. A complete, accurate, and continuously updated asset inventory is the prerequisite for every device pillar control.

### Minimum Required Asset Attributes

| Attribute | Description |
|---|---|
| Device ID | Unique identifier (serial number, Entra ID device ID, SCCM GUID) |
| Device type | Workstation, server, mobile, network appliance, OT device |
| Operating system and version | Including patch level |
| Ownership | Corporate-owned, BYOD, contractor-owned |
| Assigned user | Primary user or shared account |
| Management state | MDM-enrolled, domain-joined, hybrid-joined, unmanaged |
| Compliance state | Compliant, non-compliant, unknown |
| EDR state | Installed, active, version, last check-in |
| Last seen | Timestamp of last observed activity |
| Location | Physical location or network segment |

### Asset Discovery Methods

| Method | Coverage | Accuracy |
|---|---|---|
| MDM/Intune inventory | All enrolled devices | High (real-time) |
| Active Directory | Domain-joined Windows | High |
| Network scanning (NMAP, Nessus) | All reachable network hosts | Medium (point-in-time) |
| DHCP logs | All IP-assigned devices | Medium (historical) |
| EDR/XDR telemetry | All devices with agent | High |
| NAC (802.1X) | Devices connecting to managed network ports | High for wired/WiFi |

---

## Control 2: Mobile Device Management

MDM provides the management plane for enforcing device compliance policies, deploying certificates and configurations, and remediating non-compliant devices.

### Compliance Policy Minimum Standards

Define a compliance policy that all corporate devices must meet before access to enterprise resources is permitted. The minimum baseline:

**Windows:**
- BitLocker encryption enabled
- Windows Defender or approved EDR active and updated
- Operating system version within supported range (no EOL OS)
- Password/PIN required with defined complexity
- Device not jailbroken or rooted
- Specific firewall rules enforced

**macOS:**
- FileVault encryption enabled
- Approved EDR active
- OS version within supported range
- Firewall enabled
- Password required

**Mobile (iOS/Android):**
- Device PIN/biometric required
- Storage encryption enabled (default on modern devices)
- OS version within supported range (typically N-1 or N-2)
- Device not jailbroken or rooted
- MAM policies applied to managed apps

### BYOD (Bring Your Own Device) Strategy

For personal devices, Mobile Application Management (MAM) without MDM enrollment is the appropriate model:
- Enterprise apps (Outlook, Teams, SharePoint) are managed and protected
- Personal apps and data are untouched
- Enterprise data cannot be copied to personal apps
- Remote wipe applies to enterprise apps only, not the entire device

BYOD devices should only access lower-sensitivity resources. Access to sensitive data (financial records, PII, regulated data) from BYOD should require explicit policy justification and additional controls.

---

## Control 3: Endpoint Detection and Response

EDR provides the behavioral telemetry that enables the device risk signal fed to the Conditional Access policy engine. Without EDR, the device pillar has no visibility into what is happening on the endpoint.

### EDR Integration with ZTA Policy Engine

The EDR platform exposes a device risk or health signal. In Microsoft environments, Microsoft Defender for Endpoint feeds a device compliance signal to Intune, which is evaluated by Conditional Access. In CrowdStrike environments, Zero Trust Assessment (ZTA) scores feed into Conditional Access through a compatible integration.

The policy integration allows Conditional Access to say: "If device risk is High, block access regardless of authentication success." This closes the gap where a compromised device with valid credentials can authenticate.

### Minimum EDR Configuration for ZTA

- Real-time protection enabled and active
- Cloud-delivered protection enabled
- Tamper protection enabled (prevents attacker from disabling EDR)
- Attack surface reduction rules configured and enforced
- Behavioral anomaly detection active
- Threat and vulnerability management enabled
- Device health signals exported to identity/access management platform

---

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

---
---

# Network Pillar

> Zero Trust implementation guide for the Network pillar: segmentation, visibility, and east-west traffic control

**CISA Pillar:** Networks
**Core Question:** Is network access segmented, monitored, encrypted, and restricted to what is necessary?

---

## Why the Network Still Matters in ZTA

Zero Trust does not mean the network is irrelevant. It means the network is no longer the trust boundary. Network controls remain important for:

1. Limiting lateral movement when identity or device controls fail
2. Providing visibility into traffic patterns that inform threat detection
3. Encrypting communications that traverse untrusted paths
4. Controlling access to resources that cannot participate in identity-aware ZT controls (OT devices, legacy systems, IoT)

In a mature ZTA, the network is a transport medium with visibility and segmentation, not a trust boundary.

---

## Network Maturity Stages (CISA Model)

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Segmentation | Flat network, single VLAN | Basic VLAN segmentation | Application-level segmentation | Microsegmentation per workload |
| Encryption | Perimeter encryption only | Internal encryption starting | All sensitive traffic encrypted | All network traffic encrypted |
| DNS filtering | None or basic | DNS security service deployed | DNS filtering with threat intelligence | DNS as a signal source for ZT policy |
| Traffic visibility | Perimeter firewall logs | NetFlow or basic monitoring | Full packet inspection at key points | Complete east-west visibility |
| Remote access | Traditional VPN | VPN with MFA | ZTNA for some applications | VPN eliminated, full ZTNA |
| Network detection | IDS at perimeter | IDS/IPS at key segments | NDR deployed | NDR with behavioral analytics |

---

## Control 1: Network Segmentation

Segmentation limits the blast radius of a breach. An attacker who compromises one segment must break through additional controls to reach another. Without segmentation, lateral movement is unrestricted.

### Segmentation Levels

**Macro-segmentation (VLAN/Zone-based):**
Separating broad network zones: user network, server network, management network, DMZ, OT network, guest network. This is the minimum. Most enterprises already have this in some form but often with insufficient control between zones.

**Application-level segmentation:**
Restricting access between application tiers. The web tier communicates with the application tier on specific ports only. The application tier communicates with the database tier on the database port only. Traffic that does not match defined application flows is blocked.

**Microsegmentation:**
Restricting traffic at the individual workload level. Each server, virtual machine, or container communicates only with the specific hosts and ports it needs. East-west traffic that does not match a defined policy is blocked by default. This is the ZT target state for network controls.

### Microsegmentation Implementation Approach

**Step 1: Map existing traffic flows**
Before deploying segmentation controls, understand what communicates with what. Use NetFlow, firewall logs, or a dedicated network visibility tool to build an accurate picture of east-west traffic. Do not skip this step. Deploying microsegmentation without a traffic map will break production systems.

**Step 2: Define the segmentation model**
Group workloads by function, sensitivity, and communication patterns. Common segments: web tier, application tier, database tier, management, security tools, backup, OT/ICS, guest/untrusted.

**Step 3: Deploy in monitoring mode first**
Apply policies in log-only mode for a minimum of two weeks. Review what would have been blocked. Identify legitimate traffic flows that would be disrupted.

**Step 4: Enforce in stages**
Start with the most critical segments (database tier, management network). Enforce there first, monitor for impact, then expand.

**Step 5: Automate policy updates**
As workloads are added, retired, or modified, the segmentation policy must be updated. Manual policy management does not scale. Use infrastructure-as-code or a software-defined networking platform to manage policies programmatically.

---

## Control 2: DNS Security

DNS is a critical but frequently overlooked ZT control point. DNS filtering blocks access to malicious domains, detects DNS tunneling, and provides a telemetry source for detecting C2 communication and data exfiltration attempts.

### DNS Security Capabilities

| Capability | Description |
|---|---|
| Malware domain blocking | Block resolution of known malicious domains |
| Category-based filtering | Block entire categories (gambling, adult content) based on policy |
| DNS tunneling detection | Identify anomalous DNS query patterns indicating C2 or exfiltration |
| Response logging | Log all DNS queries for threat hunting and compliance |
| Split DNS | Route internal queries to internal DNS, external queries to security proxy |
| DNSSEC validation | Validate DNS responses have not been tampered with |

### DNS as a ZT Signal

DNS query logs reveal what users and devices are attempting to communicate with. This makes DNS a high-fidelity signal source:
- A device querying a domain associated with C2 infrastructure indicates compromise
- A device generating high-volume DNS queries with long subdomain names indicates tunneling
- A device querying domains associated with unauthorized cloud storage indicates potential exfiltration

---

## Control 3: Encrypted Traffic

All communication between users and enterprise resources must be encrypted. All communication between enterprise workloads must be encrypted. Network location does not make traffic safe.

### Minimum Encryption Standards

| Traffic Type | Minimum Standard |
|---|---|
| User to SaaS application | TLS 1.2 minimum, TLS 1.3 preferred |
| User to internal application via ZTNA | TLS 1.2 minimum with mutual TLS for sensitive apps |
| Server to server (east-west) | TLS or mTLS for all application traffic |
| Database connections | TLS with certificate validation |
| API communication | TLS 1.2 minimum |
| Management traffic | SSHv2 or TLS. No Telnet, no unencrypted HTTP. |

**TLS Inspection:** Enterprise ZT architectures require decrypting and inspecting HTTPS traffic to enforce DLP, detect malware, and apply application-level controls. TLS inspection must be performed at the SASE/SWG layer with appropriate certificate management and privacy controls for regulated traffic (healthcare, legal).

---

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
- [ ] OT/ICS networks air-gapped or strictly segmented with unidirectional gateways

---
---

# Application and Workload Pillar

> Zero Trust implementation guide for the Application and Workload pillar: ZTNA, application segmentation, and least-privilege access

**CISA Pillar:** Applications and Workloads
**Core Question:** Is application access granted on a per-session, least-privilege basis with continuous validation?

---

## The Problem with VPN in a ZT Context

Traditional VPN grants network-level access, not application-level access. A user connecting to VPN receives access to an entire network segment, not just the application they need. This violates the ZT principle of per-session, per-resource access.

VPN also provides no visibility into what the user does once connected. Traffic on the VPN tunnel is typically not inspected. There is no application-level policy. There is no continuous authentication.

Zero Trust Network Access (ZTNA) replaces VPN access with application-specific tunnels that:
- Are established only after identity and device verification
- Grant access to a single application, not a network
- Are continuously monitored and can be terminated on policy violation
- Do not expose the underlying network to the user

---

## Application Access Maturity Stages (CISA Model)

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Remote access | VPN with network access | VPN with MFA | ZTNA for some apps, VPN residual | VPN eliminated, full ZTNA |
| Application visibility | Unknown application estate | SaaS discovery started | All apps inventoried and classified | Continuous app discovery and risk assessment |
| Access control | Broad network access | App-level authentication | Role-based, least privilege | Just-in-time, context-aware access |
| Session monitoring | None | Basic logging | Session recording for sensitive apps | Continuous behavioral monitoring in session |
| API security | Unmanaged APIs | API gateway deployed | Auth enforced on all APIs | API behavior monitoring and anomaly detection |

---

## Control 1: Application Discovery and Classification

Before implementing access controls, you need a complete picture of your application estate. Shadow IT (applications deployed without IT approval) is the primary risk here.

### Application Classification Framework

| Tier | Sensitivity | Examples | Access Control Requirement |
|---|---|---|---|
| Tier 1: Critical | Highest | ERP, financial systems, HR, identity management | Phishing-resistant MFA, compliant device, privileged access review |
| Tier 2: Sensitive | High | CRM, project management, collaboration, code repositories | Strong MFA, managed device |
| Tier 3: Standard | Medium | Email, productivity apps, standard SaaS | MFA, enrolled device |
| Tier 4: Low sensitivity | Low | Public knowledge bases, informational sites | Standard authentication |

### SaaS Discovery

Most enterprises underestimate their SaaS footprint. Discovery methods:
- DNS query analysis: Which domains are users resolving?
- Proxy/SWG logs: Which applications are generating web traffic?
- CASB API integration: What OAuth grants exist in your IdP?
- Browser extension telemetry: What applications are users accessing?
- Expense report analysis: What SaaS subscriptions are being expensed?

---

## Control 2: Zero Trust Network Access

ZTNA provides application-level remote access without granting network access. The two primary ZTNA models:

### Inside-Out ZTNA (Preferred)

The resource does not need an inbound firewall rule. A connector deployed in the hosting environment (on-premises or cloud) establishes an outbound connection to the ZTNA cloud platform. Users connect to the cloud platform, which brokers access to the resource through the connector.

No inbound ports are opened. The resource is invisible to the internet. The attacker has no surface to scan or exploit.

**This model is implemented by:** Zscaler Private Access (ZPA), Cloudflare Access, Netskope Private Access (NPA), Palo Alto Prisma Access

### Outside-In ZTNA

A reverse proxy is deployed in front of the resource. Users connect to the proxy, which validates identity and device posture before forwarding the connection to the resource. The resource has an inbound rule but it only accepts traffic from the proxy.

Less architecturally clean than inside-out but easier to implement for some legacy application scenarios.

### ZTNA vs VPN Comparison

| Dimension | Traditional VPN | ZTNA |
|---|---|---|
| Access granted | Network segment | Individual application |
| Resource exposure | Network is visible | Resource is not internet-visible |
| Authentication | Once at connection | Per-session, continuous |
| Device posture | Not enforced post-connection | Continuously enforced |
| Traffic inspection | Limited | Inline inspection possible |
| Lateral movement risk | High (network access) | Low (application-only access) |
| User experience | Tunnel overhead, performance variable | Direct-to-app, typically faster |
| Scalability | Limited by concentrator capacity | Cloud-scale |

### VPN to ZTNA Migration Approach

**Phase 1: Discover and classify applications currently accessed via VPN**
List every application, port, and protocol used by VPN users. Classify by sensitivity and user population.

**Phase 2: Pilot ZTNA for Tier 3 applications**
Start with the lowest-sensitivity, lowest-complexity applications. Validate the user experience, the policy engine integration, and the operational workflow before migrating critical apps.

**Phase 3: Migrate Tier 2 and Tier 1 applications**
After validating with lower-risk applications, migrate progressively. Maintain VPN as a fallback during the transition period with monitoring to track usage.

**Phase 4: Decommission VPN**
Once all applications have ZTNA access paths validated, disable VPN. Monitor for any access failures that indicate missed applications. Decommission VPN infrastructure.

---

## Control 3: API Security

Modern applications expose APIs. APIs are a significant and growing attack surface that traditional perimeter controls do not adequately protect.

### API ZT Controls

| Control | Description |
|---|---|
| API inventory | All APIs documented: endpoint, authentication method, data exposed, consumer |
| Authentication on all APIs | No unauthenticated API endpoints in production |
| OAuth 2.0 and OIDC | Standardized authorization framework for API access |
| API gateway | Centralized enforcement of authentication, rate limiting, and input validation |
| Mutual TLS (mTLS) | Machine-to-machine API authentication with certificates |
| API behavior monitoring | Baseline normal API usage patterns; alert on anomalies |
| Rate limiting | Prevent abuse and brute force against API endpoints |

---

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

---
---

# Data Pillar

> Zero Trust implementation guide for the Data pillar: classification, protection, and access governance

**CISA Pillar:** Data
**Core Question:** Is data classified, is access restricted to authorized subjects, and is data protected regardless of where it resides?

---

## Why Data Is the Ultimate ZT Target

Every other pillar exists to protect data. Identity controls verify who is accessing data. Device controls verify the health of what is accessing data. Network controls restrict where data can flow. Application controls manage how data is accessed. The data pillar directly addresses the classification, protection, and governance of the data itself.

An enterprise that has mature identity, device, network, and application controls but no data-level controls can still suffer a significant breach if an authorized user exfiltrates data through legitimate channels.

---

## Data Maturity Stages (CISA Model)

| Control Area | Traditional | Initial | Advanced | Optimal |
|---|---|---|---|---|
| Data discovery | Unknown data estate | Discovery started for structured data | All structured and most unstructured data classified | Continuous data discovery and classification |
| Data classification | Manual, inconsistent | Tool-assisted classification | Automated classification at creation and modification | Real-time classification with ML |
| DLP | None or basic email DLP | DLP on some channels | Inline DLP on web, email, and cloud | Unified DLP across all egress channels |
| Rights management | None | Applied to some document types | Applied to all sensitive data | Automatic rights application based on classification |
| Data residency and sovereignty | Unknown | Partially mapped | Documented and enforced for regulated data | Automated enforcement with continuous audit |
| Encryption at rest | Partial | Full disk encryption | Field-level encryption for sensitive data | Envelope encryption with managed keys |

---

## Control 1: Data Discovery and Classification

### Classification Framework

A functional data classification framework has four to five tiers with clear criteria for each:

| Classification | Description | Examples | Minimum Controls |
|---|---|---|---|
| Public | Approved for public release | Marketing materials, published documentation | None beyond integrity |
| Internal | Business data not approved for public release | Internal policies, meeting notes, general business data | Access controls, encryption in transit |
| Confidential | Sensitive business data, limited distribution | Financial forecasts, M&A data, personnel files, contracts | Access controls, encryption at rest and in transit, DLP |
| Restricted | Highest sensitivity, regulatory or legal requirement | PII, PHI, PCI data, trade secrets, credentials | Strict access controls, encryption, DLP, rights management, audit logging |

### Classification Methods

**Manual classification:** Users apply labels at creation. Unreliable as the sole method. Users make errors and labels are often omitted.

**Pattern-based automatic classification:** The classification tool scans for patterns (credit card numbers, social security numbers, NHS numbers, IBAN codes) and applies labels automatically. Reliable for structured sensitive data types.

**ML-based classification:** Machine learning models trained on the organization's data to classify documents based on content, structure, and context. Higher accuracy for unstructured data (contracts, emails, documents).

**Inheritance-based classification:** A document inheriting the classification of its container (a folder classified as Confidential automatically classifies documents copied into it as Confidential).

---

## Control 2: Data Loss Prevention

DLP prevents sensitive data from leaving the organization through unauthorized channels: email, web upload, USB, cloud storage, printing, screenshot.

### DLP Coverage Channels

| Channel | Tool | Detection Capability |
|---|---|---|
| Email (outbound) | Exchange Online DLP, Proofpoint, Mimecast | Header inspection, content inspection, attachment inspection |
| Web upload | CASB inline, SWG with DLP | Content inspection of files uploaded to web applications |
| Cloud storage (sanctioned) | CASB API mode | Files uploaded to OneDrive, SharePoint, Teams, Box |
| Cloud storage (unsanctioned) | CASB inline, SWG | Block or warn on upload to personal Dropbox, Google Drive |
| Endpoint (USB, clipboard, screenshot) | Endpoint DLP (Purview, CrowdStrike, Symantec) | Activity monitoring at the OS level |
| Printing | Endpoint DLP | Print job interception and content inspection |

### DLP Policy Framework

**Policy 1: Credit card numbers leaving corporate boundaries**
Detect: PCI data patterns (16-digit card numbers with common prefixes). Action: Block email and web upload. Alert the compliance team.

**Policy 2: Social security or national identity numbers in email**
Detect: SSN patterns, NI number patterns. Action: Encrypt email automatically or block external sending. Alert.

**Policy 3: Classified documents sent to personal email**
Detect: Documents labeled Confidential or Restricted as attachments to personal email domains. Action: Block and alert.

**Policy 4: Bulk data download from sensitive applications**
Detect: Unusual volume of downloads from finance or HR systems. Action: Require justification, alert manager, step-up authentication.

---

## Control 3: Encryption

Encryption ensures that data is unreadable without the appropriate key, even if the underlying storage or transport is compromised.

### Encryption Requirements by Classification

| Classification | At Rest | In Transit | Rights Management |
|---|---|---|---|
| Public | Optional | TLS recommended | Not required |
| Internal | Full disk encryption minimum | TLS required | Not required |
| Confidential | Full disk + service-level encryption | TLS 1.2+ required | Recommended for documents |
| Restricted | Full disk + field-level encryption | mTLS for service-to-service | Required. Key access audited. |

### Key Management

Encryption is only as strong as the key management. Minimum requirements:
- Keys stored separately from the data they protect
- Hardware Security Modules (HSM) for key storage in regulated environments
- Key rotation on defined schedule
- Customer-managed keys for cloud-hosted regulated data
- Key access audit log maintained and reviewed

---

## Data Pillar Checklist

- [ ] Data inventory initiated for structured data (databases, file shares)
- [ ] Data classification framework documented and approved
- [ ] Classification tool deployed (Microsoft Purview sensitivity labels or equivalent)
- [ ] Automatic classification policies covering at least PII, PCI, and PHI patterns
- [ ] DLP policies active on email for regulated data types
- [ ] CASB inline or API mode active for cloud storage DLP
- [ ] Endpoint DLP deployed for USB and clipboard controls on sensitive endpoints
- [ ] Rights management applied to Confidential and Restricted documents
- [ ] Encryption at rest enabled for all classified data stores
- [ ] Customer-managed keys deployed for regulated cloud workloads
- [ ] Key management policy documented with rotation schedule
- [ ] Data access reviews running on defined cadence
- [ ] Data residency requirements mapped for regulated data categories
