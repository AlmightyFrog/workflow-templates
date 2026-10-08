# workflow-templates
Reusable GitHub Actions: multi-arch Docker build/push + GHCR pruning.
Call from any repo, configured via `build.env` in its root:

```yaml
jobs:
  build:
    uses: <owner>/workflow-templates/.github/workflows/docker-build.yml@v1
  cleanup:
    uses: <owner>/workflow-templates/.github/workflows/docker-cleanup.yml@v1
```

Pin a release tag (`@v1`), not `@main`. Callers need `packages: write`.

| key              | default       | meaning                |
|------------------|---------------|------------------------|
| `IMAGE`          | required      | image name (no prefix) |
| `CONTEXT`        | `.`           | build context          |
| `PLATFORMS`      | `linux/amd64` | build platforms        |
| `RETENTION_DAYS` | `30`          | `pr-*` retention       |


## docker-build.yml
Pushes `ghcr.io/<owner>/<IMAGE>`:

- default branch: `latest` + `YYYY-MM-DD`
- other branches: `<branch>` + `<branch>-YYYY-MM-DD`
- PRs: `pr-<n>` + `pr-<n>-<sha7>`
- multi-arch -> one manifest per tag, no per-arch suffixes
- `concurrency` cancels superseded runs per ref


## docker-cleanup.yml
Prunes package versions — thinned, never emptied:

- `pr-*` older than `RETENTION_DAYS` deleted
- current month all kept; older months -> first per month; older years -> first per year
- moving tags (`latest`, branches) and untagged versions untouched


## AI use
Written primarily with AI coding agents (OpenCode and GitHub Copilot),
a human reviewed and owns whatever ships here.
