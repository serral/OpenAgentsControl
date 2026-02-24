# DevSecOps Specialist Context

## Key Commands
- `checkov -d . --quiet --compact`: Scan Terraform/CloudFormation for security issues
- `terrascan scan -t terraform`: Policy-as-code scanning for IaC
- `trivy image <image:tag>`: Comprehensive container vulnerability scanning
- `trivy fs .`: Scan filesystem for vulnerabilities and misconfigurations
- `docker scout cves <image:tag>`: Docker's native CVE scanning
- `kubescape scan framework nsa .`: Kubernetes security scanning (NSA hardening guide)
- `gitleaks detect`: Scan for secrets in git history
- `trufflehog git file://.`: Scan for secrets in repository

## File Structure
- Scan results: `.security/scans/{timestamp}-{tool}.json`
- Policy files: `.security/policies/{policy-name}.yaml`
- Clearance reports: `.tmp/clearance/{timestamp}-devsecops-clearance.md`
- Ignore files: `.trivyignore`, `.checkov.yml` for suppressing false positives

## Code Style
- Not applicable (this agent audits code, doesn't write infrastructure)

## Workflow Rules

### Security Clearance Report Format

**Use this EXACT format when providing clearance:**

```markdown
# Security Clearance Report
## Status: CLEAR / BLOCKED

### Critical Findings (BLOCK if present)
| # | Finding | Risk | Remediation |
|---|---------|------|-------------|
| 1 | [Finding description] | [Risk explanation] | [How to fix] |

### High Findings
| # | Finding | Risk | Remediation |
|---|---------|------|-------------|
| 1 | [Finding description] | [Risk explanation] | [How to fix] |

### Medium Findings
| # | Finding | Risk | Remediation |
|---|---------|------|-------------|
| 1 | [Finding description] | [Risk explanation] | [How to fix] |

### Conditions for CLEAR Status
[If CLEAR with conditions, list them here]
- Condition 1
- Condition 2

### Blockers (if BLOCKED)
[If BLOCKED, list specific issues that must be resolved]
- Blocker 1: [Description and remediation]
- Blocker 2: [Description and remediation]
```

**Clearance Decision Rules:**
- **BLOCKED**: If ANY critical or high severity findings exist
- **CLEAR with conditions**: If only medium/low findings exist, but monitoring required
- **CLEAR**: If no findings or all findings are informational

## Common Patterns

### Infrastructure as Code (IaC) Security Checklist

**Terraform Security Checks:**
- ✓ No open security groups (0.0.0.0/0 to sensitive ports like 22, 3389, 3306, 5432)
- ✓ No unencrypted storage (S3, RDS, EBS volumes must have encryption enabled)
- ✓ No hardcoded secrets in variables, templates, or default values
- ✓ No overly permissive IAM policies (wildcard * on actions or resources)
- ✓ No public S3 buckets without explicit business justification
- ✓ Encryption at rest enabled for all data stores (RDS, DynamoDB, S3, EBS)
- ✓ Logging enabled for all resources (CloudTrail, VPC Flow Logs, S3 access logs)
- ✓ Versioning enabled for S3 buckets containing critical data
- ✓ MFA delete enabled for S3 buckets with compliance requirements
- ✓ No public RDS instances (publicly_accessible = false)

**Kubernetes Security Checks:**
- ✓ No privileged containers (`privileged: false`)
- ✓ No containers running as root (`runAsNonRoot: true`)
- ✓ No hostPath volumes without read-only flag
- ✓ Security contexts defined for all pods
- ✓ Network policies defined to restrict pod-to-pod communication
- ✓ Pod security standards enforced (restricted, baseline, or privileged with justification)
- ✓ Resource limits and requests defined
- ✓ No hostNetwork, hostPID, or hostIPC enabled
- ✓ AppArmor or Seccomp profiles applied where applicable

### Container Security Checklist

**Docker Image Security Checks:**
- ✓ No critical or high CVEs in base images
- ✓ No secrets in image layers (check with `docker history`)
- ✓ Minimal base images used (alpine, distroless, scratch)
- ✓ Multi-stage builds to reduce final image size
- ✓ Non-root user configured in Dockerfile (`USER nonroot`)
- ✓ Health checks defined (`HEALTHCHECK` instruction)
- ✓ Only necessary files included (use .dockerignore)
- ✓ Base image version pinned (not using `latest` tag)
- ✓ Regular image scanning in CI/CD pipeline

### Secret Detection Checklist

**Secret Scanning Checks:**
- ✓ No AWS access keys or secret keys
- ✓ No API keys or tokens
- ✓ No database passwords or connection strings
- ✓ No private SSH keys or certificates
- ✓ No OAuth tokens or refresh tokens
- ✓ No Terraform/Ansible vault passwords
- ✓ Check git history for committed secrets (use gitleaks)
- ✓ Verify secrets are stored in proper secret management systems

## Tool Integration Guide

### Recommended Tools by Category

| Category | Tool | Purpose | Command Example |
|----------|------|---------|-----------------|
| IaC Scanning | Checkov | Terraform/CloudFormation security | `checkov -d . --framework terraform` |
| IaC Scanning | Terrascan | Policy-as-code for IaC | `terrascan scan -t terraform` |
| Container Scanning | Trivy | Comprehensive vulnerability scanner | `trivy image myapp:1.0` |
| Container Scanning | Snyk | Container security & license scanning | `snyk container test myapp:1.0` |
| Container Scanning | Docker Scout | Docker native CVE scanning | `docker scout cves myapp:1.0` |
| K8s Security | Kubescape | Kubernetes hardening compliance | `kubescape scan framework nsa` |
| Secret Detection | TruffleHog | Find secrets in git history | `trufflehog git file://.` |
| Secret Detection | GitLeaks | Prevent secret leakage | `gitleaks detect --source .` |

### Tool Output Interpretation

**Checkov Severity Levels:**
- `CRITICAL`: Immediate security risk, must fix before deployment
- `HIGH`: Serious security issue, should fix before deployment
- `MEDIUM`: Security concern, fix if possible or document exception
- `LOW`: Minor issue, informational

**Trivy Severity Levels:**
- `CRITICAL`: CVE with CVSS score >= 9.0
- `HIGH`: CVE with CVSS score >= 7.0
- `MEDIUM`: CVE with CVSS score >= 4.0
- `LOW`: CVE with CVSS score < 4.0

## Before Committing
1. All security scans completed (IaC, containers, K8s, secrets)
2. No critical or high findings remaining
3. Clearance report generated in standard format
4. All findings documented with risk and remediation
5. False positives suppressed with justification in ignore files
6. Scan results stored for audit trail
