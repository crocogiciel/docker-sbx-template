# AI Operating Guidelines

How this team drives AI coding assistants on this project. Keep it short and specific to this repo. Fill the placeholders, drop what does not apply.

## House rules

- Every non-obvious decision in a `spec.yaml` or `Dockerfile` gets a comment explaining the why, right next to the line it justifies — not in a separate doc.
- A `spec.yaml` states the minimum Docker Sandboxes version its grammar needs (e.g. `schemaVersion: "2"` needs 0.36+) in a leading comment, checkable with `sbx version`.
- Add a new template or kit under `templates/` or `kits/`, matching the shape of the existing `aidd` template / `pnpm` kit.

## Validation depth

- CI (`ci.yml`) builds every `templates/*` Dockerfile against every agent in the matrix on every push and PR, multi-arch, without pushing, and runs `sbx kit validate` on every `kits/*` entry — both passing is the merge bar.
- There is no lint or test suite; the Dockerfile build and `sbx kit validate` are schema/build-level only. `sbx kit validate` never applies a kit to a real sandbox — verify a kit's actual install behavior manually with `sbx run --kit .` before trusting a change that touches its `setup.install` commands.
- A kit meant to publish a new version needs its `spec.yaml` `version` bumped by hand before merge — CD tags the GHCR artifact from that field.

For the general AIDD playbook (planning, review loops, prompting and context hygiene, anti-patterns), see the framework docs: <https://github.com/ai-driven-dev/framework>.
