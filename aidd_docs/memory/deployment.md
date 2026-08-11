# Deployment

Where the project runs and how it ships: CI/CD, environments, and release.

## Pipeline

Two independent GitHub Actions workflows, each with jobs for the two artifact kinds (build matrices kept in sync by hand when a template or kit is added):

- `ci.yml`: `build` builds every `templates/*` entry, multi-arch (`linux/amd64,linux/arm64`), never pushing; `validate-kits` runs `sbx kit validate kits/<name>` per kit. Both run on push, PR, or manual `workflow_dispatch`.
- `cd.yml`: `build` rebuilds and pushes template images to GHCR; `publish-kits` pushes each kit as an OCI artifact (`sbx kit push`) to GHCR. Both run on push to `main`, or manual `workflow_dispatch`.

Both kit jobs install the `sbx` CLI from a pinned `docker/sbx-releases` tarball (`SBX_VERSION` in the workflow) — there is no package-manager distribution for it, and `kit validate`/`kit push` have no other entry point.

`cd.yml` sets `cancel-in-progress: false` (unlike `ci.yml`'s `true`), deliberately, so a push in flight is never interrupted by a newer run.

## Environments

- Two artifact classes, both on GHCR, no staging/prod distinction:
  - Templates: one tag per (template, agent) pair, e.g. `templates/aidd` → `ghcr.io/crocogiciel/sbx-template-aidd:claude-code`. Pulled via `sbx run -t ghcr.io/crocogiciel/<PACKAGE_NAME>:<AGENT> <SBX-AGENT>` (`README.md`).
  - Kits: one OCI artifact per kit, tagged by the kit's own `spec.yaml` `version` and `latest`, e.g. `kits/aidd` (version `1.0.0`) → `ghcr.io/crocogiciel/sbx-kit-aidd:1.0.0` + `:latest`. Pulled with `sbx kit pull`.

## Release

- No git version tags in the repo. Template publishing is continuous (image tag stays `:<agent>`, no semver). Kit publishing is versioned by each kit's own `spec.yaml` `version` field, which a contributor must bump by hand before merging a kit change meant to ship.
