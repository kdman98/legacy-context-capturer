# Context Capturer

A Claude Code plugin that captures tribal knowledge from legacy codebases and developers, generating structured context files for AI coding assistants.

## The Problem

Every codebase has tribal knowledge - the undocumented gotchas, historical decisions, and "don't touch this" warnings that live only in developers' heads. When people leave or codebases grow, this knowledge is lost.

AI coding assistants like Claude work better with context, but creating and maintaining context files (CLAUDE.md, .cursor/rules) is tedious and often neglected.

## The Solution

Context Capturer uses multiple specialized agents to:

1. **Analyze** your codebase structure automatically
2. **Detect** confusing code that needs explanation
3. **Detect** scalability issues and anti-patterns
4. **Interview** developers to extract tribal knowledge
5. **Generate** structured context files

## Installation

```bash
# Add the marketplace
/plugin marketplace add 93/context-capturer-marketplace

# Install the plugin
/plugin install context-capturer
```

## Usage

### Full Pipeline
```bash
/capture
```
Runs all agents in sequence:
1. Code analysis → `structure.json`
2. Confusion detection → `confusion_points.json`
3. Scalability detection → `scalability_report.json`
4. Developer interview (interactive)
5. Context file generation

### Individual Commands
```bash
/analyze          # Just analyze codebase structure
/detect-scale     # Just check for scalability issues
/interview        # Just run the interview
```

## Agents

| Agent | Purpose | Output |
|-------|---------|--------|
| **code-analyzer** | Scans repo structure, identifies services and modules | `structure.json` |
| **confusion-detector** | Finds code that needs human explanation (WHAT, WHEN, HISTORY, DUPLICATE) | `confusion_points.json` |
| **scalability-detector** | Finds N+1 queries, missing circuit breakers, cost issues | `scalability_report.json` |
| **interview-agent** | Extracts tribal knowledge through structured questions | `interview_results.json` |
| **context-writer** | Synthesizes everything into CLAUDE.md | `CLAUDE.md` |

## Data Flow

```
code-analyzer
     │
     ▼
structure.json
     │
     ├──────────────────┐
     ▼                  ▼
confusion-detector   scalability-detector
     │                  │
     ▼                  ▼
confusion_points.json  scalability_report.json
     │                  │
     └────────┬─────────┘
              ▼
       interview-agent
              │
              ▼
       context-writer
              │
              ▼
         CLAUDE.md
```

## Output Files

### CLAUDE.md
Main context file with:
- Project overview and tech stack
- Service descriptions
- Gotchas and warnings
- Scalability issues
- Historical decisions
- Ownership map

### .cursor/rules (optional)
Cursor-specific rules for:
- Code style
- Patterns to follow/avoid
- Pre-modification checks

### .context/context.json (optional)
Machine-readable format for programmatic access.

## Example Output

```markdown
# Project Context: payment-service

## Overview
Payment retry service for P2P transactions. Handles failed payments
with exponential backoff and compliance-aware retry logic.

## Tech Stack
- Kotlin 1.9 / Spring Boot 3.2
- PostgreSQL 15 + Redis 7
- Runs on EKS (3 pods, auto-scaling)

## Critical Knowledge

### ⚠️ PaymentRetryService.kt::retryWithBackoff
Retry intervals are tuned for Stripe's rate limits.
**Do not modify without checking with @jane.**

### 🔴 FeeCalculator.kt::calculateKoreanTax
Hardcoded tax logic for KR market. Legacy code from acquisition.
**Owner**: @john (only person who understands it)

### ⚡ Scalability: OrderService.kt::getOrdersWithUsers
N+1 query pattern - loads users one-by-one.
**Impact**: 1000 orders = 1001 queries
**Fix**: Use batch fetch with findAllById()
```

## Contributing

1. Fork the repository
2. Create your feature branch
3. Submit a pull request

## License

MIT
