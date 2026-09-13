# Astra Advisor — Astra + Sol modification

[Русское описание](#описание-на-русском) · [English description](#english-description)

## Описание на русском

Это модификация плагина [Astra Advisor](https://github.com/DannyMac180/astra-advisor),
который создал Daniel McAteer. В исходном варианте работой руководит GPT-6 Astra.
В этой версии основной моделью также может быть GPT-5.6 Sol.

Выбранная основная модель получает задачу и отвечает за план, итоговую проверку и
приёмку результата. Когда разделение работы полезно, она может назначить отдельную
ограниченную часть задачи одному из разрешённых подагентов: Sol, Terra или Luna.
Модель и уровень мышления выбираются по сложности этой части задачи и возможностям
текущего инструмента Codex.

Список разрешённых подагентов входит в плагин и работает на разных компьютерах без
личных путей владельца репозитория. Если пользовательский список настроен с ошибкой,
плагин останавливает делегирование и сообщает о проблеме. Плагин не собирает расход
токенов и не рассчитывает стоимость.

Репозиторий закрытый. Перед установкой владелец должен предоставить вашему аккаунту
GitHub доступ:

~~~sh
codex plugin marketplace add chaosmorale/astra-sol-advisor --ref main
codex plugin add astra-advisor@astra-advisor
~~~

После установки перезапустите приложение ChatGPT и начните новую задачу. Лицензия MIT
и ссылка на исходный проект сохранены.

## English description

**GPT-6 Astra or GPT-5.6 Sol can lead the work, choose useful bounded delegation,
and own verification and acceptance.**

This is a community modification of Daniel McAteer's original
[Astra Advisor](https://github.com/DannyMac180/astra-advisor). The modification adds
GPT-5.6 Sol as a supported primary model alongside GPT-6 Astra and keeps delegation
limited to an explicit Sol, Terra, and Luna roster.

The selected primary model receives the goal, constraints, and repository context. It
decides whether independent work should run alongside the parent session and chooses
an enabled worker and supported effort for each bounded deliverable.

## Origin and license

- Original repository: [DannyMac180/astra-advisor](https://github.com/DannyMac180/astra-advisor)
- Original developer: Daniel McAteer
- Sol modification: [chaosmorale/astra-sol-advisor](https://github.com/chaosmorale/astra-sol-advisor)
- License: MIT. The original copyright and license notice are retained in [LICENSE](LICENSE).

## Cloud limitation

ChatGPT Work cloud `create_thread` must omit `model` and `thinking`, so it cannot
currently promise arbitrary model or effort control. Permitted Codex workers are
usable where the current tool schema exposes the needed controls.

## Worker roster

The packaged [worker roster](plugins/astra-advisor/skills/orchestration/references/worker-roster.json)
enables GPT-5.6 Sol, Terra, and Luna by default. To override it for one installation,
create `allowed-workers.json` under `$CODEX_HOME/astra-advisor/`, or under
`~/.codex/astra-advisor/` when `CODEX_HOME` is not set. An invalid user roster stops
delegation instead of silently using the packaged default.

## Original developer

Daniel McAteer writes [Attention Heads](https://attentionheads.substack.com/) about AI,
cognition, and agentic engineering. [Subscribe](https://attentionheads.substack.com/subscribe?utm_source=github&utm_medium=readme&utm_campaign=astra-advisor)
to get new posts.

## Quick start

This repository is private. The GitHub account installing it must have access.

~~~sh
codex plugin marketplace add chaosmorale/astra-sol-advisor --ref main
codex plugin add astra-advisor@astra-advisor
~~~

Restart the ChatGPT desktop app and start a fresh task. Select GPT-6 Astra or GPT-5.6
Sol at any effort supported by the current Codex host, then invoke:

~~~text
Use $astra-advisor:orchestration to plan, build, verify, and review this work.
~~~

## How routing works

The selected Astra or Sol session remains the architect and acceptance owner at the
effort selected by the user. The skill never changes the parent model or effort.

When delegation helps, the parent uses an exposed generic spawn tool with an explicit
model, reasoning effort, and fresh-context control. It chooses an enabled worker from
the roster. Every subagent receives one bounded deliverable. No predefined role files,
role-to-model table, or fixed subagent count are included.

Live tool metadata is authoritative. A version 2 interface uses `fork_turns: none`. A
version 1 interface requires its exposed fresh-context control. Missing or conflicting
controls stop delegation instead of silently substituting a model or tool.

For substantial implementation, the parent inspects the complete change, reruns the
requested checks, and requests a fresh read-only review. Only a `ship` verdict accepts
the work. The plugin reports requested and runtime-observed model settings separately.

The plugin does not collect token usage or calculate costs.

## Verify

~~~sh
sh plugins/astra-advisor/scripts/verify.sh
~~~

## Updating

~~~sh
codex plugin marketplace upgrade astra-advisor
codex plugin add astra-advisor@astra-advisor
~~~

For local development:

~~~sh
cd /absolute/path/to/astra-sol-advisor
codex plugin marketplace add /absolute/path/to/astra-sol-advisor
codex plugin add astra-advisor@astra-advisor
~~~

For operational details, read
[the orchestration operations reference](plugins/astra-advisor/skills/orchestration/references/operations.md).
