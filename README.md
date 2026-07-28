# Edka Agent Profiles

Reusable Hermes profile distributions for agents running on Edka clusters.

## Profiles

- **[conductor](profiles/conductor/)** — orchestrates Edka Codex environments from chat: implements features in coding environments, reviews pull requests in fresh read-only environments, and iterates until the work is approved. Requires a Hermes agent with Conductor enabled in its Edka settings and an installed Codex agent as the target.

## Installing a profile

Each profile is a Hermes profile distribution rooted at `profiles/<name>/`; the profile takes its name from `distribution.yaml`.

**Via Edka (chart 0.5.6+):** when deploying a Hermes agent, set Bootstrap to Profile distribution with this repository's URL and the Distribution Subpath (e.g. `profiles/conductor`). The profile is installed once, on first boot, alongside the agent's default profile.

**By hand, on a running agent:**

```bash
git clone https://github.com/edkadigital/agent-profiles /tmp/agent-profiles
hermes profile install /tmp/agent-profiles/profiles/conductor
```

Distribution updates replace `SOUL.md`, `mcp.json`, `skills/`, and `scripts/`; your `config.yaml` edits and everything user-owned (memories, sessions, `.env`) are preserved.

## Conductor prerequisites

1. A Codex agent installed on the cluster with environments configured (pinned version, environment domain, auth).
2. The Hermes agent's **Conductor** tab enabled, with the target Codex agent selected and either Allow All Repositories or an explicit allowlist.
3. `EDKA_MCP_URL`, `EDKA_MCP_GRANT_URL`, and `EDKA_MCP_TOKEN` present in the pod environment (the Edka chart injects them once Conductor is enabled; a pending pod restart is the usual reason they are missing).
4. The `codex` CLI available in the pod for `edka-env run` (Hermes's codex runtime plugin provides it).
5. Messaging access controlled at the agent level: set Gateway Allowed Users in the agent's Configuration tab. Fail closed; do not run a conductor open to a whole workspace.
