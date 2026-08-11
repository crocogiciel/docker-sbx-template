# Testing

There is no automated test layer — no test files, framework, linter, or `Makefile` anywhere in the repo. The build in `coding-assertions.md` is the only automated check, and it only proves the image builds, not that it behaves.

## Manual runtime verification

A green build says nothing about the substance of `templates/aidd/Dockerfile` — the `SessionStart` hook, managed-settings precedence, CodeGraph indexing. `templates/aidd/README.md` documents the actual manual check, run inside a container:

- `claude plugin list` — AIDD plugins show enabled, user scope.
- `codegraph status` — indexing graph status for the mounted project.
- `claude mcp list` — CodeGraph's MCP server is registered (not covered by managed settings, see `architecture.md`).

Kits (`kits/*/spec.yaml`) get schema validation (`coding-assertions.md`) but no runtime check — `sbx kit validate` never applies a kit to a real sandbox, so its actual install commands (e.g. `kits/aidd`'s `claude plugin install` loop) are unverified beyond a manual `sbx run --kit .` smoke test.
