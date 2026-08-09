# AIDD template

A `claude-code` sandbox with the
[AIDD](https://github.com/ai-driven-dev/framework) framework plugins and
[CodeGraph](https://github.com/colbymchenry/codegraph) preinstalled.

## Build

```bash
docker build -t sbx/aidd:local .
```

To base the image on another agent than `claude-code`:

```bash
docker build --build-arg BASE_AGENT=<agent> -t sbx/aidd:local .
```

## What happens at build time, and what cannot

| | Where | Why |
|---|---|---|
| AIDD plugins | build, as `agent` | `USER root` sets `HOME=/root`: installed as root they would land in `/root/.claude`, invisible to the agent, which runs as `agent`. |
| CodeGraph CLI + MCP wiring | build, `--location=global` | A `local` install only applies to the current directory (`/home/agent` at build time), while the project is mounted elsewhere at runtime. |
| `codegraph init` | **runtime** | It indexes the project's code and writes `.codegraph/` at its root. The project is only mounted when the container starts, at its absolute path on the host — unknowable at build time. |

Indexing is triggered by a `SessionStart` hook that runs
`/usr/local/bin/codegraph-bootstrap` inside the project:

- first start: `codegraph init` (creates `.codegraph/` and builds the graph);
- later starts: `codegraph sync`, since `.codegraph/` lives in the project and
  outlives the container.

The hook returns immediately — indexing runs in the background and logs to
`~/.codegraph-bootstrap.log`. On a large repository, the first CodeGraph
queries of a fresh session may therefore land before the graph is finished;
`codegraph status` shows where it stands.

Resyncing on every session is deliberate: CodeGraph's live watcher relies on OS
filesystem events, which do not cross a virtiofs mount reliably. If changes
made outside a session seem missing from the graph, `codegraph sync` catches
them up.

## Checking inside a container

```bash
claude plugin list        # AIDD plugins, user scope
codegraph status          # graph status for the current project
```
