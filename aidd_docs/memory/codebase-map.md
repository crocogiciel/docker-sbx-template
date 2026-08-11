# Codebase Map

The macro layout: the top-level areas and what each holds.

## Areas

- `README.md`: the consumer-facing install path — `sbx login`, security policy, GHCR token setup, then `sbx run -t <image> <agent>`.
- `kits/`: mixin `spec.yaml` files (`aidd`, `pnpm`) — setup layered onto a base template by the `sbx` CLI at sandbox creation, not baked into an image. Schema-validated and published as OCI artifacts by CI (see `deployment.md`), but never applied to a real sandbox by it.
- `templates/`: full sandbox images, each its own `Dockerfile` + `README.md`. Currently `templates/aidd` (Claude Code + AIDD plugins + CodeGraph); it happens to install the same AIDD plugin set as `kits/aidd`, kept in sync by hand (see `architecture.md`).
- `.github/workflows/`: build and publish pipelines — see `deployment.md`.

## Entry points

- Each template's `Dockerfile` is the build entry point; see that template's own `README.md` for the exact build invocation (e.g. `templates/aidd/README.md`).
- Each kit's `spec.yaml` is applied by the `sbx` CLI, not built directly.
