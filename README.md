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
  and publishes it to Confluence. **Requires** the Atlassian claude.ai
  connector to be authorized with Confluence access (Jira-only scopes
  aren't enough) — this is a per-user OAuth connection made in claude.ai
  connector settings, not something configured in either repo. The skill
  asks each run which Confluence space/page to file under; nothing is
  hardcoded.

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
