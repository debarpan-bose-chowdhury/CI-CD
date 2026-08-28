# CI/CD Template Implementation Plan

## Objective
Implement a reusable GitHub Actions CI/CD template repo for same-org consumers with auto-detected .NET/Python support, container packaging, staged security gates, release automation, and periodic image re-scanning.

## Scope assumptions
- Reusable workflows only (`workflow_call`).
- Registry default: GHCR.
- DAST gate: block on medium and above findings.
- Coverage gate: 85% minimum.
- Production deploy: automated after approval.
- Periodic image scans create tracking issues; they do not block release by default.

## Phase 1: Foundation
1. Create reusable workflow skeletons.
2. Add stack auto-detection.
3. Define shared inputs, outputs, and secrets.
4. Establish naming and versioning conventions.

Deliverables:
- `ci.yml`
- `docker-build-push.yml`
- reusable detection helpers
- workflow inputs contract

## Phase 2: Build and quality gates
1. Implement build and test execution for .NET and Python.
2. Add coverage collection.
3. Add SonarQube gate.
4. Add lint/format checks.
5. Add SCA and SBOM generation.

Deliverables:
- gated CI workflow
- published artifacts and reports
- fail-fast behavior on mandatory checks

## Phase 3: Container packaging
1. Add reusable Dockerfile templates.
2. Implement buildx-based image build/push.
3. Tag images with release version and `latest`.
4. Publish SBOM/provenance with image output.

Deliverables:
- `docker/Dockerfile.dotnet`
- `docker/Dockerfile.python`
- reusable container publish workflow
- image digest outputs

## Phase 4: Staging and DAST
1. Deploy candidate image to staging.
2. Run OWASP ZAP DAST.
3. Run curl/Postman smoke checks.
4. Block promotion on failed gate.

Deliverables:
- staging deploy workflow
- DAST/smoke workflow
- promotion gate enforcement

## Phase 5: Release automation
1. Read merged PR conventional commit intent.
2. Compute version bump from current version.
3. Open automated version-bump PR.
4. Create tag only after version PR merges.

Deliverables:
- version bump workflow
- release tag workflow
- release notes generation

## Phase 6: Periodic image scanning
1. Add scheduled image scan workflow.
2. Scan latest and recent release tags.
3. Generate SARIF/report output.
4. Open/update issues for findings.

Deliverables:
- `image-periodic-scan.yml`
- issue/report automation

## Phase 7: Monitoring and rollback
1. Add continuous monitoring workflow.
2. Track runtime security signals.
3. Add rollback workflow for last-known-good digest.

Deliverables:
- monitoring workflow
- rollback workflow

## Phase 8: Documentation and adoption
1. Add consumer repo usage examples.
2. Document required repo config.
3. Document secrets, environments, and branch protection.

Deliverables:
- usage docs
- consumer config checklist
- dependency map

## Success criteria
- Consumer repos can call workflows without copying pipeline logic.
- .NET and Python are auto-detected correctly.
- Mandatory gates block unsafe promotion.
- Release tagging happens only after version-bump PR merge.
- Periodic image scans run independently and create actionable tracking issues.
