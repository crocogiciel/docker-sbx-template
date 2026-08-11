# Project Brief

What this project is, the problem it solves, and its domain language. The non-derivable "why", not the "how".

## What it is

- A repo of Docker Sandboxes templates (CI-built, published to GHCR) and kits (mixin specs applied from source by the `sbx` CLI), for running AI coding agents (Claude Code) in isolated sandboxes.

## Why it exists

- Docker Sandboxes ship a bare per-agent base image; this repo layers the setup a team actually wants (AIDD's Claude Code plugins, CodeGraph indexing, pnpm) on top, as a versioned, CI-built artifact instead of a one-off `sbx` session hack.

## Domain language

| Term | Meaning |
| ---- | ------- |
| Docker Sandbox | An isolated container, managed by the `sbx` CLI, that runs an AI coding agent against a mounted project. |
| kit (mixin) | A `spec.yaml` under `kits/` that layers setup (packages, env, permissions) onto a base agent image, applied at sandbox creation. Not built or verified by CI. |
| template | A full image under `templates/` (its own `Dockerfile`), built and published by CI — see `deployment.md`. |
| AIDD | The AI-Driven Dev framework (`ai-driven-dev/framework`), installed as Claude Code plugins by the `aidd` kit and the `aidd` template — see `architecture.md` for why both exist. |
| CodeGraph | A code-indexing MCP server installed in the `aidd` template, initialized from a Claude Code `SessionStart` hook once a session starts. |
