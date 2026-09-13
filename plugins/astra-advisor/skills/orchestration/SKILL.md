---
name: orchestration
description: "Plan, route, implement, verify, and review substantial work with GPT-6 Astra or GPT-5.6 Sol and dynamically selected permitted Codex workers."
---

# Astra Advisor Orchestration

Act as the architect and acceptance owner. Supported primary models are `gpt-6-astra`
and `gpt-5.6-sol`. Keep the selected primary model and the effort selected by the user.
Both primary models are supported at any effort available in the current host.
The selected primary model owns intent, architecture, decomposition,
delegation decisions, parent verification, and acceptance. A skill cannot change the
parent model or effort, and must honor the invocation's effort. If observable runtime
metadata says the parent model is neither `gpt-6-astra` nor `gpt-5.6-sol`, report the
mismatch as a selection prerequisite and do not claim supported orchestration. If the model or effort
is unobservable, disclose that fact rather than inventing confirmation.
On Sol, Sol performs all parent responsibilities; do not require or imply an Astra
call. Existing `ASTRA` status labels identify the plugin, not the actual model.

After capability preflight and before the first implementation or delegation task
call, emit a short, machine-auditable declaration:

~~~text
ASTRA ROUTE
parent: <observed model or unobservable> / <observed effort or unobservable>
delegation: <none or the selected worker models and efforts>
risk: <concise, task-specific rationale>
~~~

Report model and effort as observed evidence. If metadata does not expose a value,
say that it is unobservable; never claim a runtime pin that was not confirmed. Read
[the operations reference](references/operations.md) before the first delegation.

Before delegation, resolve the optional user roster as
`$CODEX_HOME/astra-advisor/allowed-workers.json` when `CODEX_HOME` is set, otherwise
`~/.codex/astra-advisor/allowed-workers.json`. If it exists, validate and use it. If it
is absent, validate and use the packaged [worker roster](references/worker-roster.json)
as the read-only default. An invalid user roster disables delegation instead of
falling back. The user roster is the only editable allowlist; select an enabled entry
dynamically from the task's risk, context, and independent work. The roster is a
policy allowlist, not an engine access control list.
Use an exposed generic spawn tool under the `agents` or `collaboration` namespace only
when its schema supports an explicit `model` and a live-supported `reasoning_effort`.
With a version 2 interface, set `fork_turns: none`. With a version 1 interface,
dispatch only when its exposed fresh-context control is available, setting
`fork_context: false` when that is the control. Confirm the schema in each fresh task;
never invent a control. Do not encode a role-to-model mapping or a fixed number of
subagents. Give every subagent a concrete, bounded, independent deliverable while the parent
continues useful parent work. Do not duplicate the parent's implementation or
verification in a subagent.

Tools and their public schemas are authoritative. Select only an effort the current
tool exposes. If a selected model, effort, spawn control, or required spawn tool is
missing, conflicting, unavailable, or unobservable, fail that delegation closed and
continue only with safe parent work or report the limitation. Never silently
substitute a model, effort, role, or fabricated tool. Introspection may clarify an
omitted runtime field; it cannot replace an available public contract.

For a substantial implementation, the parent must inspect the complete diff and rerun the
requested checks before starting a fresh read-only review. Select the reviewer from
the enabled worker roster with explicit model and effort controls. Give it the actual
change set and evidence, and require:

~~~text
ASTRA REVIEW
VERDICT: ship | fix-first | rethink
REASON: <evidence-based reason>
FINDINGS: <precise findings or none>
RESIDUAL RISK: <remaining risk or none>
~~~

Accept a substantial implementation only after the fresh reviewer returns `ship`.
After `fix-first`, the parent applies the correction, verifies again, and obtains a
new fresh review. A reviewer remains read-only and never fixes its own findings.

Use permitted Codex workers only through the exposed spawn interface. The roster may
include models routed by the existing Codex `openai` transport; do not use a nested
CLI, API key, or other direct inference transport. Separate app tasks require an
explicit user request. For an explicit Codex app task, `mcp__codex_app__create_thread`
supports `model` and `thinking`; call
`mcp__codex_app__list_projects` first for project targets, using a worktree by default
for Git projects and local otherwise. ChatGPT Work cloud `create_thread` must omit
`model` and `thinking`, so it cannot currently promise arbitrary model or effort
control; do not dispatch a model-pinned request there by default or use an API-key/CLI
workaround. Use a future app work tool only when its schema exposes the required
controls.

## Live delegation updates

Automatically show a short user-visible lifecycle update for **every** delegation,
including reviews, before dispatch and on completion or failure. Before dispatch,
include task name, exact bounded ownership, requested model and effort, and the
reason for that selection. On return, include agent ID, actual status, and observed
model/effort with their evidence source; if unavailable say `unobservable`. If they
differ from the request, show both. A submitted request is not runtime confirmation.
Keep progress readable; report meaningful changes without polling narration.

Finish with the verified result and any remaining limitation. Do not collect token
usage, query prices, calculate costs or savings, or emit a cost report as part of
this plugin's workflow.
