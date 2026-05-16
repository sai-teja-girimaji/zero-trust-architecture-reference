# Architecture Decision Records

> Key Zero Trust architectural decisions with rationale, alternatives considered, tradeoffs, and consequences

**Format:** Each ADR follows the standard structure: Context, Decision, Rationale, Alternatives Considered, Consequences, and Review Trigger.

---

## ADR 001: Identity-First Sequencing

**Date:** Reference only (adapt to your program timeline)
**Status:** Recommended

### Context

A Zero Trust program must be sequenced. Attempting to implement all five pillars simultaneously is operationally infeasible for most organizations. A sequencing decision is required.

### Decision

Implement the Identity pillar first, before device, network, application, or data controls.

### Rationale

Identity is the highest-impact control with the lowest infrastructure dependency. MFA can be enforced across an entire organization without deploying new infrastructure, without network changes, and without significant application re-architecture. The return on investment is immediate and measurable.

Credential-based attacks are the dominant initial access vector in enterprise breaches. Addressing identity first addresses the most common attack path.

### Alternatives Considered

**Network segmentation first:** High complexity, long implementation timeline, limited visible impact on identity-based attack vectors. Does not address credential theft.

**SASE/ZTNA first:** Requires vendor selection, POC, and organizational change management. Longer timeline before any security value is delivered. Also requires identity controls to function correctly (ZTNA is identity-aware).

**Data classification first:** Important but does not prevent initial access or lateral movement. Better positioned later when there is more organizational context about what data exists.

### Consequences

**Positive:** Measurable reduction in credential-based breach risk within months. Establishes identity as the authoritative source of truth for all subsequent ZT decisions.

**Negative:** Identity enforcement without device compliance creates a partial ZT state. A valid identity on a compromised device is still a risk. Phase 2 (device trust) must follow quickly.

### Review Trigger

Re-evaluate if the organization has a significant OT/ICS environment where identity-centric controls cannot be applied to all assets.

---

## ADR 002: Inside-Out ZTNA Architecture

**Date:** Reference only
**Status:** Recommended

### Context

When implementing ZTNA to replace VPN, two architectural models exist: inside-out (where the connector makes outbound connections to the ZTNA cloud) and outside-in (where a reverse proxy accepts inbound connections and forwards to resources).

### Decision

Adopt inside-out ZTNA architecture for all new application access deployments.

### Rationale

Inside-out ZTNA eliminates the need for inbound firewall rules on the resource-hosting network. Resources are not internet-visible. An attacker who compromises the user-facing ZTNA endpoint cannot scan or enumerate the resource-hosting environment because no inbound ports are open.

The attack surface reduction is significant. The resource exists only from the perspective of the ZTNA platform and the App Connector. From the internet's perspective, the resource does not exist.

### Alternatives Considered

**Outside-in reverse proxy:** Easier to implement for some legacy application scenarios. However, the resource-hosting network still has inbound rules. The reverse proxy becomes a target. Lateral movement from the proxy to the resource is possible if the proxy is compromised.

**Traditional VPN:** Grants network-level access, not application-level access. Does not enforce per-application access policies. Does not provide application-layer visibility.

### Consequences

**Positive:** Significant reduction in internet-exposed attack surface. Application access is brokered through the ZTNA platform, enabling centralized policy enforcement and visibility.

**Negative:** Each hosting environment (on-premises, AWS, Azure, GCP) requires a connector deployment. Connector deployment and maintenance is an ongoing operational requirement. Connector availability affects application availability.

### Review Trigger

Re-evaluate for specific legacy applications that cannot be proxied (non-HTTP/S protocols with complex session requirements). These may require alternative access controls.

---

## ADR 003: Managed Device Requirement for Sensitive Application Access

**Date:** Reference only
**Status:** Recommended

### Context

Personal devices (BYOD) used to access enterprise applications present a control gap. The enterprise cannot enforce compliance policies, deploy EDR, or rotate credentials on personal devices. Yet users expect to access work applications from personal devices.

### Decision

Require a managed, compliant device to access applications classified as Tier 1 (Critical) and Tier 2 (Sensitive). Personal devices (BYOD) are permitted to access Tier 3 and Tier 4 applications with MAM controls applied.

### Rationale

Tier 1 and Tier 2 applications contain the organization's most sensitive data. The risk of allowing access from unmanaged devices outweighs the convenience benefit. If a personal device is compromised, the attacker has valid credentials and an unmanaged device with no EDR, no tamper protection, and no organizational visibility. This is effectively an uncontrolled access path to sensitive data.

MAM for BYOD devices provides a practical middle ground: enterprise app data is containerized and controlled, but the personal device itself is not enrolled in MDM.

### Alternatives Considered

**Allow BYOD for all applications:** Maximum user flexibility but creates an uncontrolled access path to sensitive data. Inconsistent with ZT principles.

**Block all BYOD:** Maximum control but significantly impacts user experience and productivity, particularly for executives and contractors who resist device management.

**BYOD with enhanced authentication only:** Relying on MFA alone for BYOD access to sensitive applications is insufficient. A compromised personal device with valid MFA credentials is a high-risk scenario.

### Consequences

**Positive:** Eliminates unmanaged device access to the most sensitive applications. Forces a clear accountability model: you use a managed device, or you use personal apps only.

**Negative:** Creates friction for users who want to access work applications from personal devices. Requires clear communication and a support process for device enrollment. May require hardware provisioning for users who previously relied entirely on personal devices.

### Review Trigger

Re-evaluate if a strong containerization technology emerges that provides equivalent assurance to MDM enrollment without device management overhead.

---

## ADR 004: Phased VPN Elimination

**Date:** Reference only
**Status:** Recommended

### Context

Traditional VPN must be eliminated as part of the ZT program, but doing so immediately creates significant operational risk. Applications accessed via VPN must be migrated to ZTNA before VPN can be decommissioned.

### Decision

Eliminate VPN in phases tied to application migration completeness. Do not decommission VPN until at least 95% of applications have validated ZTNA access paths with no reported access failures for 30 days.

### Rationale

Premature VPN decommission before all applications are migrated via ZTNA will break legitimate user access. The operational disruption will damage program credibility and may force a rollback that is harder to recover from than a slower migration timeline.

A phased approach allows validation at each stage and provides a fallback path if unexpected issues arise during migration.

### Consequences

**Positive:** Reduced operational risk during migration. Early phases deliver ZT value (application-specific access, better visibility) while VPN remains as a fallback.

**Negative:** VPN operating costs continue during the migration period. Users may continue to prefer VPN for convenience if it is still available, slowing adoption of ZTNA.

**Mitigation:** Monitor VPN usage by application after each ZTNA migration phase. Disable VPN access for specific applications once ZTNA is confirmed working rather than leaving VPN as a general fallback.

### Review Trigger

Re-evaluate timeline if ZTNA adoption is significantly slower than expected due to application compatibility issues.

---

## ADR 005: DNS Security Before Microsegmentation

**Date:** Reference only
**Status:** Recommended

### Context

Network ZT controls can be sequenced in multiple ways. Microsegmentation provides the strongest east-west control but is operationally complex. DNS security is simpler to deploy and provides immediate value.

### Decision

Deploy DNS security (DNS filtering and query logging) before implementing microsegmentation. Use DNS telemetry to inform segmentation design.

### Rationale

DNS security can be deployed to the full user population within weeks with minimal operational disruption. It immediately begins blocking malware domains and DNS-based C2 communication, and it generates telemetry that informs the subsequent microsegmentation design.

Microsegmentation requires a complete east-west traffic map to implement without breaking production systems. DNS query logs contribute to this traffic visibility alongside NetFlow and firewall logs.

### Consequences

**Positive:** Immediate reduction in DNS-based threat exposure. DNS telemetry collected during the DNS security phase improves the quality of the subsequent segmentation design.

**Negative:** DNS security does not address east-west lateral movement. An attacker already inside the network is not significantly constrained by DNS filtering alone. Microsegmentation must follow.

### Review Trigger

No specific trigger. This sequence is appropriate for most environments.

---

## ADR 006: No Standing Privileged Access for Tier 0 Assets

**Date:** Reference only
**Status:** Recommended

### Context

Traditional privileged access management grants administrator accounts persistent elevated permissions. These accounts exist permanently with high-level access and require only authentication (not authorization) for each privileged operation.

### Decision

Eliminate standing (persistent) privileged access to Tier 0 assets. All Tier 0 access is granted just-in-time, for a specific purpose and time window, through an approval workflow, and is automatically revoked at expiry.

### Rationale

Standing privileged accounts are high-value targets. An attacker who compromises a Global Administrator account or Domain Administrator has unrestricted access to the most critical systems. Standing access means the attack window is permanent: the attacker can use the account at any time.

Just-in-time access eliminates the standing exposure. An attacker who compromises a user account that normally has no privileged access cannot immediately move to Tier 0 systems. They must first acquire a JIT access grant, which requires going through an approval workflow that can be monitored and alerted on.

### Consequences

**Positive:** Eliminates the permanent high-value target that standing admin accounts represent. JIT access requests are a high-fidelity alert signal.

**Negative:** Operational overhead for each privileged access session. Administrators must plan their privileged work, request access, and work within a time window. Emergency access procedures must be documented and tested (break-glass).

**Mitigation:** Define common privileged tasks that warrant pre-approved JIT policies with no manual approval required (for example: standard patching operations). Reserve manual approval for out-of-pattern privileged access requests.

### Review Trigger

Re-evaluate JIT scope and time windows based on operational feedback from administrators after 90 days of operation.
