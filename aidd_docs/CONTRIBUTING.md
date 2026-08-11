# Contributing to this project's AI context

How to add or change the context the AI relies on here. For authoring AIDD skills, agents, rules, and templates, see the framework guide: <https://github.com/ai-driven-dev/framework/blob/main/CONTRIBUTING.md>.

## Changing project memory

Add or edit a file under `aidd_docs/memory/`. See [`memory/README.md`](memory/README.md) for what belongs there and how it loads.

## Adding AI content (skills, rules, agents, commands, hooks)

- Use the generator skills (`aidd-context:04-skill-generate` through `08-hook-generate`, and `10-learn` for memory or rules). They scaffold the right shape and write to the right place for each tool you use.
- Open a pull request for anything that changes how the AI behaves on this project. The team reviews it like any code change.

## Adding recipes

Create or edit project recipes under `aidd_docs/recipes/`. Use the cook skill when available so new recipes follow the shared contract and do not overwrite bundled framework recipes.

## House conventions

- A **kit** (mixin) lives at `kits/<name>/spec.yaml` and layers onto a base template. A **template** lives at `templates/<name>/` with its own `Dockerfile` and `README.md`. Both are registered by hand in `ci.yml`'s and `cd.yml`'s matrices — there is no dynamic discovery.
- Register a new template in both `ci.yml` (build-only check) and `cd.yml` (publish to `ghcr.io/crocogiciel/sbx-template-<name>`) when adding it, so it gets the same coverage as `templates/aidd`.
- Register a new kit in both `ci.yml`'s `validate-kits` matrix and `cd.yml`'s `publish-kits` matrix, and give it a `version` in its `spec.yaml`, so it gets the same coverage as `kits/aidd`/`kits/pnpm` and publishes to `ghcr.io/crocogiciel/sbx-kit-<name>`.
