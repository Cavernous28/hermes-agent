# Keirstin Customization Guide — `Cavernous28/hermes-agent`

This is a downstream fork of `NousResearch/hermes-agent` customized for Cavernous Games / Keirstin. The goal is not to maintain a full Hermes rewrite, but to keep a stable, trimmed branch (`keirstin`) that loads only what we use, while making everything else easy to re-enable.

## Branches

| Branch | Tracks | Purpose |
|--------|--------|---------|
| `main` | `upstream/main` | Clean upstream latest. Use when you want a straight NousResearch build. |
| `keirstin` | `origin/keirstin` | Our customized branch. Carries upstream merges + our cherry-picked/custom changes. |

## Remotes

```bash
origin   https://github.com/Cavernous28/hermes-agent.git
upstream https://github.com/NousResearch/hermes-agent.git
```

## Switching Between Main and Keirstin

```bash
# Use latest upstream
git checkout main
git pull upstream main
# rebuild Hermes

# Use our customized version
git checkout keirstin
# optionally pull latest upstream and merge:
git fetch upstream
git merge upstream/main
# rebuild Hermes
```

Rebuilding Hermes is required because both the Python backend and the Electron desktop app are built from source.

## What We Disabled by Default

The disabled items are gated through the `plugins.disabled` list in `config.yaml`. They remain in the source tree so they can be re-enabled without rebuilding code.

### Providers (kept: `ollama-launch`, `lm-studio` configured but off)

Disabled providers:

- alibaba, alibaba-coding-plan
- anthropic
- arcee
- azure-foundry
- bedrock
- copilot, copilot-acp
- custom
- deepinfra, deepseek
- fireworks, gemini, gmi
- huggingface
- kilocode, kimi-coding
- minimax
- nous
- novita, nvidia
- openai-codex
- opencode-zen, openrouter
- qwen-oauth
- stepfun, upstage
- vertex
- xai
- xiaomi, zai

**Re-enable a provider:**

1. Add it under `providers:` in `config.yaml` (or use `hermes config set`).
2. Remove its plugin entry from `plugins.disabled`, e.g. `model-providers/anthropic`.
3. Restart Hermes.

### Messaging Platforms (all disabled)

- dingtalk, discord, email, feishu, google_chat, homeassistant, irc, line, matrix, mattermost, ntfy, photon, raft, simplex, slack, sms, teams, telegram, wecom, whatsapp

**Re-enable a platform:**

1. Remove its plugin entry from `plugins.disabled`, e.g. `platforms/discord`.
2. Configure it under `display: platforms:` or provider-specific config.
3. Restart Hermes.

### Optional Plugins / Toolsets

Moved to `keirstin_optional` in config:

- `clawd-on-desk` — desktop pet
- `computer_use` toolset
- `context_engine` toolset
- `tts` toolset

**Re-enable:** remove from `keirstin_optional` and add to `platform_toolsets.cli` / `plugins.enabled` as appropriate.

### Display / Billing

- `credits_notices: false` — hides Nous billing/out-of-credits UI.

## Core Keirstin Pieces We Keep

- **Memory provider:** `keirstin_8tier` (configured in main `config.yaml` under `memory.provider`).
- **Overflow / context engine:** `keirstin_overflow` plugin (enabled in `plugins.enabled`).
- **Surfaces:** desktop (primary), TUI (kept for emergency), Android mobile app (separate `hermes-android` repo).
- **Providers:** Ollama (primary), LM Studio (configured, disabled by default).
- **Toolsets:** browser, code_execution, cronjob, delegation, file, image_gen, kanban, memory, session_search, skills, terminal, todo, video, vision, web.

## Test Profile

We test changes under the `test-chris` Hermes profile before applying them to the main profile.

```bash
hermes --profile test-chris
```

## Updating from Upstream

1. `git checkout keirstin`
2. `git fetch upstream`
3. Review `git log HEAD..upstream/main --oneline` for relevant fixes.
4. Either cherry-pick specific commits or merge the whole branch:
   ```bash
   git merge upstream/main
   ```
5. Resolve conflicts if any.
6. Push: `git push origin keirstin`
7. Rebuild Hermes.

## Rebuilding Hermes

```bash
cd /c/Users/cbaxt/.hermes/hermes-agent

# Python backend / CLI
uv pip install -e .

# Desktop app
cd apps/desktop
npm run dist:win:nsis
```

Restart the Hermes desktop app after building.
