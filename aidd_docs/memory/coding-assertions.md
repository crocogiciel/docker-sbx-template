# Coding Assertions

The checks that must pass for code to count as done.

## Before push

| Order | Command | Checks |
| ----- | ------- | ------ |
| 1 | `docker build --build-arg BASE_AGENT=<agent> -f templates/<name>/Dockerfile -t sbx/<name>:local templates/<name>` | The template image builds. Mirrors `ci.yml`'s `build` job, single-arch — CI additionally builds `linux/arm64`, which can fail even after a local pass (e.g. CodeGraph's `install.sh` fetches an arch-specific binary). |
| 2 | `sbx kit validate kits/<name>` | The kit's `spec.yaml` is structurally valid. Mirrors `ci.yml`'s `validate-kits` job exactly (same command, no arch dimension). |

## Behavior

- Two gates, one per artifact kind — neither is a typecheck, lint, or test suite (see `testing.md` for the manual runtime checks a green build/validate cannot prove).
- `sbx kit validate` is schema-only: it does not apply the kit in a real sandbox, so a kit can pass and still fail at `sbx run` (e.g. a network domain missing from `permissions.network.allow`).
