---
name: interview
description: Start interactive interview to capture tribal knowledge from developers
---

# Developer Interview

Start an interactive interview session to capture tribal knowledge, gotchas, and historical context from developers who know the codebase.

## What It Does

- Asks structured questions about the codebase
- Uses **confusion points** to ask about unclear code (WHAT, WHEN, HISTORY, DUPLICATE)
- Uses **scalability issues** to clarify intentional patterns vs technical debt
- Captures undocumented knowledge and gotchas
- Records ownership and expertise areas
- Documents historical decisions
- Identifies pain points and technical debt

## Inputs

When run as part of `/capture`, receives context from prior agents:

| Input | From | Used For |
|-------|------|----------|
| `structure.json` | code-analyzer | Understanding codebase layout, asking about specific services |
| `confusion_points.json` | confusion-detector | Targeted questions about unclear code |
| `scalability_report.json` | scalability-detector | Clarifying intentional vs accidental patterns |

This context enables **smart interviewing** - instead of generic questions, the agent asks specifically about flagged issues like:
- "This retry logic has magic numbers - what are these values tuned for?"
- "Is this in-memory session storage intentional for single-instance deployment?"
- "These three tax calculators look similar - which is canonical?"

## Usage

```
/interview                  # Start fresh interview
/interview --context        # Use code analysis for targeted questions
/interview --resume         # Resume previous interview
/interview --export         # Export interview results
```

## Interview Flow

1. **Context Setting** - Your role and experience with codebase
2. **Confusion Points** - Questions about unclear code flagged by detector
3. **Scalability Concerns** - Clarify intentional patterns vs issues to fix
4. **Architecture** - Key decisions and trade-offs
5. **Gotchas** - Things that have burned you before
6. **Tribal Knowledge** - Unwritten rules and conventions  
7. **Pain Points** - Frustrations and wishlist items

## Question Categories

Based on confusion-detector output:

| Category | Example Question |
|----------|-----------------|
| **WHAT** | "This regex in `validateInput` is complex - what does it actually validate?" |
| **WHEN** | "What's the required order between `reserveInventory` and `chargePayment`?" |
| **HISTORY** | "Why is `TaxCalculatorV2` separate from the main calculator?" |
| **DUPLICATE** | "These three fee calculation functions look similar - which should new code use?" |

Based on scalability-detector output:

| Pattern | Example Question |
|---------|-----------------|
| `in_memory_state` | "Is this ConcurrentHashMap cache intentional? How do you handle pod restarts?" |
| `missing_retry` | "Is retry handled elsewhere, or should we add it here?" |
| `hardcoded_limit` | "How was this batch size of 50 determined?" |

## Tips for Good Interviews

- Take your time - elaborate on interesting points
- Mention specific files when possible
- Share the "why" behind decisions
- Don't worry about being too detailed
- If a flagged issue is intentional, explain why

## Output

Produces `interview_results.json`:

```json
{
  "interviewee": {
    "name": "Jane Smith",
    "role": "Senior Backend Engineer",
    "tenure": "3 years",
    "areas": ["payments", "compliance"]
  },
  "knowledge": [
    {
      "file": "PaymentRetryService.kt",
      "function": "retryWithBackoff",
      "type": "gotcha",
      "content": "Retry intervals tuned for Stripe rate limits",
      "importance": "high"
    }
  ],
  "resolved_confusion": [
    {
      "confusion_id": "cp-001",
      "resolution": "Magic numbers are Stripe-specific rate limit timings",
      "should_document": true
    }
  ],
  "resolved_scalability": [
    {
      "issue_id": "scale-002",
      "resolution": "In-memory cache intentional - single pod deployment",
      "is_intentional": true
    }
  ]
}
```

Captured knowledge is:
- Merged into CLAUDE.md via `/capture`
- Exported as JSON for other tools
- Used to generate targeted documentation
