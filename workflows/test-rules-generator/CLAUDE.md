# Test Rules Generator Workflow

You are running a test rules generation workflow on the Ambient Code Platform.

## Hard Rules

- NEVER modify existing test files in the repository
- NEVER invent patterns that don't exist in the codebase -- every rule MUST reference real files
- ALWAYS write output to `artifacts/test-rules-generator/`
- ALWAYS include real code examples extracted from existing tests, not synthetic examples
- Minimum 5 code examples per rule file

## Purpose

Analyze a repository's existing test patterns and generate `.claude/rules/` files that enable AI agents to create tests following the repository's actual conventions.

## Analysis Process

### Step 1: Test Discovery

Scan the repository to identify all test types present:

1. **Unit tests**: `*_test.go`, `*.spec.ts`, `*.test.ts`, `test_*.py`, `*_test.py`
2. **Integration tests**: Files in `integration/`, `test/integration/`
3. **E2E tests**: Files in `e2e/`, `test/e2e/`, `cypress/`, `playwright/`
4. **Mock tests**: Files using mock frameworks (mockgen, jest.mock, unittest.mock)
5. **Contract tests**: Pact files, contract test directories
6. **Container tests**: Testcontainers usage, Docker-based test setups
7. **API tests**: HTTP test utilities, supertest, httptest

### Step 2: Pattern Extraction

For each test type found, extract:

1. **File naming convention**: How are test files named relative to source files?
2. **Directory structure**: Where do tests live? Co-located or separate directory?
3. **Import patterns**: What test libraries/frameworks are imported?
4. **Setup/teardown**: How are test fixtures created? beforeEach/AfterSuite/conftest.py?
5. **Mocking strategy**: What's mocked? How are mocks created and injected?
6. **Assertion style**: Which assertion library? What patterns are used?
7. **Test data**: How is test data managed? Factories, fixtures, inline?
8. **Helper functions**: Common test utilities and where they live
9. **Naming conventions**: How are test functions/describe blocks named?
10. **Coverage patterns**: How is coverage measured and reported?

### Step 3: Generate Rule Files

Create one rule file per test type detected. Each rule file MUST contain:

1. **Overview**: What this test type covers and when to use it
2. **File location and naming**: Where to put the test, how to name it
3. **Boilerplate template**: Standard imports and setup
4. **Patterns with examples**: 5+ real examples extracted from the codebase
5. **Anti-patterns**: What NOT to do (based on observed conventions)
6. **Checklist**: Pre-submission quality checks

### Step 4: Generate Summary

Create an index file that maps test types to rule files and identifies any gaps.

## Output

Write all artifacts to `artifacts/test-rules-generator/`:

```
artifacts/test-rules-generator/
  analysis.md                    # Test pattern analysis summary
  rules/
    unit-tests.md                # Unit test creation rules
    integration-tests.md         # Integration test rules (if applicable)
    e2e-tests.md                 # E2E test rules (if applicable)
    mock-tests.md                # Mock/stub rules (if applicable)
    contract-tests.md            # Contract test rules (if applicable)
    container-tests.md           # Container test rules (if applicable)
  README.md                      # Index of all generated rules
```

## Rule File Template

Each generated rule file MUST follow this structure:

```markdown
# [Test Type] Test Creation Rules

## Overview
[When to write this type of test, what it validates]

## File Location and Naming
- Test files go in: [path pattern]
- Naming convention: [pattern]
- Example: `src/components/Button.tsx` -> `src/components/__tests__/Button.spec.tsx`

## Required Imports
[Standard import block with framework-specific imports]

## Patterns

### Pattern 1: [Name]
[Description of when to use this pattern]

**Example from codebase** (`[file path]`):
[Actual code extracted from the repository]

### Pattern 2: [Name]
[Continue for each pattern found...]

## Test Data Management
[How this repo handles test data, fixtures, factories]

## Mocking Strategy
[How and what to mock, with examples]

## Anti-Patterns
- DO NOT: [thing to avoid, with reason]
- DO NOT: [thing to avoid, with reason]

## Pre-Submission Checklist
- [ ] Test file follows naming convention
- [ ] All imports match repository patterns
- [ ] Mocking follows established strategy
- [ ] Test data uses existing fixtures/factories
- [ ] Test names are descriptive and follow convention
```

## Quality Standards

Generated rules MUST:
- Reference only files that actually exist in the repository
- Include code examples copied verbatim from existing tests (with file path attribution)
- Be specific to the repository's frameworks, not generic testing advice
- Cover edge cases and error handling patterns observed in the codebase
- Include the repository's specific assertion style (not mix styles)
