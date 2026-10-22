# Paperclip fork — OpenAI-compatible API integration

> Unreleased fork integration: 2026-10-22

## Highlights

- Run agents directly against an OpenAI-compatible Chat Completions endpoint using an API URL, model ID, and API key, without installing a separate agent CLI. Imported from [upstream PR #15409](https://github.com/paperclipai/paperclip/pull/15409).
- Configure the adapter through **Agents → New agent → OpenAI-compatible API**; API keys entered there are saved as organization secrets.

## Upgrade Guide

- This integration is on `feature/openai-compatible-pr15409`, based on upstream `043f2514e` and PR head `eb54c9fdc7d00c58d54a1b59827893b68e064f35`. It is not an official Paperclip release.
- The first-agent onboarding wizard is not extended by this PR. The new adapter is available in the New agent setup flow.
- LiteLLM can be used through its OpenAI-compatible `/v1` URL and a virtual API key; PR #15887 is not required for this adapter. Models must support tool/function calling.
- See [fork maintenance and setup](../doc/FORK.md) for installation, limitations, verification, and updates.
