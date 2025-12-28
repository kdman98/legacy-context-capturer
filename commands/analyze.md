---
name: analyze
description: Run code analysis only - scans repository structure and identifies services
---

# Code Analysis

Run the code analyzer agent standalone to understand codebase structure.

## What It Does

- Scans repository folder structure
- Identifies services, modules, and components
- Maps dependencies (internal and external)
- Detects architectural patterns
- Lists key configuration files

## Usage

```
/analyze                    # Analyze current directory
/analyze ./src              # Analyze specific path
/analyze --depth 3          # Limit folder depth
/analyze --output json      # Output as JSON
```

## Output

Produces a structured analysis including:
- Project type (monolith/microservices/monorepo)
- Language and framework detection
- Service inventory with purposes
- Dependency map
- Pattern observations

## When to Use

- Quick codebase orientation
- Before diving into specific areas
- To generate input for other tools
- As part of the full `/capture` pipeline
