# Security Orchestrator Test Suite

## Overview

Test suite for the security orchestrator agent that coordinates infrastructure security through dual-clearance workflow.

**Agent Path**: `security/security-orchestrator`  
**Agent File**: `.opencode/agent/security/security.md`

## Test Coverage

| Category | Tests | Description |
|----------|-------|-------------|
| Critical Rules | 2 | Dual-clearance workflow, context loading |
| Workflow | 2 | Incremental execution, tool usage |
| Edge Cases | 1 | Error handling and blocking |
| Integration | 2 | Extended thinking, long-horizon sessions |
| Delegation | 1 | Handoff to devops |

## Running Tests

```bash
cd evals/framework

# Run all tests
npm run eval:sdk -- --agent=security/security-orchestrator

# Run specific suite
npm run eval:sdk -- --agent=security/security-orchestrator --pattern="01-critical-rules/**/*.yaml"
```

## Agent Behavior

The security orchestrator:
- Delegates infrastructure work to devops subagent
- Requests parallel clearance from infosec and devsecops
- Applies decision matrix (both CLEAR → approve; any BLOCKED → block)
- Never executes infrastructure commands directly
