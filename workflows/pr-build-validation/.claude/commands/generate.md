# Generate PR Build Validation

Analyze this repository's build configuration and generate a PR-time build validation workflow.

## Process

1. Scan for Dockerfiles, Kustomize configs, operator manifests, Module Federation configs, and Makefiles
2. Identify build arguments, multi-stage builds, environment-specific configurations
3. Assess what can break post-merge that isn't validated at PR time
4. Generate a GitHub Actions workflow with applicable validation phases
5. Generate a local validation script for developer use
6. Write a build analysis explaining what was detected and why each phase was included

## Output

Write all files to `artifacts/pr-build-validation/`:
- `build-analysis.md` -- Detection results and rationale
- `pr-build-validation.yml` -- The generated GitHub Actions workflow
- `validate-build.sh` -- Local validation script

Begin the analysis now.
