# Astra Advisor operations

This reference holds the operational details behind the short orchestration skill.
It describes capability selection and evidence rules; it does not define installed
roles, role files, task lanes, or an installer. The optional user roster is
`$CODEX_HOME/astra-advisor/allowed-workers.json` when `CODEX_HOME` is set, otherwise
`~/.codex/astra-advisor/allowed-workers.json`. If it is absent, the packaged
[worker roster](worker-roster.json) is the read-only default. An invalid user roster
disables delegation instead of falling back.

## Parent session

The primary session is GPT-6 Astra (`gpt-6-astra`) or GPT-5.6 Sol (`gpt-5.6-sol`)
at whatever supported effort the user selected. Neither model requires a particular effort.
The invocation is authoritative. Do not require a particular effort, rewrite the
parent configuration, or claim a model/effort pin without runtime evidence. If the
session exposes model and effort metadata and the model is neither `gpt-6-astra` nor
`gpt-5.6-sol`, report that mismatch as a selection prerequisite and do not claim
supported orchestration. If
metadata does not expose the model or effort, report the value as unobservable and
continue within the user's request without inventing confirmation.

The selected parent performs all planning, integration, verification, and acceptance.
Sol does not require an Astra call. `ASTRA` labels below are stable plugin status
identifiers, not evidence of the running model.

After capability preflight and before the first implementation or delegation task
call, record the selected plan:

~~~text
ASTRA ROUTE
parent: <observed model or unobservable> / <observed effort or unobservable>
delegation: <none or each selected model and effort>
risk: <concise, task-specific rationale>
~~~

The declaration is a record of the current decision, not a fixed set of workflow
lanes. Update it only when new evidence changes the plan, and explain that evidence.

## Dynamic delegation

Resolve the optional user roster from `CODEX_HOME` or `~/.codex` as described above
before selecting a worker. If it exists, validate and use it. If it is absent, validate
and use the packaged [worker roster](worker-roster.json). Validate schema_version 1, a
workers array, unique nonempty model IDs, nonempty provider fields, and boolean enabled
values. An invalid user roster disables delegation; do not fall back or merge files.
Select an enabled roster entry and an
effort for each concrete, bounded, independent deliverable from the task's risk,
context, available work, and the live schema. The roster is a policy allowlist, not an
engine access control list. Use a generic spawn tool exposed under the `agents` or
`collaboration` namespace only when the schema has explicit `model` and
`reasoning_effort` controls. Pass the chosen model and effort explicitly.

Include the bounded task and expected result in the message. Include a task name only
if the current tool accepts it. Select the fresh-context control from the live schema:
- Version 2: `fork_turns: "none"`.
- Version 1: `fork_context: false`, only if exposed by that tool.

The saved local transport setting is default; the selected model determines its
interface. Confirm the actual schema in each task; do not infer it from saved settings.
Use the current tool schema for any additional required fields and reject a request
whose selected controls cannot be enforced. Do not invent controls, use a nested CLI,
or send work through a direct API transport.

Do not rely on role names, predefined TOMLs, a role-to-model table, or a fixed count
cap. Dispatch only work whose files, interfaces, and acceptance evidence are clear;
keep useful planning, implementation, integration, or verification work in the
parent session while independent subagents run. Avoid assigning the same change or
check to both parent and subagent. Preserve concurrent edits and return each
subagent's actual result and evidence to the parent.

Inspect the current tool metadata when selecting and invoking a subagent. A changed
live capability list wins over the roster. If the selected enabled model, effort,
explicit spawn control, or required tool is unavailable, conflicting, or unobservable,
fail the affected delegation closed. Continue safe parent work when possible and
report the limitation; never silently substitute another model, effort, or tool.

## Evidence and review

The public spawn and thread metadata are authoritative for model and effort. Use
runtime introspection only to resolve a field that public metadata omitted, and report
the source of each value. Chosen values are not the same as runtime-confirmed values.

For substantial implementation, the parent first inspects the complete accumulated
diff and reruns the requested checks. It then starts a fresh read-only reviewer in a
new context. Select the reviewer from the enabled worker roster with an effort
supported by live metadata; it must receive the exact change set, interfaces,
constraints, and verification evidence. Ask it to return:

~~~text
ASTRA REVIEW
VERDICT: ship | fix-first | rethink
REASON: <evidence-based reason>
FINDINGS: <precise findings or none>
RESIDUAL RISK: <remaining risk or none>
~~~

Treat `ship` as the only accepting verdict for substantial implementation. On
`fix-first`, the parent makes the correction, reruns verification, and obtains a new
fresh review. On `rethink`, revise the plan before claiming completion. The reviewer
must not edit files or implement its own fixes. Capture actual sandbox and permission
metadata when the host exposes them; do not claim enforced read-only isolation unless
it was observed.

## ChatGPT app and cloud boundaries

Permitted Codex workers use only the exposed spawn interface. The roster may include
models routed by the existing Codex `openai` transport; do not use a nested CLI, API
key, or other direct inference transport. Separate app tasks require an explicit user
request.
For an explicit Codex app project task, `mcp__codex_app__create_thread` supports
`model` and `thinking`; call `mcp__codex_app__list_projects` first, use a worktree by
default when the selected project is a Git repository, and use local otherwise.
Follow any explicit starting-state request exactly.

ChatGPT Work cloud `create_thread` does not accept `model` or `thinking`; omit both.
Cloud work therefore cannot currently promise arbitrary model or effort control. Do
not dispatch an incompatible model-pinned request there by default, and do not use an
API key, nested CLI, or fabricated tool as a workaround. A future native work tool is
usable only once its schema exposes the required controls.

## Reporting

For each delegation and review, report the selected model/effort, the evidence source,
the bounded deliverable, and the actual result. Keep chosen-but-unconfirmed values
separate from runtime-confirmed values. A parent acceptance claim requires its own
diff inspection and requested checks; a subagent's assertion alone is insufficient.

## Automatic lifecycle updates

Emit these updates in the user's conversation, not only in an internal log. They
apply to each implementer and each fresh reviewer, including failed dispatches:

~~~text
ASTRA DELEGATE <name>
task: <bounded deliverable and owned files>
requested: <model> / <effort>
reason: <why this work warrants this selection>

ASTRA RESULT <name> / <agent ID or unavailable>
status: <completed, failed, interrupted, or blocked; actual evidence>
requested: <model> / <effort>
observed: <model or unobservable> / <effort or unobservable>
evidence: <runtime metadata source or unavailable>
~~~

Do not equate a successful dispatch with completed work. Keep a record of agent IDs,
requested settings, runtime observations, and result evidence.
If runtime settings are unavailable, say so. Finish with the verified result and
remaining limitations; no token accounting, price lookup, cost calculation, or cost
report is part of this workflow.
