# PR Build Validation Generator Workflow

You are running a build validation workflow on the Ambient Code Platform.

## Hard Rules

- NEVER push directly to the target repository's main/master branch
- ALWAYS write generated workflows to `artifacts/pr-build-validation/`
- ALWAYS include clear comments in generated YAML explaining each step
- NEVER hardcode secrets or credentials in generated workflows
- Generated workflows MUST use `pull_request` trigger, not `push`

## Purpose

Analyze a repository's build configuration and generate GitHub Actions workflows that catch build failures at PR time instead of post-merge in production CI systems (e.g., Konflux).

## Analysis Process

### Step 1: Build Configuration Discovery

Scan the repository for:

1. **Dockerfiles**: `Dockerfile`, `Containerfile`, `*.Dockerfile`, and their locations
2. **Build arguments**: `ARG` directives, especially `BUILD_MODE`, environment-specific flags
3. **Multi-stage builds**: Builder stages, runtime stages, COPY --from directives
4. **Compose files**: `docker-compose.yml`, `compose.yaml`
5. **Kustomize**: `kustomization.yaml` files, overlays directory structure
6. **Operator manifests**: CRDs, RBAC, webhook configs in `manifests/`, `config/`, `bundle/`
7. **Module Federation**: Webpack federation plugin configs, remote entry points
8. **Makefile targets**: `build`, `docker-build`, `manifests`, `generate` targets

### Step 2: Risk Assessment

Identify what can break post-merge:
- Docker builds with environment-specific configuration
- Multi-stage builds where COPY paths depend on build output
- Kustomize overlays that aren't validated
- Operator manifests that aren't built/tested
- Module Federation remotes that aren't validated
- Multi-arch builds (amd64, arm64, etc.)

### Step 3: Generate Validation Workflow

Create `.github/workflows/pr-build-validation.yml` with applicable phases:

**Phase 1: Docker Build Validation**
- Build with production configuration (e.g., BUILD_MODE=RHOAI)
- Validate multi-stage build completes
- Check image size is reasonable

**Phase 2: Manifest Validation** (if Kustomize/operator detected)
- Run `kustomize build` on all overlays
- Validate CRDs with `kubectl apply --dry-run`
- Check RBAC manifests

**Phase 3: Module Federation Validation** (if applicable)
- Validate all remote entry points exist
- Check federation ConfigMap generation

**Phase 4: Integration Smoke Test** (if Kind/Minikube patterns detected)
- Create Kind cluster
- Apply manifests
- Verify pods start successfully
- Basic health check

**Phase 5: Multi-arch Validation** (if multi-arch detected)
- Validate cross-platform build with `docker buildx`

### Step 4: Generate Local Validation Script

Create `scripts/validate-build.sh` for developer use:
- Mirrors the CI workflow steps locally
- Runs without requiring a cluster (where possible)
- Provides clear pass/fail output

## Output

Write all artifacts to `artifacts/pr-build-validation/`:

```
artifacts/pr-build-validation/
  build-analysis.md              # What was detected and why each phase was included
  pr-build-validation.yml        # The generated GitHub Actions workflow
  validate-build.sh              # Local validation script
```

## Generated Workflow Template

The generated workflow MUST follow this structure:

```yaml
name: PR Build Validation
on:
  pull_request:
    branches: [main, master]
    # Only trigger on files that affect the build
    paths:
      - 'Dockerfile*'
      - '**/*.go'        # or language-appropriate patterns
      - 'Makefile'
      - 'manifests/**'

concurrency:
  group: pr-build-${{ github.event.pull_request.number }}
  cancel-in-progress: true

jobs:
  # Phase 1-5 as applicable
```

## Workflow Quality Standards

Generated workflows MUST:
- Use `concurrency` to cancel superseded runs
- Use `paths` filters to avoid unnecessary runs
- Cache build layers where possible (Docker layer cache, Go module cache)
- Set reasonable timeouts (15-30 minutes max)
- Use pinned action versions (sha, not @latest)
- Include clear job/step names
- Report results as PR status checks
