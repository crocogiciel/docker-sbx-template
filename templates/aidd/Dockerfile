# Custom Docker Sandboxes template for an agent (default claude-code)
ARG BASE_AGENT=claude-code

FROM docker/sandbox-templates:${BASE_AGENT}

# ============================================================================
# CONFIGURATION
# ============================================================================
ARG BASE_AGENT


# ============================================================================
# ROOT - Install the system packages
# ============================================================================

USER root

# `codegraph init` needs the project's source code, which is only mounted when
# the container starts, never during the build. So indexing happens at
# runtime, triggered by the SessionStart hook configured further down.
RUN cat > /usr/local/bin/codegraph-bootstrap <<'BOOTSTRAP' \
    && chmod 0755 /usr/local/bin/codegraph-bootstrap
#!/usr/bin/env bash
# Build (or refresh) the CodeGraph graph for the mounted project.
# Called by Claude Code's SessionStart hook. It must return immediately:
# indexing is sent to the background.
set -uo pipefail

dir="${CLAUDE_PROJECT_DIR:-$PWD}"
log="$HOME/.codegraph-bootstrap.log"

cd "$dir" 2>/dev/null || exit 0
command -v codegraph >/dev/null 2>&1 || exit 0

# Indexing already running (another session on the same workspace): leave it
# alone.
if pgrep -u "$(id -u)" -f 'codegraph (init|index|sync)' >/dev/null 2>&1; then
  exit 0
fi

if [ -d .codegraph ]; then
  # The graph already exists — it lives in the project, so it outlives the
  # container. CodeGraph's live watcher cannot rely on filesystem events
  # across a virtiofs mount, so resync on every session instead.
  nohup codegraph sync >>"$log" 2>&1 &
  exit 0
fi

nohup codegraph init >>"$log" 2>&1 &
printf '{"systemMessage":"CodeGraph: building the graph in the background (%s)."}\n' "$log"
BOOTSTRAP


# ============================================================================
# AGENT - Install the user packages
# ============================================================================

USER agent
WORKDIR /home/agent
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Plugins are installed as `agent`, not as root: `USER root` sets HOME=/root,
# so everything would land in /root/.claude — invisible to the agent, which
# runs as `agent` at runtime.
RUN claude plugin marketplace add ai-driven-dev/framework \
    && claude plugin install aidd-context@aidd-framework \
    && claude plugin install aidd-refine@aidd-framework \
    && claude plugin install aidd-dev@aidd-framework \
    && claude plugin install aidd-vcs@aidd-framework \
    && claude plugin install aidd-pm@aidd-framework \
    && claude plugin install aidd-orchestrator@aidd-framework \
    && claude plugin install aidd-ui@aidd-framework

# Install CodeGraph MCP
# `--location=global` rather than `local`: a local install only applies to the
# current directory (/home/agent at build time), whereas the project is mounted
# elsewhere at runtime, at its absolute path on the host. Global covers every
# project.
RUN curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh \
    && codegraph install --target=claude --location=global --yes

# Trigger indexing at the start of every session, inside the mounted project.
RUN mkdir -p "$HOME/.claude" \
    && settings="$HOME/.claude/settings.json" \
    && [ -s "$settings" ] || echo '{}' > "$settings" \
    && tmp="$(mktemp)" \
    && jq '.hooks.SessionStart = ((.hooks.SessionStart // []) + [{"hooks":[{"type":"command","command":"/usr/local/bin/codegraph-bootstrap","timeout":15,"statusMessage":"CodeGraph"}]}])' "$settings" > "$tmp" \
    && mv "$tmp" "$settings"


# ============================================================================
# ROOT - Configure the shell environment
# ============================================================================

USER root


# ============================================================================
# AGENT - Run the container as agent
# ============================================================================

USER agent
