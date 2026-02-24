# DevSecOps Specialist Test Suite

## Overview

Test suite for the DevSecOps specialist subagent that performs security scanning and infrastructure hardening.

**Agent Path**: `security/devsecops-specialist`  
**Agent File**: `.opencode/agent/subagents/security/devsecops.md`

## Test Coverage

| Category | Tests | Description |
|----------|-------|-------------|
| Critical Rules | 2 | Security scanning before clearance, context loading |
| Workflow | 2 | Incremental scanning, tool usage |
| Edge Cases | 1 | Error handling and blocking |
| Integration | 2 | Extended thinking, long-horizon sessions |
| Delegation | 1 | Handoff clearance reports |

## Running Tests

```bash
cd evals/framework

# Run all tests
npm run eval:sdk -- --agent=security/devsecops-specialist

# Run specific suite
npm run eval:sdk -- --agent=security/devsecops-specialist --pattern="01-critical-rules/**/*.yaml"
```

## Agent Behavior

The DevSecOps specialist:
- Runs IaC scanners (Checkov, Terrascan)
- Scans containers (Trivy) and Kubernetes (Kubescape)
- Issues BLOCKED clearance for critical/high findings
- Provides specific remediation steps
