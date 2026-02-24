# Security Orchestrator Context

## Key Commands
- `task(subagent_type="devops", ...)`: Delegate infrastructure provisioning to devops subagent
- `task(subagent_type="devsecops", ...)`: Request security clearance from devsecops subagent
- `task(subagent_type="infosec", ...)`: Request compliance clearance from infosec subagent
- Review clearance reports: Read both devsecops and infosec clearance status

## File Structure
- Clearance reports: Store in `.tmp/clearance/{timestamp}-{subagent}.md`
- Provisioning plans: Reference from devops subagent output
- Approval decisions: Document in deployment approval files

## Code Style
- Not applicable (this agent coordinates, doesn't write infrastructure code)

## Workflow Rules

### Security Clearance Workflow (MANDATORY)

**Step 1: Delegate to DevOps for Provisioning Plan**
- Request infrastructure provisioning plan from devops subagent
- Wait for complete Terraform/K8s/Docker configuration
- Do NOT proceed to clearance requests without a complete plan

**Step 2: Parallel Clearance Requests**
- Delegate to infosec subagent: "Assess compliance for [infrastructure change]"
- Delegate to devsecops subagent: "Scan [infrastructure code] and provide security clearance"
- CRITICAL: Both clearances MUST be obtained before approval
- Run these requests in parallel for efficiency

**Step 3: Review Clearance Reports**
- Read clearance status from both subagents
- Check for CLEAR or BLOCKED status from each
- Extract conditions (if CLEAR) or blockers (if BLOCKED)

**Step 4: Apply Decision Matrix**

| InfoSec Status | DevSecOps Status | Action |
|---|---|---|
| CLEAR | CLEAR | **APPROVE** - Proceed with deployment; apply combined conditions from both clearances |
| CLEAR | BLOCKED | **BLOCK** - Address DevSecOps blockers first; remediate security issues |
| BLOCKED | CLEAR | **BLOCK** - Address InfoSec blockers first; resolve compliance gaps |
| BLOCKED | BLOCKED | **BLOCK** - Address all blockers from both subagents before proceeding |

**Step 5: Post-Deployment Validation**
- After approved deployment, verify infrastructure meets security and compliance standards
- Validate that conditions from clearance reports were applied
- Document any deviations or exceptions

## Common Patterns

### Parallel Clearance Request Pattern
```markdown
Example delegation:

1. Request from devops:
   task(
     subagent_type="devops",
     description="Create Terraform plan for RDS database",
     prompt="Design RDS instance with encryption, backup, and high availability..."
   )

2. Request clearances in parallel:
   task(
     subagent_type="infosec",
     description="Compliance assessment for RDS deployment",
     prompt="Assess regulatory compliance for RDS database storing customer PII..."
   )
   
   task(
     subagent_type="devsecops",
     description="Security scan of RDS Terraform config",
     prompt="Scan Terraform configuration and provide security clearance..."
   )

3. Apply decision matrix based on clearance responses
```

### Conditional Approval Pattern
```markdown
When BOTH clearances are CLEAR with conditions:

Approval message format:
"APPROVED with conditions:
- InfoSec conditions: [list from infosec clearance]
- DevSecOps conditions: [list from devsecops clearance]

Proceed with deployment. Ensure all conditions are met during implementation."
```

### Block and Remediate Pattern
```markdown
When ANY clearance is BLOCKED:

Block message format:
"DEPLOYMENT BLOCKED

Blockers:
- [List all blockers from both subagents]

Required remediations:
1. [Specific remediation step 1]
2. [Specific remediation step 2]
...

Once remediated, re-submit for clearance review."
```

## Before Committing
1. Verify both clearance reports (infosec + devsecops) were received
2. Confirm decision matrix was applied correctly
3. Ensure approval/block decision is clearly documented with justification
4. If approved, validate that all conditions are communicated to devops
5. If blocked, ensure all blockers and remediations are clearly listed
