# Extract Test Patterns and Generate Rules

Analyze this repository's existing tests and generate .claude/rules/ files.

## Process

1. Discover all test types present in the repository
2. For each test type, extract patterns: naming, structure, imports, fixtures, mocking, assertions
3. Collect 5+ real code examples per test type from existing test files
4. Generate one rule file per test type with actionable guidance and real examples
5. Generate an analysis summary and index

## Output

Write all files to `artifacts/test-rules-generator/`:
- `analysis.md` -- Test pattern analysis summary
- `rules/*.md` -- One rule file per test type detected
- `README.md` -- Index mapping test types to rule files

Begin the extraction now.
