# Anvil Integration

This fork of `OpenHands/software-agent-sdk` is the substrate for **Anvil**, a self-improving
coding-agent framework at `github.com/penumbral-labs/anvil`. This document tracks the pin point and
integration state.

## Pin

- **Anvil baseline tag:** `v-anvil-baseline-2026-04-21`
- **Upstream commit:** `059c944d` (2026-04-21)
- **Upstream title at pin:** `fix(skills): Load installed skills in load_user_skills() (#2884)`
- **Customization branch:** `anvil-baseline` (branched from the tag)

## Why pinned

OpenHands SDK is actively developed (548 commits in the 90 days preceding pin). The stated API
stability policy requires a 5-minor-release deprecation runway for public API removals (see
`AGENTS.md:134-147`), and breaking SDK API changes require at least a MINOR SemVer bump. Anvil pins
to a spike-verified HEAD and re-evaluates the pin each MINOR upstream release.

## Spike verification (2026-04-21)

`anvil/spikes/forge-skeleton/run_spike.py` passed **8/8 stages** against this pin point using
Kimi-K2 via OpenRouter:

1. Plugin loads from local path — PASS
2. Plugin introspection surface (agents, skills, commands, entry_slash_command) — PASS
3. Agent composes with CriticBase subclass — PASS
4. Conversation instantiates with Agent + Critic; AgentBase frozen enforced — PASS
5. AgentDefinition factorises via `agent_definition_to_factory` — PASS
6. `conversation.run()` executes and reaches FINISHED status — PASS
7. Critic fires automatically on FinishEvent, returns CriticResult — PASS
8. `Conversation.fork()` produces an independent conversation with copied events — PASS

## Anvil-specific modifications

These land on the `anvil-baseline` branch. Upstream-submittable subsets go to separate
`upstream/*` branches.

| Step | Modification | Upstream-submittable? | Status |
|------|---|---|---|
| 2 | Codex:sub OAuth pre-sync from `~/.codex/auth.json` in `openhands-sdk/.../llm/auth/openai.py` | Yes (`upstream/codex-oauth-cli-parity`) | pending |
| 2 | Strip `"originator": "openhands"` from `_build_authorize_url` at `openai.py:250` | Likely fork-only (upstream may push back) | pending |
| 4 | Port Hermes's `MAX_DEPTH=2` enforcement into `openhands-tools/.../delegate/impl.py` | Yes | pending |

## Upgrade policy

Re-evaluate the pin when upstream ships a MINOR release:

1. Read upstream changelog + deprecation notices
2. Rerun `anvil/spikes/forge-skeleton/run_spike.py` against the new HEAD
3. If spike green: advance the `v-anvil-baseline-<date>` tag, rebase `anvil-baseline` onto new
   pin, update this document
4. If spike red: file an issue, don't advance, investigate regression

## Known EXPERIMENTAL APIs used

- `AgentBase.critic` field (`openhands-sdk/openhands/sdk/agent/base.py:217-225`) — labeled
  EXPERIMENTAL. API/behavior may change without notice. Anvil's multi-axis eval loop depends
  directly on this. Monitor upstream MINOR bumps for API churn.

## Related documents

- Decision rationale: `~/.claude/projects/-home-aaron-src-github-com-penumbral-labs-anvil/memory/project_substrate_decision.md`
- Deep audit: `anvil/.plan/harness-survey/09-openhands-deep-audit.md`
- Execution plan: `anvil/.plan/anvil-execution-plan.md`
