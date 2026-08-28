# CI/CD Template Dependency Map

## Main delivery chain
```mermaid
flowchart TD
  A[Consumer repo PR to main] --> B[CI build/test/coverage]
  B --> C[SCA + SBOM]
  C --> D[SonarQube + lint + CodeQL]
  D --> E[Package & containerize]
  E --> F[Push image + SBOM]
  F --> G[Deploy to staging]
  G --> H[DAST + smoke]
  H --> I[Approve and merge]
  I --> J[Deploy to production]
  J --> K[Continuous monitoring]
```

## Release automation chain
```mermaid
flowchart TD
  M[Manual PR merged to main] --> N[Read conventional commit intent]
  N --> O[Compute version bump]
  O --> P[Create version-bump branch and PR]
  P --> Q[Version-bump PR merged]
  Q --> R[Create release tag]
  R --> S[Publish release notes]
```

## Periodic image scan chain
```mermaid
flowchart TD
  U[Scheduled trigger] --> V[Pull latest/recent image tags]
  V --> W[Scan runtime image and SBOM]
  W --> X{Findings?}
  X -->|No| Y[Record clean result]
  X -->|Yes| Z[Open/update tracking issue]
  Z --> AA[Optional SARIF/report export]
```

## Dependency rules
- CI gates must pass before container publish.
- Container publish must complete before staging deploy.
- Staging deploy must complete before DAST/smoke.
- DAST/smoke must pass before production deploy.
- Version bump PR merge must happen before release tag creation.
- Release tag must exist before release notes publication.
- Periodic image scans depend on pushed images and SBOM artifacts.
- Monitoring depends on deployed production image and stable health endpoints.

## Cross-cutting dependencies
- SonarQube requires code checkout, build output, and token access.
- SCA/SBOM requires dependency restore or container metadata.
- DAST requires a reachable staging URL and test credentials if protected.
- Registry push requires registry credentials and image naming rules.
- Production deploy requires environment approval and protected secrets.
