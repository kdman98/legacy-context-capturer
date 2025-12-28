---
name: detect-scale
description: Scan codebase for scalability anti-patterns and performance issues
---

# Scalability Detection

Scan the codebase for patterns that will cause problems under load, at scale, or with cloud costs.

## What It Detects

### 🔴 Critical Issues
- N+1 query patterns
- Unbounded database queries  
- Missing connection timeouts
- Synchronous loops with I/O
- Expensive API calls in hot paths
- Large payload transfers per request

### 🟡 Warnings
- In-memory state (won't scale horizontally)
- Missing circuit breakers
- Blocking operations in async context
- No retry logic on external calls
- Chatty API patterns (multiple calls instead of batch)
- Redundant external calls without caching

### 🟢 Notes
- Hardcoded limits without documentation
- Caches without TTL
- Single points of failure
- Unmetered third-party API usage

## Usage

```
/detect-scale                       # Scan entire codebase
/detect-scale ./src/services        # Scan specific path
/detect-scale --severity critical   # Only critical issues
/detect-scale --fix                 # Include fix suggestions
```

## Output

For each issue found:
- **Severity**: Critical / Warning / Note
- **Location**: File and function reference (e.g., `OrderService.kt::getOrdersWithUsers`)
- **Category**: load / concurrency / scaling / cost
- **Pattern**: What anti-pattern was detected
- **Impact**: What breaks and when
- **Fix**: Concrete suggestion to resolve
- **Needs Interview**: Whether to ask developer for clarification

Example output:
```json
{
  "id": "scale-001",
  "severity": "critical",
  "category": "load",
  "pattern": "n_plus_one",
  "file": "OrderService.kt",
  "function": "getOrdersWithUsers",
  "impact": "1000 orders = 1001 database queries. Will timeout at scale.",
  "fix": "Use batch fetch with findAllById() or JOIN query.",
  "needs_interview": false
}
```

## Integration

Results can be:
- Displayed inline during development
- Merged into CLAUDE.md via `/capture`
- Fed to interview-agent for developer clarification
- Exported for CI/CD pipelines
- Used for technical debt tracking

## Interview Flags

Some patterns need human clarification before being marked as issues:

| Pattern | Why Interview? |
|---------|---------------|
| `in_memory_state` | May be intentional for single-instance deployment |
| `missing_retry` | Retry might be handled at a different layer |
| `hardcoded_limit` | Developer may know the reasoning behind the number |
| `cache_no_ttl` | Might have external invalidation mechanism |

Issues with `needs_interview: true` are passed to the interview-agent for clarification.
