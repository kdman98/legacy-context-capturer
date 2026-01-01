---
name: interview
description: Start interactive interview to capture tribal knowledge from developers
---

# Developer Interview

Start an interactive interview session to capture tribal knowledge, gotchas, and historical context from developers who know the codebase.

## What It Does

- Asks structured questions about the codebase
- Captures undocumented knowledge and gotchas
- Records ownership and expertise areas
- Documents historical decisions
- Identifies pain points and technical debt
- **Saves progress** for long interviews (resume support)

## Usage

```
/interview                  # Start fresh interview
/interview --context        # Use code analysis for targeted questions
/interview --resume         # Resume previous interview
/interview --export         # Export interview results
/interview --scoped         # Focus on scoped area (if scope.json exists)
```

## Interview Flow

1. **Context Setting** - Your role and experience with codebase
2. **Architecture** - Key decisions and trade-offs
3. **Gotchas** - Things that have burned you before
4. **Tribal Knowledge** - Unwritten rules and conventions  
5. **Pain Points** - Frustrations and wishlist items

## Progress Tracking

For longer interviews (10+ questions), the agent:
- **Captures progressively** — saves knowledge after each answer
- **Shows progress** — "We've covered 5 topics, 3 remaining..."
- **Checkpoints** — saves state every 5 questions
- **Supports resume** — pick up where you left off

```
Agent: "We've made good progress. So far I've captured:
        - Retry logic constraints
        - Cache scaling considerations  
        - Fee calculator history
        
        I have 4 more questions. Continue or take a break?"
```

## Resume Support

If an interview was interrupted:

```
/interview --resume

Agent: "Resuming from yesterday. We covered:
        - PaymentService retry logic
        - FeeCalculator V1/V2 split
        
        3 topics remaining. Ready to continue?"
```

## Tips for Good Interviews

- Take your time - elaborate on interesting points
- Mention specific files when possible
- Share the "why" behind decisions
- Don't worry about being too detailed
- It's OK to take breaks - progress is saved

## Output

Captured knowledge is stored in:
- `interview_knowledge.json` — all captured insights
- `interview_state.json` — progress for resume

And can be:
- Merged into CLAUDE.md via `/capture`
- Exported as JSON for other tools
- Used to generate targeted documentation
