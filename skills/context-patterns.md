---
name: context-patterns
description: Shared knowledge about context file patterns and best practices
---

# Context File Patterns

This skill contains shared knowledge about how to create effective context files for AI coding assistants.

## CLAUDE.md Best Practices

### Structure
- Start with a 2-3 sentence overview
- List tech stack early (language, framework, infra)
- Group information by "what you need to know" priority
- Use clear section headers

### Gotcha Documentation
Good gotcha format:
```markdown
### ⚠️ PaymentRetryService.kt::retryWithBackoff
The retry intervals are tuned for Stripe's rate limits. 
Changing them may get us temporarily banned.
**Last verified**: 2024-01 by @jane
```

Bad gotcha format:
```markdown
### Warning
Be careful with payments.
```

### Scalability Warnings
Always include:
- What the issue is
- When it becomes a problem (at what scale)
- Suggested fix or workaround

```markdown
### ⚡ N+1 Query in OrderService.kt::getOrdersWithUsers
Currently loads users one-by-one in the order loop.
**Impact**: 1000 orders = 1001 queries, ~3s response time
**Fix**: Use `userRepository.findAllById(orderUserIds)`
**Priority**: Fix before Black Friday traffic
```

### Historical Decisions
Document the "why" not just the "what":
```markdown
## Historical Decisions

### Why polling instead of webhooks (2022-03)
Our payment provider's webhooks were unreliable (30% delivery rate).
Polling every 30s was more reliable. Re-evaluate if we switch providers.
**Decision by**: @john, @jane
```

## Confusion Categories

Code that needs human explanation falls into four categories. Use these to structure documentation:

### WHAT — "What is this doing?"
Complex logic that isn't self-explanatory.

```markdown
### 🤔 WHAT: RateLimiter.kt::calculateBackoff
Complex exponential backoff with jitter. The formula accounts for:
- Base delay: 1000ms (Stripe's minimum between retries)
- Multiplier: 1.5x (stays under rate limit ceiling)
- Jitter: ±200ms (prevents thundering herd)
**Owner**: @jane
```

Signals: deep nesting, long functions, dense conditionals, regex, bitwise ops, magic numbers

### WHEN — "When does this run? In what order?"
Timing and ordering dependencies.

```markdown
### ⏱️ WHEN: OrderProcessor.kt::processOrder
**Required order**:
1. `inventoryService.reserve()` - must succeed first
2. `paymentService.charge()` - only after inventory reserved
3. `shippingService.schedule()` - only after payment confirmed

**Failure handling**: If payment fails, inventory reservation auto-expires after 15 minutes.
```

Signals: temporal coupling, schedulers, event handlers, async coordination, transaction boundaries

### HISTORY — "Why is it like this?"
Decisions that only make sense with context.

```markdown
### 📜 HISTORY: FeeCalculator.kt::calculateKoreanTax
Legacy code from 2021 acquisition of KR payment provider.
- Different tax rules than our standard calculator
- Kept separate to avoid regression in KR market
- Only @john understands the edge cases
**Do not merge with main FeeCalculator without full regression testing.**
```

Signals: TODO/FIXME/HACK comments, "don't touch" warnings, version suffixes (V1, V2), inconsistent patterns

### DUPLICATE — "Which one is canonical?"
Similar code where the source of truth is unclear.

```markdown
### 🔀 DUPLICATE: Tax Calculators
Multiple implementations exist:
- `TaxCalculator.kt` - **CANONICAL** for new code
- `TaxCalculatorV2.kt` - Used only by legacy orders API
- `LegacyTax.kt` - Deprecated, do not use

**Migration plan**: TaxCalculatorV2 will be removed in Q2 2025.
```

Signals: similar function names, copy-paste code, multiple implementations of same interface

## Pattern Library

### Service Documentation Template
```markdown
### [ServiceName]
**Purpose**: [one line]
**Owner**: [@person]
**Key Files**: 
- `src/main/kotlin/ServiceName.kt` - Main logic
- `src/test/kotlin/ServiceNameTest.kt` - Tests

#### Gotchas
- [specific gotcha with file::function reference]

#### Dependencies
- Calls: [other services]
- Called by: [other services]
- External: [APIs, databases]
```

### Common Anti-Patterns to Flag

| Pattern | Detection | Severity |
|---------|-----------|----------|
| N+1 queries | Loop + single fetch | Critical |
| Unbounded query | `findAll()` without limit | Critical |
| In-memory cache | `HashMap`/`ConcurrentHashMap` singleton | Warning |
| No timeout | HTTP client without timeout config | Warning |
| Missing index hint | Complex query without index comment | Note |

### Output Format Guidelines

#### For Humans (CLAUDE.md)
- Markdown with clear headers
- Emoji for visual scanning (⚠️ 🔴 ⚡ ✅ 🤔 ⏱️ 📜 🔀)
- Concrete examples over abstract descriptions
- Names of people, not just roles
- Use `file::function` references, not line numbers (more stable)

#### For Machines (context.json)
- Consistent schema
- Enums for categories
- File hashes for staleness detection
- ISO dates for timestamps

## Reference Location Format

Always use `file::function` format instead of line numbers:

**Good**: `PaymentService.kt::processPayment`
**Bad**: `PaymentService.kt:45`

Why: Line numbers change frequently with code edits. Function references remain stable and are easier to find.
