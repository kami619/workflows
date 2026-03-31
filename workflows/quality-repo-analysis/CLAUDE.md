# Quality Repo Analysis Workflow

You are running a quality assessment workflow on the Ambient Code Platform.

## Hard Rules

- NEVER modify source code in the target repository
- NEVER push commits or create PRs from this workflow
- ALWAYS back findings with specific file paths
- ALWAYS write output to `artifacts/quality-repo-analysis/`
- If a dimension is not applicable (e.g., no containers), score it as N/A, do not score 0

## Quality Framework (7 Dimensions)

Score each dimension 0-10. Overall score is a weighted average:

| Dimension | Weight | What to Check |
|-----------|--------|---------------|
| Unit Tests | 15% | Test files, frameworks, test-to-code ratio, coverage generation |
| Integration/E2E | 20% | E2E directories, frameworks (Cypress/Playwright/envtest/Kind), PR vs periodic triggers |
| Build Integration | 25% | PR-time Docker builds, BUILD_MODE validation, manifest validation, multi-arch |
| Image Testing | 15% | Container testing (Testcontainers), vulnerability scanning (Trivy/Snyk), SBOM, multi-arch |
| Coverage Tracking | 10% | Codecov/coveralls, threshold enforcement, PR coverage reporting |
| CI/CD Automation | 10% | Workflow organization, concurrency control, caching, build automation |
| Agent Rules | 5% | .claude/rules/ presence, completeness, actionability, framework-specific guidance |

## Scoring Criteria

- **10**: Gold standard, exceeds expectations
- **8-9**: Strong practices, minor gaps
- **6-7**: Adequate, moderate improvements needed
- **4-5**: Weak, significant gaps
- **0-3**: Critical gaps, major work required

## Analysis Process

### Step 1: Repository Discovery
1. Identify repository type (operator, web app, CLI, library)
2. Detect primary language(s)
3. Identify framework (Kubernetes operator, React app, etc.)

### Step 2: CI/CD Analysis
Examine `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`:
- List all workflows and their triggers (PR vs periodic vs manual)
- Check concurrency control and caching strategies
- Identify what tests run on PRs vs post-merge

### Step 3: Test Coverage Analysis
- Count test files by pattern (`*_test.go`, `*.spec.ts`, `test_*.py`)
- Calculate test-to-code ratio
- Identify testing frameworks
- Check for integration/E2E test directories and infrastructure

### Step 4: Code Quality Assessment
- Check linting configs (`.golangci.yaml`, `.eslintrc`, `ruff.toml`)
- Check `.pre-commit-config.yaml`
- Check SAST tools (CodeQL, gosec, Semgrep)

### Step 5: Build Integration Analysis (CRITICAL)
- Does the PR workflow build Docker images?
- Are multi-stage builds validated?
- Are Kustomize overlays / operator manifests validated on PRs?
- Is there environment-specific build validation (BUILD_MODE)?

### Step 6: Container Image Testing
- Dockerfile/Containerfile analysis
- Runtime validation (Testcontainers, Kind deployment)
- Security scanning (Trivy, Snyk) with thresholds
- SBOM generation, image signing

### Step 7: Agent Rules Analysis
- Check for CLAUDE.md, AGENTS.md, `.claude/rules/`
- Evaluate rule completeness (all test types covered?)
- Assess rule quality (examples, checklists, framework-specific?)

### Step 8: Generate Report
Write the report to `artifacts/quality-repo-analysis/quality-report.md`.

## Output Format

The report MUST follow this structure:

```
# Quality Analysis: [Repository Name]

## Executive Summary
- Overall Score: X/10
- Key Strengths: ...
- Critical Gaps: ...

## Quality Scorecard
| Dimension | Score | Weight | Status | Notes |
|-----------|-------|--------|--------|-------|
| Unit Tests | X/10 | 15% | ... | ... |
| Integration/E2E | X/10 | 20% | ... | ... |
| Build Integration | X/10 | 25% | ... | ... |
| Image Testing | X/10 | 15% | ... | ... |
| Coverage Tracking | X/10 | 10% | ... | ... |
| CI/CD Automation | X/10 | 10% | ... | ... |
| Agent Rules | X/10 | 5% | ... | ... |
| **Overall** | **X/10** | | | |

## Critical Gaps
[Numbered list with impact, severity, and estimated effort]

## Quick Wins
[Low-effort, high-impact improvements with implementation hints]

## Detailed Findings
[Per-dimension analysis with file paths and evidence]

## Recommendations
### Priority 0 (Critical)
### Priority 1 (High Value)
### Priority 2 (Nice-to-Have)

## Key Files Analyzed
[List of configuration files examined]
```

## Gold Standards for Comparison

When assessing, compare against these benchmarks:
- **odh-dashboard**: 4-layer testing (unit, mock, E2E, contract), 16 agent rule files, codecov enforced at 70%
- **notebooks**: 5-layer image validation (Testcontainers, Makefile, K8s, Playwright, Trivy), multi-arch support
- **kserve**: 80% Go coverage enforced, Minikube E2E, multi-network testing

## Special Considerations

### Kubernetes Operators
Check for: envtest usage, CRD testing, webhook validation, RBAC coverage, multi-namespace testing

### Web Applications
Check for: frontend coverage, API contract tests, BFF tests, accessibility testing

### CLI Tools
Check for: command testing, cross-platform testing, installation testing
