# OpenAI-compatible adapter fork

## Source and branch

This fork integrates [paperclipai/paperclip#15409](https://github.com/paperclipai/paperclip/pull/15409) on `feature/openai-compatible-pr15409`.

- Upstream base: `043f2514e`.
- Imported PR head: `eb54c9fdc7d00c58d54a1b59827893b68e064f35`.
- The PR history is preserved in a merge, rather than copied or squashed. Future non-rewritten PR updates can therefore be merged incrementally.
- The `master` branch remains the upstream baseline, separate from the integration branch.
- Merge resolution preserves current native-runner thinking-effort controls and adds the API adapter's exclusion. The upstream lockfile is retained, following the repository lockfile policy.

## Run this source checkout

```sh
git switch feature/openai-compatible-pr15409
pnpm install --no-frozen-lockfile
pnpm dev
```

Use Node.js 24.11+ and pnpm 9+. Native Runner builds also require Rust/Cargo. `npx paperclipai onboard --yes` downloads the published package; it does not run this fork's changes. Use this source checkout to try the integration.

## Configure a local endpoint

In **Agents → New agent**, select **OpenAI-compatible API** and provide:

| Field | Example |
| --- | --- |
| API URL | `http://localhost:11434/v1` for an Ollama server; use the actual port of your oMLX or LiteLLM server |
| Model | The endpoint's exact model ID or LiteLLM `model_name` alias |
| API key | Your provider key or LiteLLM virtual key, saved as an organization secret |

Run the connection test before finishing setup. The model must support tool/function calling. The adapter runs in the Paperclip server process: `localhost` refers to that process's host/container. No real oMLX, Ollama, or LiteLLM endpoint was available for live verification during this import.

PR #15409 does **not** add a source to the first-agent onboarding wizard. It adds the adapter to the New agent flow. PR #15884 addresses that separate onboarding entry point; it is not included here. PR #15887 adds a dedicated LiteLLM connection option and protocol routing for other harnesses; it is not needed to use LiteLLM through this direct Chat Completions adapter.

Workspace shell/file tools are off by default. Enabling them runs commands with the Paperclip server user's permissions; this is not a filesystem sandbox. See the [adapter documentation](../docs/adapters/openai-compatible.md).

## Updating without force-pushing

Start with a clean working tree. Update the integration branch from upstream:

```sh
git switch feature/openai-compatible-pr15409
git fetch --no-tags https://github.com/paperclipai/paperclip.git master
git merge FETCH_HEAD
```

To incorporate a newer revision of the same upstream PR:

```sh
git fetch --no-tags https://github.com/paperclipai/paperclip.git refs/pull/15409/head
git merge FETCH_HEAD
```

Resolve any conflicts, install dependencies, run the relevant checks below, then push:

```sh
pnpm install --no-frozen-lockfile
pnpm exec vitest run packages/adapters/openai-compatible server/src/adapters/registry.test.ts ui/src/adapters ui/src/components/NewAgentDialog.test.tsx ui/src/pages/NewAgent.test.tsx ui/src/components/AgentConfigForm.render.test.tsx
pnpm check:token-gates
pnpm -r typecheck
pnpm test:run
pnpm build
git push origin feature/openai-compatible-pr15409
```

Keep local fixes in follow-up commits and record their user-visible effect in release notes. Review incoming changes before merging; a force-rewritten PR may require manual reconciliation. If upstream merges the PR, update from upstream master and check which fork-only resolutions are still necessary.

## Import verification (2026-10-22)

- Adapter tests: 27 passed across 5 files.
- Relevant UI/adapter tests: 373 passed across 17 files.
- Server adapter registry: 52 passed after building the plugin SDK.
- Adapter, server, CLI, and UI TypeScript checks: passed. UI checking generated the required ignored Storybook catalog first.
- Token gates and release package manifest validation: passed.
- Full `pnpm -r typecheck` and `pnpm build`: attempted; blocked by missing `cargo` when reaching the native Runner Rust step.
- Full `pnpm test:run`: attempted; did not finish within the 10-minute command timeout. No full-suite success is claimed.
- Browser journeys and a real local-model request were not run. The passing unit tests include mocked provider responses; they are not live provider qualification.
