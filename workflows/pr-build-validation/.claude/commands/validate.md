# Validate Existing Build Workflow

Check an existing PR build validation workflow for completeness against this repository's build configuration.

## Process

1. Find existing CI workflows in `.github/workflows/`
2. Scan the repository's build configuration (Dockerfiles, Kustomize, manifests, etc.)
3. Compare what the CI validates vs what could break
4. Report gaps where build failures could slip through to post-merge
5. Write findings to `artifacts/pr-build-validation/validation-audit.md`

Begin the audit now.
