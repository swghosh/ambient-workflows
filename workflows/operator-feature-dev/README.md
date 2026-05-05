# Operator Feature Dev Workflow

Multi-PR OpenShift operator feature development from Enhancement Proposals. Takes an EP URL and generates a complete implementation across 3 Pull Requests.

## What It Does

Given an Enhancement Proposal PR URL and an operator repository URL, this workflow generates:

1. **PR #1 — API Type Definitions**: Go type definitions with markers, validation, godoc, FeatureGate registration, and `.testsuite.yaml` integration tests
2. **PR #2 — Controller Implementation**: Complete controller/reconciler code with reconciliation logic, dependent resource management, RBAC markers, and status handling
3. **PR #3 — E2E Tests**: End-to-end test artifacts (test cases, execution steps, and Ginkgo/bash test code) based on git diff

## Directory Structure

```text
workflows/operator-feature-dev/
├── .ambient/
│   └── ambient.json                    # Workflow configuration
├── .claude/
│   ├── commands/                       # Atomic, argument-driven operations
│   │   ├── ofd.init.md                 # Clone and validate operator repo
│   │   ├── ofd.api-generate.md         # Generate API types from EP
│   │   ├── ofd.api-generate-tests.md   # Generate integration tests
│   │   ├── ofd.api-implement.md        # Generate controller/reconciler
│   │   ├── ofd.e2e-generate.md         # Generate E2E test artifacts
│   │   ├── ofd.review.md              # Code review with auto-fix
│   │   ├── ofd.pr.md                  # Create pull request
│   │   └── ofd.speedrun.md            # Autonomous full pipeline
│   └── skills/                         # Orchestration + cross-cutting
│       ├── controller/SKILL.md         # Phase transition orchestrator
│       ├── effective-go/SKILL.md       # Go best practices
│       └── summary/SKILL.md           # Final synthesis
├── CLAUDE.md                           # Hard limits and safety rules
└── README.md
```

## Quick Start

1. Start the workflow in ACP
2. Provide an Enhancement Proposal PR URL and operator repo URL
3. The controller guides you through each phase, or use `/ofd.speedrun` for autonomous execution

### Example Session

```text
User: I want to implement EP https://github.com/openshift/enhancements/pull/1234
      in https://github.com/openshift/cert-manager-operator, base branch main

Atlas: [runs /ofd.init, then guides through each phase]
```

### Speedrun (Autonomous)

```text
/ofd.speedrun https://github.com/openshift/enhancements/pull/1234 https://github.com/openshift/cert-manager-operator main
```

## Commands

| Command | Arguments | Purpose |
| --- | --- | --- |
| `/ofd.init` | `<repo-url> <base-branch>` | Clone repo, detect framework |
| `/ofd.api-generate` | `<ep-url> [--design-doc <gist-url>]` | Generate API type definitions |
| `/ofd.api-generate-tests` | `<path-to-types>` | Generate integration tests |
| `/ofd.api-implement` | `<ep-url> [--design-doc <gist-url>]` | Generate controller/reconciler |
| `/ofd.e2e-generate` | `<base-branch>` | Generate E2E test artifacts |
| `/ofd.review` | `<base-branch>` | Code review with auto-fix |
| `/ofd.pr` | `<base-branch>` | Create draft pull request |
| `/ofd.speedrun` | `<ep-url> <repo-url> <base-branch>` | Run all phases autonomously |

## Workflow Phases

```text
PR #1: /ofd.init → /ofd.api-generate → /ofd.api-generate-tests → /ofd.review → /ofd.pr
PR #2: /ofd.api-implement → /ofd.review → /ofd.pr
PR #3: /ofd.e2e-generate → /ofd.review → /ofd.pr
Final: summary skill
```

## Artifacts

All artifacts are written to `artifacts/operator-feature-dev/`:

```text
artifacts/operator-feature-dev/
├── init-summary.md
├── api/
│   ├── generation-summary.md
│   ├── test-generation-summary.md
│   ├── review-verdict.md
│   └── pr-description.md
├── impl/
│   ├── implementation-summary.md
│   ├── review-verdict.md
│   └── pr-description.md
├── e2e/
│   ├── test-cases.md
│   ├── execution-steps.md
│   ├── e2e-suggestions.md
│   ├── generation-summary.md
│   ├── review-verdict.md
│   └── pr-description.md
└── summary.md
```

## Prerequisites

- `git` — Git installed
- `go` — Go toolchain installed
- `gh` — GitHub CLI installed and authenticated
- Access to `openshift/enhancements` repository
- Target operator repository must be a Go-based OpenShift operator

## Supported Frameworks

- **controller-runtime** (kubebuilder/operator-sdk) — most common
- **library-go** (OpenShift core operators) — uses SyncFunc pattern

## Testing

Use the ACP "Custom Workflow" feature to test without merging:

| Field | Value |
| --- | --- |
| **URL** | Your fork's git URL |
| **Branch** | Your branch name |
| **Path** | `workflows/operator-feature-dev` |
