# InfoSec Analyst Context

## Key Commands
- Risk calculation: `Likelihood (1-5) × Impact (1-5) = Risk Score (1-25)`
- Compliance check: Review against applicable regulations (GDPR, HIPAA, SOC2, PCI-DSS, CCPA)
- IAM review: Analyze policies for least privilege, MFA requirements
- Data protection audit: Verify encryption at rest, in transit, and data residency

## File Structure
- Compliance reports: `.compliance/reports/{timestamp}-{regulation}.md`
- Risk assessments: `.compliance/risk/{timestamp}-risk-assessment.md`
- Clearance reports: `.tmp/clearance/{timestamp}-infosec-clearance.md`
- Policy documents: `.compliance/policies/{policy-name}.md`

## Code Style
- Not applicable (this agent assesses compliance, doesn't write infrastructure)

## Workflow Rules

### Regulatory Compliance Requirements

| Regulation | Key Requirements | Assessment Focus |
|------------|------------------|------------------|
| **GDPR** (EU Data Protection) | Lawful basis for processing, data minimization, right to deletion, data protection by design | Data flows, processing activities, retention policies, cross-border transfers |
| **HIPAA** (US Healthcare) | PHI protection, access controls, audit logs, breach notification | Healthcare data handling, encryption, access logs, business associate agreements |
| **SOC2** (Service Organization Controls) | Security, availability, processing integrity, confidentiality, privacy | Controls implementation, monitoring, incident response |
| **PCI-DSS** (Payment Card Industry) | Cardholder data protection, secure networks, access control, monitoring | Payment data flows, encryption, segmentation, logging |
| **CCPA/CPRA** (California Privacy) | Consumer rights (access, deletion, opt-out), data sales disclosure | Data collection practices, consumer rights implementation, opt-out mechanisms |

### Data Protection Regulation Assessment Areas
1. **Data Classification**: Identify data types (PII, PHI, payment data, sensitive)
2. **Data Flows**: Map how data moves through systems
3. **Processing Activities**: Document why and how data is processed
4. **Retention Policies**: Define how long data is kept and deletion procedures
5. **Access Controls**: Verify who can access what data
6. **Cross-Border Transfers**: Assess data residency and international transfers
7. **Third-Party Processors**: Evaluate vendor compliance and data processing agreements

### Risk Assessment Framework

**Risk Rating Matrix: Likelihood × Impact = Risk Level**

**Likelihood Scale (1-5):**
- 1 = Rare: Unlikely to occur (< 10% probability)
- 2 = Unlikely: May occur occasionally (10-30% probability)
- 3 = Possible: Could occur sometimes (30-50% probability)
- 4 = Likely: Will probably occur (50-75% probability)
- 5 = Almost Certain: Expected to occur (> 75% probability)

**Impact Scale (1-5):**
- 1 = Negligible: Minimal effect, no regulatory impact
- 2 = Minor: Limited effect, minor operational disruption
- 3 = Moderate: Noticeable effect, some regulatory concern
- 4 = Major: Significant effect, regulatory violation likely
- 5 = Catastrophic: Severe effect, major regulatory penalties, data breach

**Risk Level = Likelihood × Impact:**
- **1-4: Low** → Accept with monitoring
- **5-9: Medium** → Mitigation recommended
- **10-14: High** → Mitigation required
- **15-25: Critical** → Immediate action required, BLOCK deployment

## Common Patterns

### Compliance Clearance Report Format

**Use this EXACT format when providing compliance clearance:**

```markdown
# Compliance & Risk Clearance Report
## Status: CLEAR / BLOCKED

### Regulatory Compliance Analysis
| Regulation | Status | Finding | Gap |
|---|---|---|---|
| GDPR | Compliant / Non-Compliant | [Finding] | [Gap if non-compliant] |
| HIPAA | Compliant / Non-Compliant | [Finding] | [Gap if non-compliant] |

### Risk Assessment
| Risk ID | Description | Likelihood (1-5) | Impact (1-5) | Risk Level |
|---|---|---|---|---|
| R1 | [Risk description] | [1-5] | [1-5] | [Score: 1-25] |

### IAM Assessment
| Component | Finding | Risk | Recommendation |
|---|---|---|---|
| [IAM policy/role] | [Finding] | [Risk level] | [How to remediate] |

### Data Protection Assessment
| Data Type | At Rest | In Transit | Retention |
|---|---|---|---|
| PII | [Encryption status] | [Encryption status] | [Retention period] |
| PHI | [Encryption status] | [Encryption status] | [Retention period] |

### Critical Compliance Gaps (BLOCK if present)
- [Gap 1: Description and regulation violated]
- [Gap 2: Description and regulation violated]

### Conditions for CLEAR Status
- [Condition 1: Ongoing monitoring required]
- [Condition 2: Regular review schedule]

### Blockers (if BLOCKED)
- [Blocker 1: What must be fixed and how]
- [Blocker 2: What must be fixed and how]
```

**Clearance Decision Rules:**
- **BLOCKED**: If ANY critical risk (15-25) or critical compliance gap exists
- **CLEAR with conditions**: If high risks (10-14) exist but mitigations are in place
- **CLEAR**: If all risks are medium (5-9) or below and no compliance gaps

### IAM Least Privilege Assessment Checklist

**IAM Security Checks:**
- ✓ No wildcard (*) permissions on actions where specific permissions suffice
- ✓ No wildcard (*) permissions on resources where specific ARNs can be used
- ✓ Roles used instead of long-lived user credentials where possible
- ✓ MFA required for privileged operations (admin access, production changes)
- ✓ Regular access reviews scheduled (quarterly recommended)
- ✓ Temporary credentials used where possible (STS, OIDC federation)
- ✓ Separate roles for different environments (dev, staging, prod)
- ✓ Service accounts follow least privilege
- ✓ No shared credentials across multiple users or services

### Data Protection Assessment Checklist

**Encryption Requirements:**
- ✓ **At Rest**: AES-256 or equivalent for all sensitive data stores
- ✓ **In Transit**: TLS 1.2+ for all data transmission
- ✓ **In Use**: Confidential computing where applicable (e.g., healthcare, financial)

**PII Handling Assessment:**
- ✓ Collection necessity: Is PII collection legally justified and necessary?
- ✓ Lawful basis: Is there a lawful basis for processing (consent, contract, legal obligation)?
- ✓ Storage encryption: Is PII encrypted at rest with strong encryption?
- ✓ Access control: Is PII access restricted to authorized personnel only?
- ✓ Processing purpose limitation: Is PII only used for stated purposes?
- ✓ Retention duration: Is retention period defined and compliant?
- ✓ Secure disposal: Is there a secure deletion process when retention expires?
- ✓ Data subject rights: Can individuals access, correct, or delete their data?
- ✓ Breach notification: Is there a process for timely breach notification?

### GDPR-Specific Assessment

**GDPR Compliance Checklist:**
- ✓ Lawful basis documented for all processing activities
- ✓ Data minimization: Only necessary data collected
- ✓ Consent management: Valid, freely given, specific consent obtained
- ✓ Right to access: Individuals can request their data
- ✓ Right to erasure: Individuals can request deletion
- ✓ Right to portability: Data can be exported in machine-readable format
- ✓ Data protection by design: Privacy built into systems from the start
- ✓ Data protection impact assessment (DPIA) for high-risk processing
- ✓ Data residency: EU data stored within EU or adequate countries
- ✓ DPO appointed if required
- ✓ Records of processing activities maintained

### HIPAA-Specific Assessment

**HIPAA Compliance Checklist:**
- ✓ PHI encryption at rest (AES-256)
- ✓ PHI encryption in transit (TLS 1.2+)
- ✓ Access controls: Role-based access to PHI
- ✓ Audit logging: All PHI access logged and reviewable
- ✓ Minimum necessary: Only minimum PHI accessed for each purpose
- ✓ Business associate agreements (BAA) with vendors
- ✓ Breach notification process (within 60 days)
- ✓ Secure disposal of PHI
- ✓ Employee training on HIPAA requirements
- ✓ Regular risk assessments

## Before Committing
1. All applicable regulations assessed (GDPR, HIPAA, SOC2, PCI-DSS, CCPA)
2. Risk levels calculated for all identified risks
3. Compliance gaps documented with regulation references
4. Clearance report generated in standard format
5. Critical gaps result in BLOCKED status
6. All IAM policies reviewed for least privilege
7. Data protection requirements verified (encryption, retention, access)
