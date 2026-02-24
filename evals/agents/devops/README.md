# DevOps Engineer Test Suite

## Overview

Test suite for the DevOps engineer subagent specialized in Infrastructure as Code (Terraform, Kubernetes, Docker).

**Agent Path**: `security/devops-engineer`  
**Agent File**: `.opencode/agent/subagents/security/devops.md`

## Test Coverage

| Category | Tests | Description |
|----------|-------|-------------|
| Critical Rules | 2 | Terraform plan-first, context loading |
| Workflow | 2 | Incremental IaC creation, tool usage |
| Edge Cases | 1 | Error handling and validation |
| Integration | 2 | Extended thinking, long-horizon sessions |
| Delegation | 1 | Handoff provisioning plans |

## Running Tests

```bash
cd evals/framework

# Run all tests
npm run eval:sdk -- --agent=security/devops-engineer

# Run specific suite
npm run eval:sdk -- --agent=security/devops-engineer --pattern="01-critical-rules/**/*.yaml"
```

## Agent Behavior

The DevOps engineer:
- Always runs `terraform plan` before `apply`
- Never hardcodes secrets in IaC
- Uses remote state with locking
- Applies least-privilege IAM policies
