---
name: capture
description: Run full context capture pipeline - analyzes code, detects confusion and scalability issues, conducts interview, and generates context files
---

# Context Capture - Full Pipeline

Run the complete context capture workflow to generate CLAUDE.md and other context files.

## Pipeline Steps

Execute these agents in sequence, passing outputs forward:

### Step 1: Code Analysis
Invoke @code-analyzer on the current repository.
- Scan entire codebase structure
- Identify all services and modules
- Map dependencies and patterns
- Output: `structure.json`

### Step 2: Confusion Detection
Invoke @confusion-detector with `structure.json`.
- Identify code that needs human explanation
- Categorize by type: WHAT, WHEN, HISTORY, DUPLICATE
- Generate suggested interview questions
- Output: `confusion_points.json`

### Step 3: Scalability Detection  
Invoke @scalability-detector with `structure.json`.
- Check for performance anti-patterns
- Flag load, concurrency, scaling, and cost issues
- Identify issues needing developer clarification
- Output: `scalability_report.json`

### Step 4: Developer Interview
Invoke @interview-agent with context from Steps 1-3.
- Use confusion points to ask targeted questions about unclear code
- Use scalability issues to clarify intentional vs accidental patterns
- Capture tribal knowledge and gotchas
- Record ownership information
- Allow interactive Q&A with the developer
- Output: `interview_results.json`

### Step 5: Context Generation
Invoke @context-writer with all previous outputs.
- Synthesize all gathered information
- Generate CLAUDE.md in project root
- Generate .cursor/rules if requested
- Create context.json for programmatic access

## Data Flow

```
structure.json
     │
     ├──────────────────┐
     ▼                  ▼
confusion_points.json  scalability_report.json
     │                  │
     └────────┬─────────┘
              ▼
     interview_results.json
              │
              ▼
         CLAUDE.md
```

## Usage

```
/capture                    # Full pipeline with interview
/capture --skip-interview   # Skip interview, use code analysis only
/capture --output ./docs    # Custom output directory
/capture --format all       # Generate all output formats
```

## Output

Files created:
- `CLAUDE.md` - Main context file for AI assistants
- `.cursor/rules` - Cursor-specific rules (optional)
- `.context/context.json` - Machine-readable context (optional)

Intermediate files (in `.context/`):
- `structure.json` - Codebase structure from code-analyzer
- `confusion_points.json` - Confusion points from confusion-detector
- `scalability_report.json` - Scalability issues from scalability-detector
- `interview_results.json` - Interview results from interview-agent

## Notes

- Interview step is interactive - developer input required
- Estimated time: 10-20 minutes depending on codebase size
- Re-run periodically as codebase evolves
- Use `--skip-interview` for quick automated scans
