# AI Registry — Docker (Inferred)

> **Inferred vendor repository.** This repo is maintained by the [AI Registry](https://github.com/eclipsefdn-ai-registry/ai-registry-core) project, not by Docker. It pre-seeds the registry with an Agent Skill published by Docker at [github.com/docker/model-runner](https://github.com/docker/model-runner), plus the connection config for Docker's self-published Docker Hub MCP server.
>
> *This entry is based solely on information published through Docker's official public channels. Docker has not endorsed, approved or validated this listing, and is not necessarily participating in the AI Registry.*

## What this repo contains

**Agent Skill:**

- `docker-model-runner` — the skill bundled inside the `docker/model-runner` CLI (`cmd/cli/commands/skills/docker-model-runner/SKILL.md`), installable by end users via `docker model skills --claude` (also `--codex`, `--opencode`, or `--dest`). This is a genuine first-party, externally-distributed skill: the CLI command explicitly copies it into `~/.claude/skills` and equivalents for other tools, not an internal dev-workflow file.

**MCP server:**

- **Docker Hub MCP Server** (`com.docker/hub-mcp`) — not listed in the official Anthropic/modelcontextprotocol.io registry, so this approval supplies its own `metadata` (fallback name/description) and a generic `config` instead of relying on registry lookup; `mcpRegistryVerified` will read `false`, which the schema treats as a warning, not a blocker. The config is the officially published stdio connection (`docker run -i --rm mcp/dockerhub --transport=stdio`) from the `mcp/dockerhub` Docker Hub image page, which is explicitly attributed to `docker` (Docker Inc.) and links back to [github.com/docker/hub-mcp](https://github.com/docker/hub-mcp) as its source. Marked `selfPublished: true` since Docker Inc. is both the image publisher and the source repo owner. (The full README also documents an authenticated variant with a `HUB_PAT_TOKEN` env var and a `--username` flag; this approval uses the simpler public-access-only invocation as the reusable, credential-free default.)

**Agent Plugin — considered and excluded:**

- [`docker/claude-plugins`](https://github.com/docker/claude-plugins) is a genuine, first-party plugin marketplace (`docker` GitHub org, `plugin.json` `author.name: "Docker Inc."`) with two plugins: `mcp-toolkit` (wires up Docker Desktop's MCP Gateway) and `beta-mcp-skills` (bundles the `mcp-server-yaml-creator` skill). It was **not** approved here because it isn't an [agent-plugins.org](https://agent-plugins.org) plugin in the first place — the repo's own README says explicitly: "This repository contains a Claude Code plugin marketplace." agent-plugins.org is a separate, vendor-neutral standard (its spec puts `plugin.json`, `mcp.json`, and `skills/` at the plugin directory's root); Claude Code's own marketplace mechanism is Anthropic-specific, with each plugin's manifest at `plugins/<name>/.claude-plugin/plugin.json` and its MCP config at a sibling `.mcp.json` (leading dot). Docker's repo only implements the latter — there's no root-level `plugin.json`/`mcp.json`/`skills/` anywhere in it. Contrast with this registry's other approved plugins (e.g. `atlassian/forge-skills`, `gemini-cli-extensions/bigquery-data-analytics`), which publish an agent-plugins.org-conformant root layout *in addition to* Claude-Code-specific (`.claude-plugin/`), Codex-specific (`.codex-plugin/`), Cursor-specific, and Gemini-specific adapters — Docker's repo has only the Claude Code adapter, with nothing agent-plugins.org-conformant underneath it. Confirmed empirically: pointing `source.path` at a plugin's `.claude-plugin` subdirectory does let `plugin-source.ts` parse `name`/`description`/`version`/`author`/`homepage`/`keywords`, but `containedSkills`/`containedMcpServers` come back empty for both plugins even though bundling an MCP server and a skill respectively is each plugin's entire purpose — a symptom of forcing a Claude-Code-only artifact through the agent-plugins.org-shaped parser, not a bug in the parser itself. There's nothing to fix on the core-repo side; Docker simply hasn't published an agent-plugins.org plugin.

**A2A agent — none found:** no `agent_card.json` (or equivalent) published by Docker was found. `docker/docker-agent` (the `cagent` CLI) and `docker/compose-for-agents` both reference the A2A protocol as a capability their runtime/examples can use, but neither publishes a specific first-party agent as an installable A2A agent card.

**Other candidates considered and rejected:**

- `dockersamples/mcp-docker-release-information` — registry-listed (`io.github.dockersamples/mcp-docker-release-information`), and the `dockersamples` org's public members are real Docker Inc. engineers, but the org isn't GitHub-verified and the repo's own README says "It is used as a sample in blog posts and other training materials" — an explicit demo artifact rather than a maintained product, so it was excluded.
- `docker/mcp-obsidian` — a fork of a community Obsidian MCP server, not Docker's own work.
- `docker/labs-ai-tools-for-devs` — explicitly deprecated in favor of the bundled MCP Toolkit (covered by the excluded plugin above).
- `docker/docs`, `docker/docker-agent`, `docker/docker-agent-action`, `docker/mcp-gateway` `.agents/skills/*` / `.claude/skills/*` entries (e.g. `write`, `check-pr`, `triage-prs`, `bump-go-dependencies`, `otel-instrument`) — internal repo-maintenance automation skills Docker uses to manage those specific repos with their own dev-agent workflows, not published for external users to install.
- `docker/sbx-kits-contrib` — explicitly a community repository, not first-party.

## Documentation

See the [Vendor Guide](https://github.com/eclipsefdn-ai-registry/ai-registry-core#vendor-guide) in the central repository for how vendor repos work, how to add approvals, and how validation runs.
