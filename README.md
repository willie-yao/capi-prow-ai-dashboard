# capi-prow-ai-dashboard

AI-powered dashboard for [Cluster API](https://github.com/kubernetes-sigs/cluster-api) (CAPI core) E2E tests.

Deployed at **https://willie-yao.github.io/capi-prow-ai-dashboard/**.

## How this repo works

All the dashboard logic — Go fetcher, React frontend, universal Prow base
prompt — lives in
[`willie-yao/prow-ai-dashboard`](https://github.com/willie-yao/prow-ai-dashboard).
This repo owns CAPI-specific configuration and AI knowledge:

1. [`project.yaml`](project.yaml) — bucket, dashboard, branding, AI endpoint.
2. [`prompts/system.md`](prompts/system.md) — the CAPI-specific AI prompt addendum
   (architecture, common failure patterns, transient errors, artifact layout).
   This is concatenated between the engine's universal Prow base prompt and the
   engine's JSON response schema. See [docs/writing-prompts.md][writing-prompts]
   for guidance.
3. `.github/workflows/deploy.yml` — calls the engine's reusable workflow on a
   30-minute cron with `project_dir: "."`.
4. `gh-pages` branch — stores the built site.

To tweak CAPI-specific behavior, edit `project.yaml` or `prompts/system.md` in
this repo. Editing the prompt does **not** invalidate the AI cache; run the
`Clear AI Cache` workflow after a prompt edit.

[writing-prompts]: https://github.com/willie-yao/prow-ai-dashboard/blob/main/docs/writing-prompts.md

## Required secrets

Set these under **Settings → Secrets and variables → Actions**:

- `AI_TOKEN` — token for the chat completions endpoint (used for failure analysis).
- `SLACK_WEBHOOK_URL` — webhook for failure notifications (optional).

## Required settings

- **Settings → Pages → Source**: `GitHub Actions`.

## Forcing a fresh AI run

Run the `Clear AI Cache` workflow under the **Actions** tab to wipe the cache,
then re-run `Deploy Dashboard`.
