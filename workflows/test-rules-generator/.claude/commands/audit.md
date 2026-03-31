# Audit Existing Test Rules

Evaluate existing .claude/rules/ files in this repository for completeness and accuracy.

## Process

1. Read all files in `.claude/rules/`
2. Scan the repository for all test types present
3. Check: does every test type have a corresponding rule file?
4. Check: do the rules reference actual files and patterns that still exist?
5. Check: are the rules actionable (examples, checklists, framework-specific)?
6. Report gaps, outdated references, and improvement suggestions
7. Write findings to `artifacts/test-rules-generator/audit-report.md`

Begin the audit now.
