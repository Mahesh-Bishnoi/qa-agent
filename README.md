# qa-agent — Agentic QA GitHub Action

Composite action that runs the Agentic QA `qa-agent` in **your** CI so your
repo code never has to be read by the SaaS platform.

```yaml
- uses: Mahesh-Bishnoi/qa-agent@v1
  with:
    command: generate # generate | run | assist-context | ...
  env:
    QA_API_KEY: ${{ secrets.QA_API_KEY }} # repo-scoped aq_live_… key (required)
    QA_PLATFORM_URL: ${{ secrets.QA_PLATFORM_URL }} # SaaS base URL (required)
    QA_LLM_ENDPOINT: ${{ secrets.QA_LLM_ENDPOINT }} # optional pair: your own model
    QA_LLM_API_KEY: ${{ secrets.QA_LLM_API_KEY }}
```

What it does, in order:

1. Installs Node 24 and `@mahesh-bishnoi/qa-agent` from npm.
2. Installs the pinned OpenCode CLI (`opencode-version`, default `1.18.33`).
3. Calls `GET <QA_PLATFORM_URL>/api/v1/settings/effective` as a guard —
   when the triggering event is disabled in the repo's effective settings it
   exits 0 with a notice instead of running (`enabled: 'false'` output).
4. Runs `qa-agent <command>` (`assist-context` also exposes `opencode_config`
   and `model_base_url` outputs for the `/qa` reply step).

## Inputs

| Input | Required | Default | Purpose |
|---|---|---|---|
| `command` | yes | — | `generate`, `run`, `detect`, `ingest`, `cases`, `explore`, `scripts`, `revise`, `classify`, `heal`, `assist-context` |
| `mode` | no | `regenerate` | generate mode: `setup`, `regenerate`, `scripts`, `revise` |
| `pr` | no | — | PR number (required for revise) |
| `api-url` | no | — | Overrides `QA_PLATFORM_URL` env |
| `agent-version` | no | `latest` | npm version of `@mahesh-bishnoi/qa-agent` |
| `opencode-version` | no | `1.18.33` | Pinned OpenCode CLI version |
| `event-name` | no | — | Overrides `GITHUB_EVENT_NAME` for the guard |

## Outputs

| Output | Meaning |
|---|---|
| `enabled` | `'true'` when the trigger ran, `'false'` when the guard skipped it |
| `opencode_config` | OpenCode config JSON (only set by `assist-context`) |
| `model_base_url` | Model base URL (only set by `assist-context`) |

## Security baseline

Customer workflows use `pull_request` (never `pull_request_target`),
`persist-credentials: false`, least-privilege `permissions:`, and this action
pinned to a release tag (never `@main`). Full workflow templates live in the
platform's `templates/github/` (`qa-generate.yml`, `qa-run.yml`,
`qa-assist.yml`).

## License

MIT — see [LICENSE](LICENSE).
