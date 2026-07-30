# claude-marketplace

Claude Code plugin marketplace for georgejinu-labs.

Install with:

```
/plugin marketplace add georgejinu-labs/claude-marketplace
```

## How SHA pinning works

Each entry in `.claude-plugin/marketplace.json` pins a plugin to an exact
commit (`source.sha`) in [claude-plugins](https://github.com/georgejinu-labs/claude-plugins),
not a moving branch. When a plugin subdirectory changes on `main` in that
repo, its `notify-marketplace` workflow fires a `repository_dispatch` here,
and `update-plugin-sha.yml` bumps the matching entry's `sha` and pushes —
no manual edits needed.

## Plugins

- `dev-toolkit` — commit hygiene, debugging, dependency audits, doc sync,
  env checks, SQL review, test generation, todo sweeps.
- `knowledge-capture` — `/doc-learning` captures something you just learned
  and publishes it to Confluence. Bundles the official Atlassian remote MCP
  server (`https://mcp.atlassian.com/v1/mcp/authv2`) in its own `.mcp.json`,
  so it's portable — no claude.ai account connector to pre-configure, no
  GitHub secret involved. First use prompts a one-time OAuth connect via
  `/mcp` (per user, per machine). **Requires** Confluence to actually be
  provisioned on the Atlassian site being used (a Jira-only site won't have
  it). The skill asks each run which Confluence space/page to file under;
  nothing is hardcoded.

## Adding a new plugin to the marketplace

Add an entry to `plugins[]` in `marketplace.json`:

```json
{
  "name": "<plugin-name>",
  "description": "...",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/georgejinu-labs/claude-plugins.git",
    "path": "plugins/<plugin-name>",
    "ref": "main",
    "sha": "<initial commit sha>"
  }
}
```

No CI setup is needed in this repo beyond the default `GITHUB_TOKEN`
(already granted `contents: write` in the workflow) — the PAT lives in the
`claude-plugins` repo, since that's the side that needs to reach across
repos.
