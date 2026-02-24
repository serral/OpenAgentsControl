# InfoSec Analyst Test Suite

## Overview

Test suite for the InfoSec analyst subagent that evaluates regulatory compliance and risk.

**Agent Path**: `security/infosec-analyst`  
**Agent File**: `.opencode/agent/subagents/security/infosec.md`

## Test Coverage

| Category | Tests | Description |
|----------|-------|-------------|
| Critical Rules | 2 | Compliance assessment before clearance, context loading |
| Workflow | 2 | Incremental risk assessment, tool usage |
| Edge Cases | 1 | Error handling and blocking |
| Integration | 2 | Extended thinking, long-horizon sessions |
| Delegation | 1 | Handoff compliance reports |

## Running Tests

```bash
cd evals/framework

# Run all tests
npm run eval:sdk -- --agent=security/infosec-analyst

# Run specific suite
npm run eval:sdk -- --agent=security/infosec-analyst --pattern="01-critical-rules/**/*.yaml"
```

## Agent Behavior

The InfoSec analyst:
- Assesses compliance (GDPR, HIPAA, SOC2, PCI-DSS)
- Evaluates risk using Likelihood × Impact matrix
- Issues BLOCKED clearance for critical compliance gaps
- Verifies encryption, data residency, audit logging
